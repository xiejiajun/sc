# 高性能Flink SQL优化技巧 {#concept_xdd_nbz_zfb .concept}

本文主要介绍有助于大幅提升性能的Flink SQL推荐写法、推荐配置、推荐函数。

## Group Aggregate优化技巧 {#section_cwh_rcz_zfb .section}

-   开启MicroBatch/MiniBatch （提升吞吐）

    MicroBatch和MiniBatch都是微批处理，只是微批的触发机制上略有不同。原理上都是缓存一定的数据后再触发处理，以减少对state的访问从而显著提升吞吐，以及减少输出数据量。

    MiniBatch主要依靠在每个task上注册的timer线程来触发微批，会有一定的线程调度开销。MicroBatch 是 MiniBatch 的升级版，主要基于事件消息来触发微批，事件消息会按用户指定的时间间隔在源头插入。MicroBatch 在攒批效率、反压表现、吞吐和延迟性能上都要胜于MiniBatch。

    -   适用场景

        微批处理是使用一定的延迟来换取大量吞吐的策略，如果您有超低延迟的要求的话，不建议开启微批处理。微批处理一般对于聚合的场景都有显著的性能提升，所以建议开启。

        **说明：** MicroBatch模式也能解决之前一直困扰用户的两级Agg数据抖动问题。

    -   开启方式

        MicroBatch/MiniBatch默认关闭，开启方式：

        ```
        # 攒批的间隔时间，使用 microbatch 策略时需要加上该配置，且建议和 blink.miniBatch.allowLatencyMs 保持一致
        blink.microBatch.allowLatencyMs=5000
        # 使用 microbatch 时需要保留以下两个 minibatch 配置
        blink.miniBatch.allowLatencyMs=5000
        # 防止OOM，每个批次最多缓存多少条数据
        blink.miniBatch.size=20000
        ```

-   开启LocalGlobal（解决常见数据热点问题）

    LocalGlobal优化即将原先的Aggregate分成Local+Global 两阶段聚合，也就是在 MapReduce模型中熟知的Combine+Reduce 处理模式。第一阶段在上游节点本地攒一批数据进行聚合（localAgg），并输出这次微批的增量值（Accumulator），第二阶段再将收到的Accumulator merge起来，得到最终的结果（globalAgg）。

    LocalGlobal本质上能够靠localAgg聚合掉倾斜的数据，从而降低globalAgg的热点，从而提升性能。LocalGlobal如何解决数据倾斜问题可以结合下图理解。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/75347/154814958933642_zh-CN.png)

    -   适用场景

        LocalGlobal 适用于提升如SUM、COUNT, MAX, MIN, AVG等普通agg上的性能，以及解决这些场景下的数据热点问题。

        **说明：** 开启LocalGlobal需要UDAF实现merge方法。

    -   开启方式

        在 实时计算2.0 版本开始，LocalGlobal是默认开启的，参数是: `blink.localAgg.enabled=true`，但是需要在microbatch/minibatch开启的前提下才能生效。

    -   如何判断是否生效

        观察最终生成的拓扑图的节点名字中是否包含**GlobalGroupAggregate**或**LocalGroupAggregate**

-   开启PartialFinal（解决count\_distinct热点）

    上述的LocalGlobal优化能针对常见普通agg有较好的效果（如SUM、COUNT、MAX、MIN和AVG）。但是对于count distinct收效不明显，原因是count distinct在local 聚合时，对于distinct key的去重率不高，导致在global节点仍然存在热点。

    在旧版本用户为了解决count distinct 的热点问题时，一般会手动改写成两层聚合（增加按distinct key 取模的打散层），自 2.2.0版本开始，实时计算提供了count distinct自动打散，我们称之为PartialFinal优化，用户无需自己改写成两层聚合。和 LocalGlobal 的原理对比请结合下图理解。![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/75347/154814958933643_zh-CN.png)

    -   适用场景

        使用count distinct且aggregate节点性能无法满足时。

        **说明：** 

        -   PartialFinal优化方法不能在包含UDAF的Flink SQL中使用。
        -   数据量不大的情况下不建议使用PartialFinal优化方法。PartialFinal优化会自动打散成两层聚合，引入额外的网络shuffle，在数据量不大的情况下，可能反而会浪费资源。
    -   开启方式

        默认不开启，使用参数显式开启`blink.partialAgg.enabled=true`

    -   如何判断是否生效

        观察最终生成的拓扑图的节点名中是否包含**Expand**节点，或者原来一层的aggregate变成了两层的aggregate。

