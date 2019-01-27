# EMIT语句 {#concept_ejj_ppy_zfb .concept}

**说明：** 支持实时计算`2.0`以上版本

## 策略 {#section_gwp_5py_zfb .section}

EMIT 策略是指在Flink SQL 中，query的输出策略（如能忍受的延迟）可能在不同的场景有不同的需求，而这部分需求，传统的 ANSI SQL 并没有对应的语法支持。比如用户需求：1小时的时间窗口，窗口触发之前希望每分钟都能看到最新的结果，窗口触发之后希望不丢失迟到一天内的数据。针对这类需求，抽象出了EMIT语法，并扩展到了SQL语法。

## 用途 {#section_qb2_xpy_zfb .section}

EMIT语法的用途目前总结起来主要提供了：控制延迟、数据精确性，两方面的功能。

1.  控制延迟。针对大窗口，设置窗口触发之前的EMIT输出频率，减少用户看到结果的延迟。
2.  数据精确性。不丢弃窗口触发之后的迟到的数据，修正输出结果。

在选择EMIT策略时，还需要与处理开销进行权衡。因为越低的输出延迟、越高的数据精确性，都会带来越高的计算开销。

## 语法 {#section_y5g_cqy_zfb .section}

EMIT 语法是用来定义输出的策略，即是定义在输出（INSERT INTO）上的动作。当未配置时，保持原有默认行为，即 window 只在 watermark 触发时 EMIT 一个结果。语法：

```
INSERTINTO tableName
query
EMIT strategy [, strategy]*

strategy ::= {WITH DELAY timeInterval | WITHOUT DELAY} 
                [BEFORE WATERMARK |AFTER WATERMARK]

timeInterval ::='string' timeUnit

```

-   `WITH DELAY`：声明能忍受的结果延迟，即按指定 interval 进行间隔输出。
-   `WITHOUT DELAY`：声明不忍受延迟，即每来一条数据就进行输出。
-   `BEFORE WATERMARK`：窗口结束之前的策略配置，即watermark 触发之前。
-   `AFTER WATERMARK`：窗口结束之后的策略配置，即watermark 触发之后。

注：

1.  其中 strategy可以定义多个，同时定义before和after的策略。 但不能同时定义两个 before 或 两个after 的策略。
2.  若配置了AFTER WATERMARK 策略，需要显式地配上 `blink.state.ttl.ms` 标识能忍受的最大迟到时间。

## 例子 {#section_pct_dqy_zfb .section}

```
-- 窗口结束之前，按1分钟延迟输出，窗口结束之后无延迟输出
EMIT 
  WITH DELAY '1'MINUTE BEFORE WATERMARK,
  WITHOUT DELAY AFTER WATERMARK

-- 窗口结束之前不输出，窗口结束之后无延迟输出
EMIT WITHOUT DELAY AFTER WATERMARK

-- 全局都按1分钟的延迟输出 (minibatch)
EMIT WITH DELAY '1'MINUTE-- 窗口结束之前按1分钟延迟输出
EMIT WITH DELAY '1'MINUTE BEFORE WATERMARK

```

-   最大容忍迟到时间

    当配置AFTER 的策略，即表示会接收迟到的数据，窗口的状态就会一直保留以等待迟到数据，那么会等待多久呢？这是一个用户能自定义的参数，即 `blink.state.ttl.ms` 状态的超时时间，默认未配置，如启用了 AFTER 策略，必须显式地配上 `blink.state.ttl.ms` 参数 。例如：`blink.state.ttl.ms=3600000` 最多容忍1小时的迟到数据，超过这个时间的数据会直接丢弃。

-   Example

    例如，我们有一个一小时的滚动窗口 `tumble_window` 。

    ```
    CREATE VIEW tumble_window AS
    SELECT 
      id,
      TUMBLE_START(rowtime, INTERVAL '1' HOUR) as start_time,
      COUNT(*) as cnt
    FROM source
    GROUP BY id, TUMBLE(rowtime, INTERVAL '1' HOUR)
    
    ```

    默认 `tumble_window` 的输出是需要等到一小时结束才能看到结果，我们希望能尽早能看到窗口的结果（即使是不完整的结果）。例如，我们希望每分钟看到最新的窗口结果：

    ```
    INSERT INTO result
    SELECT * FROM tumble_window
    EMIT WITH DELAY '1' MINUTE BEFORE WATERMARK -- 窗口结束之前，每隔1分钟输出一次更新结果
    
    ```

    另外，默认 `tumble_window` 会忽略并丢弃窗口结束后到达的数据，而这部分数据对我们来说很重要，希望能统计进最终的结果里。而且我们知道我们的迟到数据不会太多，且迟到时间不会超过一天以上，并且希望收到迟到的数据立刻就更新结果：

    ```
    INSERT INTO result
    SELECT * FROM tumble_window
    EMIT WITH DELAY '1' MINUTE BEFORE WATERMARK, 
         WITHOUT DELAY AFTER WATERMARK  -- 窗口结束后，每收到一条数据输出一次更新结果
    
    -- 增加一天的 state TTL 配置
    blink.state.ttl.ms = 86400000
    
    ```


## Delay 的概念 {#section_qnh_3qy_zfb .section}

本文的 Delay 指的是用户能忍受的延迟，该延迟是指用户的数据从进入实时计算统，到看到结果数据（出实时计算系统）的时间，不管 mode 是Event Time 还是Processing Time。延迟的计算都是基于系统时间的。动态表（流在实时计算内部的存储）中的任意一行记录发生了变化，到结果表（实时计算外部的存储）中能看到这行新记录的时间间隔，称为延迟。

假设实时计算系统的处理耗时是0，那么会产生延迟的地方有 minibatch 的攒批，window 的等待窗口数据。如果用户指定了最多延迟30秒，那这30秒可以用来给 minibatch 攒批。如果query 是一个一小时的窗口，那么最多延迟30秒的含义是，每隔30秒要 EMIT 变化的行，也就是window结果的trigger interval。

例如配置 `EMIT WITH DELAY '1' MINUTE` ，如果普通groupby 聚合，则会用1分钟做 minibatch 攒批。如果有window且window 的size大于1分钟，则window 会每隔1分钟发射一次，否则忽略这个配置，因为窗口依靠watermark 的输出就能保证这个latency SLA。

又例如配置 `EMIT WITHOUT DELAY` 。如果是group by，则不会启用minibatch，每来一条数据都触发计算和输出。如果是window，则也是每来一条数据都触发计算和输出。

## 目前状态和未来计划 {#section_bvx_3qy_zfb .section}

目前状态和未来计划·

1.  目前EMIT策略只支持TUMBLE，HOP窗口，暂不支持SESSION窗口。
2.  目前一个Job若有多个输出，多个输出的EMIT需要定义成一样的策略。未来会支持不一样的策略。
3.  目前EMIT 语法还不能用来配置minibatch的allowLateness，未来打算使用EMIT 策略来声明allowLateness。

