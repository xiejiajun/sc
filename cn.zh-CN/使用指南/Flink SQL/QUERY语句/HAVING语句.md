# HAVING语句 {#concept_62504_zh .concept}

在使用聚合函数时，您需要添加HAVING语句，以实现与WHERE语句一样的过滤效果。

## 语法 {#section_i2d_vs4_cgb .section}

```language-sql
  SELECT [ ALL | DISTINCT ]{ * | projectItem [, projectItem ]* }
  FROM tableExpression
  [ WHERE booleanExpression ]
  [ GROUP BY { groupItem [, groupItem ]* } ]
  [ HAVING booleanExpression ];

```

## 示例 {#section_ay5_vs4_cgb .section}

-   测试数据

    |Customer|OrderPrice|
    |--------|----------|
    |Bush|1000|
    |Carter|1600|
    |Bush|700|
    |Bush|300|
    |Adams|2000|
    |Carter|100|

-   测试语句

    ```language-SQL
    SELECT Customer,SUM(OrderPrice) FROM XXX
    GROUP BY Customer
    HAVING SUM(OrderPrice)<2000;
    
    ```

-   测试结果

    |Customer|SUM（OrderPrice）|
    |--------|---------------|
    |Carter|1700|