-   改写成 agg with filter 语法（提升大量count distinct场景性能）

    **说明：** 仅支持实时计算2.2.2 及以上版本。

    有一些统计作业会计算各种维度的UV，比如全网UV、来自手淘的UV、来自PC的UV等等。很多用户都是用Case When来实现这种多维度统计的功能，但是建议使用更标准的agg with filter语法，因为实时计算目前的SQL优化器能分析出filter 参数，从而同一个字段上计算不同条件下的count distinct能共享state，减少对state的读写操作。性能测试中有一倍的性能提升。

    -   适用场景

        我们建议用户将 agg with CASE WHEN 的语法都替换成 agg with filter 的语法，尤其是对同一个字段上计算不同条件下的 count distinct 结果时有极大的性能提升。

    -   原始写法

        ```
        COUNT(distinct visitor_id)as UV1,COUNT(distinctcasewhen is_wireless='y'then visitor_id elsenullend)as UV2
        
        ```

    -   优化写法

        ```
        COUNT(distinct visitor_id)as UV1,COUNT(distinct visitor_id) filter (where is_wireless='y')as UV2
        ```


## TopN 优化技巧 {#section_ncg_d3z_zfb .section}

当TopN的输入是非更新流（如source），TopN 只有一种算法AppendRank。当TopN的输入是更新流时（如经过了Agg/Join计算），TopN 有3种算法，性能从快到慢分别是：UpdateFastRank \>\> UnaryUpdateRank \>\> RetractRank。算法名字会显示在拓扑图的节点名字上。其中：

-   RetractRank 是保底算法，性能最差，不建议使用该算法上生产。如果是该算法，请检查下文第一点，看是否能优化。
-   UpdateFastRank 是最优算法，其有两个条件：1.输入流有PK信息。2.排序字段的更新是单调的，且单调方向与排序方向相反。如 order by count/count\_distinct/sum（正数） desc。
-   UnaryUpdateRank是仅次于UpdateFastRank 的算法。其有一个条件：输入流有 PK 信息。不需要单调信息。如order by avg。

-   order by sum（正数）desc 时，要加上正数的过滤条件

    且已知 sum 的参数不可能有负数，那么需要加上过滤条件从而告诉优化器这个信息，才能优化出 UpdateFastRank 算法（仅支持实时计算2.2.2及以上版本）。如下所示（注意 sum\(total\_fee\) filter ... ）

    ```
    SELECT cate_id, seller_id, stat_date, pay_ord_amt  # 不输出 rownum 字段，能减小对结果表的输出量
    FROM(SELECT*
          ROW_NUMBER ()OVER(PARTITIONBY cate_id, stat_date   # 注意要有时间字段，否则state过期会导致数据错乱
    ORDERBY pay_ord_amt DESC## 根据上游 sum 结果排序)AS rownum
      FROM(SELECT cate_id, seller_id, stat_date,# 重点！声明 sum 的参数都是正数，所以 sum 的结果是单调递增的，所以 TopN 能用优化算法，只存前 N 个数sum(total_fee) filter (where total_fee >=0)as pay_ord_amt
        FROMWHERE total_fee >=0GROUPBY cate_name, seller_id, stat_date)WHERE rownum <=100))
    ```

