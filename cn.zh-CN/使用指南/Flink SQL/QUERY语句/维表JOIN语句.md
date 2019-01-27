# 维表JOIN语句 {#concept_62506_zh .concept}

维表是一张不断变化的表，因此在JOIN维表的时候，需指明这条记录关联维表快照的时刻。目前维表JOIN仅支持对当前时刻维表快照的关联。（未来会支持关联左表rowtime所对应的维表快照。）

## 维表JOIN语法 {#section_syw_j1p_cgb .section}

```language-sql
SELECT column-names
FROM table1  [AS <alias1>]
[LEFT] JOIN table2 FOR SYSTEM_TIME AS OF PROCTIME() [AS <alias2>]
ON table1.column-name1 = table2.key-name1

```

例如，事件流JOIN白名单维表的SQL如下。

```language-sql
SELECT e.*, w.*
FROM event AS e
JOIN white_list FOR SYSTEM_TIME AS OF PROCTIME() AS w
ON e.id = w.id

```

**说明：** 

-   维表支持`INNER JOIN`和`LEFT JOIN`，不支持`RIGHT JOIN`或`FULL JOIN`。
-   必须加上`FOR SYSTEM_TIME AS OF PROCTIME()`，表示JOIN维表当前时刻所看到的每条数据。
-   JOIN行为只发生在处理时间（processing time），即使维表中的数据新增、更新或删除，已关联的数据也不会被改变或撤回。
-   ON条件必须包含维表PRIMARY KEY的等值条件（且要求与真实表定义一致），但除此之外，ON条件中也可以有其他等值条件。
-   维表和维表不能做JOIN。

## 示例 {#section_jrp_m1p_cgb .section}

-   测试数据

    nameinfo：

    |id（bigint）|name（varchar）|age（bigint）|
    |----------|-------------|-----------|
    |1|lilei|22|
    |2|hanmeimei|20|
    |3|libai|28|

    phoneNumber：

    |name（varchar）|phoneNumber（bigint）|
    |-------------|-------------------|
    |dufu|18867889855|
    |baijuyi|18867889856|
    |libai|18867889857|
    |lilei|18867889858|

-   测试语句

    ```language-sql
    CREATE TABLE datahub_input1 (
    id            BIGINT,
    name        VARCHAR,
    age           BIGINT
    ) WITH (
    type='datahub'
    );
    
    create table phoneNumber(
    name VARCHAR,
    phoneNumber bigint,
    primary key(name),
    PERIOD FOR SYSTEM_TIME
    )with(
    type='rds'
    );
    
    CREATE table result_infor(
    id bigint,
    phoneNumber bigint,
    name VARCHAR
    )with(
    type='rds'
    );
    
    INSERT INTO result_infor
    SELECT
    t.id,
    w.phoneNumber,
    t.name
    FROM datahub_input1 as t
    JOIN phoneNumber FOR SYSTEM_TIME AS OF PROCTIME() as w
    ON t.name = w.name;
    
    ```

-   测试结果

    |id（bigint）|phoneNumber（bigint）|name（varchar）|
    |----------|-------------------|-------------|
    |1|18867889858|lilei|
    |3|18867889857|libai|


