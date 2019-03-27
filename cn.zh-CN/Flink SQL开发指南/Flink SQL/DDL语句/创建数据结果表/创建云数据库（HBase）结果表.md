# 创建云数据库（HBase）结果表 {#concept_91464_zh .concept}

本文为您介绍如何创建实时计算云数据库（HBase）结果表。

**说明：** 本文档只适用于独享模式。

## DDL定义 {#section_l2m_q1g_cgb .section}

实时计算支持使用HBase作为结果输出。示例代码如下。

```
create table liuxd_user_behavior_test_front (
    row_key varchar,
    from_topic varchar,
    origin_data varchar,
    record_create_time varchar,
    PRIMARY KEY (row_key)
) with (
    type = 'cloudhbase',
    zkQuorum = '2  columnFamily = '*******',
    tableName = '*******',
    batchSize = '500'
)

```

**说明：** 

-   `primary key`支持定义多个字段。多个字段会按照`rowkeyDelimiter`（默认为`:`）拼接起来作为`row_key`。
-   HBase做撤回删除操作时，如果column定义了多版本，会把所有版本的值清空。

## WITH参数 {#section_zdt_m1g_cgb .section}

|参数|注释说明|备注|
|--|----|--|
|zkQuorum|HBase集群配置的zk地址|可以在hbase-site.xml文件中找到hbase.zookeeper.quorum相关配置|
|zkNodeParent|集群配置在zk上的路径|可以在hbase-site.xml文件中找到hbase.zookeeper.quorum相关配置|
|tableName|HBase表名|无|
|userName|用户名|无|
|password|密码|无|
|partitionBy|设置为true之后会在用joinKey做partition，将数据分发到join节点，提高缓存命中率。|可选，默认为false|
|shuffleEmptyKey|设置为true之后遇到空key会随机往下游做shuffle，否则往0号下游发。|建议打开|
|columnFamily|列族名|目前只支持插入同一列族|
|maxRetryTimes|最大尝试次数|可选，默认为10|
|bufferSize|流入多少条数据后进行去重|默认为5000|
|batchSize|一次批量写入的条数|可选，默认为100|
|flushIntervalMs|最长插入时间|可选，默认为2000|
|writePkValue|是否写入主键值|可选，默认为false|
|stringWriteMod|是否都按照string插入|可选，默认为false|
|rowkeyDelimiter|rowKey的分隔符|可选，默认为`:`|
|isDynamicTable|是否为动态表|可选，默认为false|

**说明：** 建议batchsize参数值设置在200到300之间。过大的batchsize值可能导致任务OOM报错。

