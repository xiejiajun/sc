# OVER窗口 {#concept_62514_zh .concept}

OVER窗口（OVER Window）是传统数据库的标准开窗，OVER Window不同于Group By Window，OVER Window中每1个元素都对应1个窗口。窗口元素是与当前元素相邻的元素集合，流上元素会在多个窗口中。在Flink SQL Window的实现中，每个触发计算的元素所确定的行，都是该元素所在窗口的最后一行。

在应用OVER Window的流式数据中，每1个元素都对应1个OVER Window。每1个元素都触发一次数据计算。在实时计算的底层实现中，OVER Window的数据进行全局统一管理（数据只存储一份），逻辑上为每1个元素维护1个OVER Window，为每1个元素进行窗口计算，完成计算后会清除过期的数据。

## 语法 {#section_s3c_jxv_cgb .section}

```language-sql
SELECT
    agg1(col1) OVER (definition1) AS colName,
    ...
    aggN(colN) OVER (definition1) AS colNameN
FROM Tab1

```

**说明：** 

-   agg1到aggN所对应的OVER definition1必须相同。
-   AS的别名可供外层SQL进行查询。

## 类型 {#section_fnt_jxv_cgb .section}

Flink SQL中对OVER Window的定义遵循标准SQL的定义语法，传统OVER Window没有对其进行更细粒度的窗口类型命名划分。本节为了方便您理解OVER Window的语义，将OVER Window按照计算行的定义方式划分为如下两类。

-   ROWS OVER Window：每一行元素都视为新的计算行，即每一行都是一个新的窗口。
-   RANGE OVER Window：具有相同时间值的所有元素行视为同一计算行，即具有相同时间值的所有行都是同一个窗口。

## 属性 {#section_upy_nxv_cgb .section}

|正交属性|proctime|eventtime|
|----|--------|---------|
|rows|√|√|
|range|√|√|

-   rows：按照实际元素的行进行确定窗口。
-   range：按照实际的元素值（时间戳值）进行确定窗口。

## Rows OVER Window语义 {#section_kd5_2yv_cgb .section}

-   窗口数据

    ROWS OVER Window的每个元素都确定一个窗口。ROWS OVER Window也有Unbounded和Bounded的两种情况。

    Unbounded ROWS OVER Window数据如下图所示。

    ![UnBounded ROWS OVER Window](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40915/154806178834339_zh-CN.png)

    **说明：** 上图所示窗口user1的w7和w8，user2的窗口w3和w4，虽然元素都是同一时刻到达，但是它们仍然是在不同的窗口，这一点有别于RANGE OVER Window。

    Bounded ROWS OVER Window数据以3个元素（2 PRECEDING）的窗口为例，如下图所示。

    ![Bounded ROWS OVER Window](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40915/154806178834340_zh-CN.png)

    **说明：** 上图所示窗口user1的w5和w6，user2的窗口w2和w3，虽然有元素都是同一时刻到达，但是它们仍然是在不同的窗口，这一点有别于RANGE OVER Window。

-   窗口语法

    ```language-sql
    SELECT 
        agg1(col1) OVER(
         [PARTITION BY (value_expression1,..., value_expressionN)] 
         ORDER BY timeCol
         ROWS 
         BETWEEN (UNBOUNDED | rowCount) PRECEDING AND CURRENT ROW) AS colName, ... 
    FROM Tab1
    
    ```

    -   value\_expression：分区值表达式。
    -   timeCol：用于元素排序的时间字段。
    -   rowCount：是定义根据当前行开始向前追溯几行元素。
-   案例

    以Bounded ROWS OVER Window场景示例，假设，有一张商品上架表，包含有商品ID、商品类型、商品上架时间、商品价格数据。假设，求在当前商品上架之前同类的3个商品中的最高价格。

     **测试数据** 

    |商品ID|商品类型|上架时间|销售价格|
    |----|----|----|----|
    |ITEM001|Electronic|2017-11-11 10:01:00|20|
    |ITEM002|Electronic|2017-11-11 10:02:00|50|
    |ITEM003|Electronic|2017-11-11 10:03:00|30|
    |ITEM004|Electronic|2017-11-11 10:03:00|60|
    |ITEM005|Electronic|2017-11-11 10:05:00|40|
    |ITEM006|Electronic|2017-11-11 10:06:00|20|
    |ITEM007|Electronic|2017-11-11 10:07:00|70|
    |ITEM008|Clothes|2017-11-11 10:08:00|20|

     **测试代码** 

    ```language-sql
    CREATE TABLE tmall_item(
       itemID VARCHAR,
       itemType VARCHAR,
       onSellTime TIMESTAMP,
       price DOUBLE,
       WATERMARK onSellTime FOR onSellTime as withOffset(onSellTime, 0) 
    ) 
    WITH (
      type = 'sls',
       ...
    ) ;
    
    SELECT  
        itemID,
        itemType, 
        onSellTime, 
        price,  
        MAX(price) OVER (
            PARTITION BY itemType 
            ORDER BY onSellTime 
            ROWS BETWEEN 2 preceding AND CURRENT ROW) AS maxPrice
      FROM tmall_item
    
    ```

     **测试结果** 

    |itemID|itemType|onSellTime|price|maxPrice|
    |------|--------|----------|-----|--------|
    |ITEM001|Electronic|2017-11-11 10:01:00|20|20|
    |ITEM002|Electronic|2017-11-11 10:02:00|50|50|
    |ITEM003|Electronic|2017-11-11 10:03:00|30|50|
    |ITEM004|Electronic|2017-11-11 10:03:00|60|60|
    |ITEM005|Electronic|2017-11-11 10:05:00|40|60|
    |ITEM006|Electronic|2017-11-11 10:06:00|20|60|
    |ITEM007|Electronic|2017-11-11 10:07:00|70|70|
    |ITEM008|Clothes|2017-11-11 10:08:00|20|20|


