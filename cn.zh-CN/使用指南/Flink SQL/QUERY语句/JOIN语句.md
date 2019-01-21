# JOIN语句 {#concept_62505_zh .concept}

实时计算的JOIN和传统批处理JOIN的语义一致，都用于将两张表联系起来。区别的是实时计算联系的是两张动态表，联系的结果也会动态更新以保证最终结果和批处理结果保持一致。

## 语法 {#section_e5s_t54_cgb .section}

```language-sql
tableReference [, tableReference ]* | tableexpression
[ LEFT ] JOIN tableexpression [ joinCondition ];

```

**说明：** 

-   只支持等值连接，不支持不等连接。
-   只支持INNER JOIN和LEFT OUTER JOIN两种JOIN方式。

## 示例一 {#section_w4k_x54_cgb .section}

-   测试数据

    Orders：

    |rowtime|productId|orderId|units|
    |-------|---------|-------|-----|
    |10:17:00|30|5|4|
    |10:17:05|10|6|1|
    |10:18:05|20|7|2|
    |10:18:07|30|8|20|
    |11:02:00|10|9|6|
    |11:04:00|10|10|1|
    |11:09:30|40|11|12|
    |11:24:11|10|12|4|

    Products：

    |productId|name|unitPrice|
    |---------|----|---------|
    |30|Cheese|17|
    |10|Beer|0.25|
    |20|Wine|6|
    |30|Cheese|17|
    |10|Beer|0.25|
    |10|Beer|0.25|
    |40|Bread|100|
    |10|Beer|0.25|

-   测试语句

    ```language-sql
      SELECT o.rowtime, o.productId, o.orderId, o.units,p.name, p.unitPrice
      FROM Orders AS o
      JOIN Products AS p
      ON o.productId = p.productId;
    
    ```

-   测试结果

    |rowtime|productId|orderId|units|name|unitPrice|
    |-------|---------|-------|-----|----|---------|
    |10:17:00|30|5|4|Cheese|17|
    |10:17:05|10|6|1|Beer|0.25|
    |10:18:05|20|7|2|Wine|6|
    |10:18:07|30|8|20|Cheese|17|
    |11:02:00|10|9|6|Beer|0.25|
    |11:04:00|10|10|1|Beer|0.25|
    |11:09:30|40|11|12|Bread|100|
    |11:24:11|10|12|4|Beer|0.25|


## 示例二 {#section_zgj_gv4_cgb .section}

-   测试数据

    datahub\_stream1：

    |a\(bigint\)|b\(bigint\)|c\(VARCHAR\)|
    |-----------|-----------|------------|
    |0|10|test11|
    |1|10|test21|

    datahub\_stream2：

    |a\(bigint\)|b\(bigint\)|c\(VARCHAR\)|
    |-----------|-----------|------------|
    |0|10|test11|
    |1|10|test21|
    |0|10|test31|
    |1|10|test41|

-   测试语句

    ```language-SQL
    SELECT s1.c,s2.c 
    FROM datahub_stream1 AS s1
    JOIN datahub_stream2 AS s2 
    ON s1.a =s2.a
    WHERE s1.a = 0;
    
    ```

-   测试结果

    |s1\_c\(varchar\)|s2\_c\(varchar\)|
    |----------------|----------------|
    |test1|test11|
    |test1|test31|


