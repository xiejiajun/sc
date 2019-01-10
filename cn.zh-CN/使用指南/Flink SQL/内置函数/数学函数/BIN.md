# BIN {#concept_62739_zh .concept}

本文为您介绍如何使用实时计算数学函数BIN。

## 语法 {#section_dks_qgp_dgb .section}

```
VARCHAR BIN(BIGINT number)

```

## 入参 {#section_eks_qgp_dgb .section}

|参数|数据类型|
|--|----|
|number|BIGINT**说明：** 参数的隐式转换支持INT，不支持其他类型。

|

## 功能描述 {#section_hks_qgp_dgb .section}

将长整型参数转换为二进制形式，返回类型为字符串。

## 示例 {#section_iks_qgp_dgb .section}

-   测试数据

    |id\(INT\)|x\(BIGINT\)|
    |---------|-----------|
    |1|12L|
    |2|10L|
    |3|0L|
    |4|10000000000L|

    **说明：** 测试数据**x**列中的字母`L`代表的是数据类型**Long**，并不是做二进制转换的数值的一部分。

-   测试语句

    ```
    SELECT id, bin(x) as var1
    FROM T1
    
    ```

-   测试结果

    |id\(INT\)|var1\(VARCHAR\)|
    |---------|---------------|
    |1|1100|
    |2|1010|
    |3|0|
    |4|1001010100000010111110010000000000|


