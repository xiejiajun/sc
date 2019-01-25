# UNIX\_TIMESTAMP {#concept_xzs_wyq_dgb .concept}

本文为您介绍如何使用实时计算日期函数UNIX\_TIMESTAMP。

## 语法 {#section_dks_qgp_dgb .section}

```language-sql
BIGINT UNIX_TIMESTAMP()
BIGINT UNIX_TIMESTAMP(VARCHAR date)
BIGINT UNIX_TIMESTAMP(TIMESTAMP timestamp)
BIGINT UNIX_TIMESTAMP(VARCHAR date, VARCHAR format)
```

## 入参 {#section_eks_qgp_dgb .section}

|参数|数据类型|
|--|----|
|timestamp|TIMESTAMP|
|date|VARCHAR**说明：** 默认日期格式为`yyyy-MM-dd HH:mm:ss`。

|
|format|VARCHAR**说明：** 默认格式为`yyyy-MM-dd hh:mm:ss`。

|

## 功能描述 {#section_hks_qgp_dgb .section}

返回第一个参数date转换成的长整型的时间戳，单位为秒。无参数时返回当前时间的时间戳，单位为秒，与now语义相同。若有参数为null或解析错误，返回null。

## 示例 {#section_iks_qgp_dgb .section}

-   测试数据

    |nullstr（VARCHAR）|
    |----------------|
    |null|

-   测试语句

    ```language-sql
    SELECT UNIX_TIMESTAMP() as big1,
           UNIX_TIMESTAMP(nullstr) as big2
    FROM T1
    
    ```

-   测试结果

    |big1（BIGINT）|big2（BIGINT）|
    |------------|------------|
    |1403006911|null|