-   无排名优化

    TopN的输出不要带上 rownum，最终前端显式时在做一次排序，这样能极大地减少往结果表输出的数据量。 详细请参看[TopN语句](https://help.aliyun.com/document_detail/62508.html)。

-   增加 TopN 的 cache 大小

    TopN 为了提升性能有一个state cache层，cache层能提升对 state 的访问效率。topn 的 cache 命中率的计算公式为：

    ```
    
    cache_hit = cache_size*parallelism/top_n/partition_key_num
    
    ```

    例如 Top100，配置缓存10000条，并发50，当用户的patitionBy的key维度较大时，如10万级别，那么cache 命中率只有10000\*50/100/100000=5%，命中率会很低，导致大量的请求都会击中state（磁盘），观察state seek metric 可能会有很多毛刺。性能会大幅下降。因此当partitionKey维度特别大时，可以适当加大 topn 的 cache size，相对应的也建议适当加大topn节点的heap memory（参见[手动配置调优](cn.zh-CN/使用指南/配置调优/手动配置调优.md#)）。

    ```
    默认10000条，调整topn cahce到20万，那么理论命中率能达 200000*50/100/100000 = 100%
    blink.topn.cache.size=200000
    ```

-   partitionBy的字段中要有时间类字段

    比如每天的排名，要带上day字段。否则TopN的结果到最后会由于state ttl有错乱。


## 高效去重方案 {#section_yjb_nkz_zfb .section}

-   使用FirstRow语法替换 first\_value 函数

    **说明：** 仅支持实时计算2.2.2及以上版本。

    FirstRow的作用是去重，且只保留该主键下第一条出现的数据，之后出现的数据会被丢弃掉。因为其state中只存储了key数据，所以替换first\_value函数后一般能有一倍的性能提升。

    **说明：** FirstRow和first\_value的区别：FirstRow 是作用在一整行上的，是取该key下收到的第一行数据，无论行中的字段是否为null。first\_value 是作用在字段上的，是取该key下该字段第一个非null的数据。

    -   原始写法（使用first\_value去重）：

        ```
        select biz_order_id, first_value(seller_id), first_value(buyer_id), first_value(total_fee)from tt_source
        groupby biz_order_id;
        ```

    -   优化写法（使用FirstRow语法）： 需要给源表增加PRIMARY KEY属性，并加上`fetchFirstRow='true'`的配置。

        ```
        CREATETABLE tt_source (
        biz_order_id varchar,
        seller_id varchar,
        buyer_id varchar,
        total_fee doublePRIMARYKEY(biz_order_id # 1. 声明主键，可以是联合主键，即根据什么主键去重
        )WITH(
        type='tt',fetchFirstRow='true' # 2. 声明成只保留首行，默认为false，即保留末行...)
        ```

-   使用LastRow语法替换last\_value 函数

    LastRow的作用是也是去重，且只保留该主键下最后一条出现的数据。其性能略胜于使用 last\_value 函数。

    **说明：** LastRow 和last\_value的区别：LastRow 是作用在一整行上的，是取该key下收到的最后一行数据，无论行中的字段是否为null。last\_value 是作用在字段上的，是取该key下该字段最后一个非null的数据。

    -   原始写法（使用last\_value去重）：

        ```
        select biz_order_id,
         last_value(seller_id),
         last_value(buyer_id),
         last_value(total_fee)
        from tt_source
        group by biz_order_id;
        
        ```

    -   优化写法（使用 LastRow 语法）： 需要给源表增加 PRIMARY KEY属性，并加上 fetchFirstRow='false' 的配置。

        ```
        CREATETABLE tt_source (
        biz_order_id varchar,
        seller_id varchar,
        buyer_id varchar,
        total_fee doublePRIMARYKEY(biz_order_id)# 1. 声明主键，可以是联合主键，即根据什么主键去重
        )WITH(type='tt',
        fetchFirstRow='false',# 2. 声明成保留末行，默认为 false，即保留末行...)
        ```


## 高效的内置函数 {#section_qqb_clz_zfb .section}

-   使用内置函数替换自定义函数

    请尽量使用内置函数。 重要的事情说三遍。在老版本时，由于内置函数不齐全，很多用户都用的三方包的自定义函数。在实时计算2.x 中，我们对内置函数做了很多的优化（主要是节省了序列化/反序列化、以及直接对 bytes 进行操作），但是自定义函数无法享受到这些优化。

-   KEY VALUE 函数使用单字符的分隔符

    KEY VALUE 的签名是：KEYVALUE\(content, keyValueSplit, keySplit, keyName\)，当keyValueSplit和KeySplit是单字符时，如`:`,`,`会使用优化的算法，会在二进制数据上直接寻找所需的keyName 的值，而不会将整个 content做切分。性能约有30%提升。

-   多KEYVALUE场景使用MULTI\_KEYVALUE

    **说明：** 仅支持实时计算-2.2.2及以上版本

    如果在query中有对同一个content 做大量KEYVALUE 的操作，比如content中包含10个key-value对，希望把10个value 的值都取出来作为字段。用户经常会写10个KEY VALUE函数，那么就会对content 做10次解析。在这种场景建议使用 [MULTI\_KEYVALUE](cn.zh-CN/使用指南/Flink SQL/内置函数/表值函数/MULTI_KEYVALUE.md#)，这是一个表值函数。使用该函数可以只对content 做一次 split 解析。性能约有 50%~100%的性能提升。

-   LIKE 操作注意事项
    -   如果想做startWith的操作，用`LIKE 'xxx%'`。
    -   如果想做endWith的操作，用`LIKE '%xxx'`。
    -   如果想做contains的操作，用`LIKE '%xxx%'`。
    -   如果是做equals操作，用`LIKE 'xxx'`，其实和`str = 'xxx'`等价。
    -   如果想匹配`_`字符，请注意要做转义 `LIKE '%seller/id%' ESCAPE '/'`。因为 `_` 在 SQL 中是个单字符的通配符，能匹配上任何字符，如果声明成`LIKE '%seller_id%'`，那么 不单单会匹配 `seller_id` 还会匹配`seller#id`, `sellerxid`, `seller1id` 等等，可能会导致结果不是预期的，而且在运行时会使用正则来匹配，效率就会特别慢。
-   慎用正则函数（REGEXP）

    正则表达式是非常耗时的操作，对比加减乘除通常有百倍的性能开销，而且正则表达式在某些极端情况下[可能会进入无限循环](https://stackoverflow.com/questions/4500507/infinite-loop-in-regex-in-java)，导致作业卡住。所以尽量用 LIKE。见上文第四点说明。正则函数包括：[REGEXP](cn.zh-CN/使用指南/Flink SQL/内置函数/字符串函数/REGEXP.md#), [REGEXP\_EXTRACT](cn.zh-CN/使用指南/Flink SQL/内置函数/字符串函数/REGEXP_EXTRACT.md#), [REGEXP\_REPLACE](cn.zh-CN/使用指南/Flink SQL/内置函数/字符串函数/REGEXP_REPLACE.md#)。


## 网络传输的优化 {#section_hy1_gmz_zfb .section}

目前常见的Partitioner 策略包括：

-   KeyGroup/Hash：根据指定的key分配。
-   Rebalance：轮询分配给各个channel。
-   Dynamic-Rebalance：根据下游负载情况动态选择分配给负载较低的channel。
-   Forward：未chain一起时，同Rebalance。chain一起时是一对一分配。
-   Rescale：上游与下游一对多或多对一。

-   使用Dynamic-Rebalance替代Rebalance

    Dynamic Rebalance，它可以根据当前各subpartition中堆积的buffer的数量，选择负载较轻的subpartition进行写入，从而实现动态的负载均衡。相比于静态的rebalance策略，在下游各任务计算能力不均衡时，可以使各任务相对负载更加均衡，从而提高整个作业的性能。例如，在使用rebalance时，发现下游各个并发负载不均衡时，可以考虑使用 Dynamic-Rebalance。参数：`task.dynamic.rebalance.enabled=true`， 默认关闭。

-   使用Rescale替代Rebalance

    **说明：** 仅支持实时计算2.2.2及以上版本。

    例如上游是5个并发，下游是10个并发。当使用Rebalance时，上游每个并发会轮询发给下游10个并发。当使用Rescale时，上游每个并发只需轮询发给下游2个并发。因为 channel个数变少了，subpartition的buffer填充速度能变快，能提高网络效率。当上游的数据比较均匀时，且上下游的并发数成比例时，可以使用Rescale替换成 Rebalance。参数：`enable.rescale.shuffling=true`，默认关闭。


## 推荐的优化配置方案 {#section_rfj_vmz_zfb .section}

综上所述，作业建议使用如下的推荐配置：

```
# excatly-once语义
blink.checkpoint.mode=EXACTLY_ONCE
# checkpoint间隔时间，单位毫秒
blink.checkpoint.interval.ms=180000
blink.checkpoint.timeout.ms=600000
# 2.x使用niagara作为statebackend，以及设定state数据生命周期，单位毫秒
state.backend.type=niagara
state.backend.niagara.ttl.ms=129600000
# 2.x开启5秒的microbatch（使用窗口函数不能设置该参数）
blink.microBatch.allowLatencyMs=5000
# 表示整个job允许的延迟
blink.miniBatch.allowLatencyMs=5000
# 单个batch的size
blink.miniBatch.size=20000
# local 优化，2.x默认已经开启，1.6.4需手动开启
blink.localAgg.enabled=true
# 2.x开启partial优化，解决count distinct热点
blink.partialAgg.enabled=true
# union all 优化
blink.forbid.unionall.as.breakpoint.in.subsection.optimization=true
# object reuse 优化，默认已开启
#blink.object.reuse=true
# GC 优化（SLS做源表不能设置该参数）
blink.job.option=-yD heartbeat.timeout=180000 -yD env.java.opts='-verbose:gc -XX:NewRatio=3 -XX:+PrintGCDetails -XX:+PrintGCDateStamps -XX:ParallelGCThreads=4'
# 时区设置
blink.job.timeZone=Asia/Shanghai
```

