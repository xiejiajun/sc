# DISTINCT {#concept_mz3_bzx_ngb .concept}

`DISTINCT`用于`SELECT`语句中，表示对查询的结果的去重后进行输出。

## DISTINCT语法 {#section_pmr_p1y_ngb .section}

```sql
SELECT DISTINCT expressions 
FROM tables
 ...

```

-   `DISTINCT`必须放到开始位置。
-   `expressions`是一个或多个expression，可以是具体的column，也可以是function等任何合法表达式。

## DISTINCT示例 {#section_dcr_1by_ngb .section}

-   SQL语句

    Flink SQL中`DISTINCT`的示例如下：

    ```sql
    CREATE TABLE distinct_tab_source(
        FirstName  VARCHAR,
        LastName VARCHAR
    )WITH(
       type='random'
    ) ;
    CREATE TABLE distinct_tab_sink(
        FirstName  VARCHAR,
        LastName VARCHAR
    )WITH(
        type = 'print'
    ) ;
    INSERT INTO distinct_tab_sink 
    SELECT DISTINCT FirstName, LastName -- 以FirstName和LastName两个列进行去重
    FROM distinct_tab_source;
    ```

-   测试数据

    |FristName|LastName|
    |---------|--------|
    |SUNS|HENGRAN|
    |SUN|JINCHENG|
    |SUN|SHENGRAN|
    |SUN|SHENGRAN|

-   查询结果

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/117137/154822256937912_zh-CN.png)

    **说明：** 

    -   测试数据有4条记录。`DISTINCT FirstName, LastName` 进行去重后将重复数据 `SUN,SHENGRAN`去重输出3条结果记录。
    -   `SUNS,HENGRAN` 和 `SUN,SHENGRAN` 两条记录并没有被去重，说明`DISTINCT FirstName, LastName` 是对两个字段分别处理的，而不是拼接到一起再进行去重。
-   DISTINCT的等效写法

    在SQL中利用`GROUP BY`语句也可以到达和`DISTINCT`类似的去重效果。`GROUP BY`语法如下：

    ```sql
    SELECT expressions 
    FROM tables
    GROUP BY expressions 
    ;
    
    ```

    通过多路输出的方式编写的和DISTINCT示例等效的SQL如下：

    ```sql
    CREATE TABLE distinct_tab_source(
        FirstName  VARCHAR,
        LastName VARCHAR
    )WITH(
       type='random'
    ) ;
    CREATE TABLE distinct_tab_sink(
        FirstName  VARCHAR,
        LastName VARCHAR
    )WITH(
        type = 'print'
    ) ;
    CREATE TABLE distinct_tab_sink2(
        FirstName  VARCHAR,
        LastName VARCHAR
    )WITH(
        type = 'print'
    ) ;
    INSERT INTO distinct_tab_sink 
        SELECT DISTINCT FirstName, LastName -- 以FirstName和LastName两个列进行去重
            FROM distinct_tab_source;
    INSERT INTO distinct_tab_sink2 
        SELECT FirstName, LastName
            FROM distinct_tab_source
             GROUP BY FirstName, LastName; -- 以FirstName和LastName两个列进行去重
    
    ```

    使用相同的测试数据产生的输出结果如下，和DISTINCT示例的输出结果保持一致。说明两种方式的语义一致。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/117137/154822256937913_zh-CN.png)


## DISTINCT在聚合函数COUNT中的作用 {#section_wqt_mhy_ngb .section}

`DISTINCT`能使聚合函数`COUNT`统计去重后的计数。

```sql
COUNT(DISTINCT expression)
```

**说明：** `expression`目前只支持一个表达式。

## COUNT DISTINCT语法示例 {#section_ahp_nvy_ngb .section}

-   SQL语法

    ```sql
    CREATE TABLE distinct_tab_source(
        FirstName  VARCHAR,
        LastName VARCHAR
    )WITH(
       type='random'
    ) ;
    CREATE TABLE distinct_tab_sink(
        cnt BIGINT  ,
        distinct_cnt BIGINT
    )WITH(
        type = 'print'
    ) ;
    INSERT INTO distinct_tab_sink 
        SELECT 
          COUNT(FirstName), -- 不去重
          COUNT(DISTINCT FirstName)-- 按FirstName去重
        FROM distinct_tab_source;
    
    ```

-   测试数据

    |FristName|LastName|
    |---------|--------|
    |SUNS|HENGRAN|
    |SUN|JINCHENG|
    |SUN|SHENGRAN|
    |SUN|SHENGRAN|

-   测试结果

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/117137/154822256937914_zh-CN.png)


