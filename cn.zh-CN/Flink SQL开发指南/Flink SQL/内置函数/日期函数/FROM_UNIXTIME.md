# FROM\_UNIXTIME {#concept_otj_bsq_dgb .concept}

本文为您介绍如何使用实时计算日期函数FROM\_UNIXTIME。

## 语法 {#section_dks_qgp_dgb .section}

```
VARCHAR FROM_UNIXTIME(BIGINT unixtime[, VARCHAR format])

```

## 入参 {#section_eks_qgp_dgb .section}

|参数|数据类型|
|--|----|
|unixtime|BIGINT|
|unixtime|VARCHAR|

## 功能描述 {#section_hks_qgp_dgb .section}

-   参数unixtime为长整型，是以秒为单位的时间戳，
-   参数format可选，为日期格式，默认为yyyy-MM-dd HH:mm:ss，返回VARCHAR类型的符合指定格式的日期，若有参数为null或解析错误，返回null。
-   返回值为VARCHAR类型的日期值, 默认日期格式：yyyy-MM-dd HH:mm:ss，若指定日期格式按指定格式输出 任一输入参数是NULL，返回NULL。

## 示例 {#section_iks_qgp_dgb .section}

-   测试数据

    |unixtime1\(BIGINT\)|nullstr\(VARCHAR\)|
    |-------------------|------------------|
    |1505404800|null|

-   测试语句

    ```language-sql
    SELECT FROM_UNIXTIME(unixtime1) as var1, 
     FROM_UNIXTIME(unixtime1,'MMdd-yyyy') as var2,
     FROM_UNIXTIME(unixtime1,nullstr) as var3
    FROM T1
    
    ```

-   测试结果

    |var1\(VARCHAR\)|var2\(VARCHAR\)|var3\(VARCHAR\)|
    |---------------|---------------|---------------|
    |2017-09-15 00:00:00|0915-2017|null|


