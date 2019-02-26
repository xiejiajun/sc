# VAR\_POP {#concept_cf1_x3x_dgb .concept}

本文为您介绍如何使用实时计算聚合函数STDDEV\_POP。Flink SQL中使用VAR\_POP函数返回指定表达式中所有值的总体统计方差。

## 语法 {#section_dks_qgp_dgb .section}

```
T VAR_POP(T value)

```

## 入参 {#section_eks_qgp_dgb .section}

|参数|数据类型|
|--|----|
|value|数值型，可为BIGINT, DOUBLE等类型。|

## 功能描述 {#section_hks_qgp_dgb .section}

返回所有输入值的方差。

## 示例 {#section_iks_qgp_dgb .section}

-   测试数据

    |a\(BIGINT\)|c\(VARCHAR\)|
    |-----------|------------|
    |2900|Hi|
    |2500|Hi|
    |2600|Hi|
    |3100|Hello|
    |11000|Hello|

-   测试语句

    ```language-sql
    SELECT 
    VAR_POP(a) as `result`,
     c
    FROM MyTable
    GROUP BY c
    
    ```

-   测试结果

    |result\(BIGINT\)|c|
    |----------------|--|
    |28889|Hi|
    |15602500|Hello|