## RANGE OVER Window语义 {#section_o3z_syv_cgb .section}

-   窗口数据

    RANGE OVER Window所有具有共同元素值（元素时间戳）的元素行确定一个窗口，RANGE OVER Window也有Unbounded和Bounded的两种情况。

     **Unbounded RANGE OVER Window** 数据如下图所示。

    ![UnBounded RANGE OVER Window](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40915/154806178834341_zh-CN.png)

    > 注意: 上图所示窗口user1的w7， user2的窗口w3 ，两个元素同一时刻到达，他们属于相同的window，这一点有别于ROWS OVER Window。

     **Bounded RANGE OVER Window** 数据，以3秒中数据`(INTERVAL '2' SECOND)`的窗口为例，如下图所示。

    ![Bounded RANGE OVER Window](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40915/154806178834342_zh-CN.png)

    **说明：** 上图所示窗口user1的w6， user2的窗口w3，元素都是同一时刻到达，他们属于相同的window，这一点有别于ROWS OVER Window。

-   窗口语法

    ```language-sql
    SELECT 
        agg1(col1) OVER(
         [PARTITION BY (value_expression1,..., value_expressionN)] 
         ORDER BY timeCol
         RANGE 
         BETWEEN (UNBOUNDED | timeInterval) PRECEDING AND CURRENT ROW) AS colName, 
    ... 
    FROM Tab1
    
    ```

    -   value\_expression：进行分区的字表达式。
    -   timeCol：用于元素排序的时间字段。
    -   timeInterval：是定义根据当前行开始向前追溯指定时间的元素行。
-   案例

    以Bounded RANGE OVER Window场景示例，假设有一张商品上架表，包含有商品ID、商品类型、商品上架时间、商品价格数据。假设求比当前商品上架时间早2分钟的同类商品中的最高价格。

     **测试数据** 

    |商品ID|商品类型|上架时间|销售价格|
    |----|----|----|----|
    |ITEM001|Electronic|2017-11-11 10:01:00|20|
    |ITEM002|Electronic|2017-11-11 10:02:00|50|
    |ITEM003|Electronic|2017-11-11 10:03:00|30|
    |ITEM004|Electronic|2017-11-11 10:03:00|60|
    |ITEM005|Electronic|2017-11-11 10:05:00|40|
    |ITEM006|Electronic|2017-11-11 10:06:00|20|
    |ITEM007|Electronic|2017-11-11 10:07:00|70|
    |ITEM008|Clothes|2017-11-11 10:08:00|20|

     **测试代码** 

    ```language-sql
    CREATE TABLE tmall_item(
       itemID VARCHAR,
       itemType VARCHAR,
       onSellTime TIMESTAMP,
       price DOUBLE,
       WATERMARK onSellTime FOR onSellTime as withOffset(onSellTime, 0) 
    ) 
    WITH (
      type = 'sls',
       ...
    ) ;
    
    SELECT  
        itemID,
        itemType, 
        onSellTime, 
        price,  
        MAX(price) OVER (
            PARTITION BY itemType 
            ORDER BY onSellTime 
            RANGE BETWEEN INTERVAL '2' MINUTE preceding AND CURRENT ROW) AS maxPrice
      FROM tmall_item
    
    
    ```

     **测试结果** 

    |itemID|itemType|onSellTime|price|maxPrice|
    |------|--------|----------|-----|--------|
    |ITEM001|Electronic|2017-11-11 10:01:00|20|20|
    |ITEM002|Electronic|2017-11-11 10:02:00|50|50|
    |ITEM003|Electronic|2017-11-11 10:03:00|30|50|
    |ITEM004|Electronic|2017-11-11 10:03:00|60|60|
    |ITEM005|Electronic|2017-11-11 10:05:00|40|60|
    |ITEM006|Electronic|2017-11-11 10:06:00|20|40|
    |ITEM007|Electronic|2017-11-11 10:07:00|70|70|
    |ITEM008|Clothes|2017-11-11 10:08:00|20|20|


