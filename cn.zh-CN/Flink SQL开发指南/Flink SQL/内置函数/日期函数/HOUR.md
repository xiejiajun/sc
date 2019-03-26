# HOUR {#concept_dt2_5tq_dgb .concept}

本文为您介绍如何使用实时计算日期函数HOUR。

## 语法 {#section_dks_qgp_dgb .section}

```
BIGINT HOUR(TIME time)
BIGINT HOUR(TIMESTAMP timestamp)

```

## 入参 {#section_eks_qgp_dgb .section}

|参数|数据类型|
|--|----|
|time|TIME|
|timestamp|TIMESTAMP|

## 功能描述 {#section_hks_qgp_dgb .section}

返回输入时间参数time或timestamp中的24小时制的小时数，范围0～23。

## 示例 {#section_iks_qgp_dgb .section}

-   测试数据

    |datetime1\(VARCHAR\)|time1\(VARCHAR\)|time2\(TIME\)|timestamp1\(TIMESTAMP\)|
    |--------------------|----------------|-------------|-----------------------|
    |2017-10-15 11:12:13|22:23:24|22:23:24|2017-10-15 11:12:13|

-   测试语句

    ```language-sql
    SELECT HOUR(TIMESTAMP '2016-09-20 23:33:33') AS int1,
     HOUR(TIME '23:30:33') AS int2,
     HOUR(time2) AS int3,
     HOUR(timestamp1) AS int4,
     HOUR(CAST(time1 AS TIME)) AS int5,
     HOUR(TO_TIMESTAMP(datetime1)) AS int6
    FROM T1
    
    ```

-   测试结果

    |int1\(BIGINT\)|int2\(BIGINT\)|int3\(BIGINT\)|int4\(BIGINT\)|int5\(BIGINT\)|int6\(BIGINT\)|
    |--------------|--------------|--------------|--------------|--------------|--------------|
    |23|23|22|11|22|11|


