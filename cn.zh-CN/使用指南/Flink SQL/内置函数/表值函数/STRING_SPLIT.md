# STRING\_SPLIT {#concept_tsw_kwr_dgb .concept}

本文为您介绍如何使用实时计算表值函数STRING\_SPLIT。

## 语法 {#section_dks_qgp_dgb .section}

```language-sql
string_split(varchar string , varchar separator)

```

## 入参 {#section_eks_qgp_dgb .section}

|参数|数据类型|
|--|----|
|string|varchar|
|separator|varchar|

## 功能描述 {#section_hks_qgp_dgb .section}

将字符串string按照separator分隔符分割成多个部分。

## 示例 {#section_iks_qgp_dgb .section}

-   测试数据

    |d\(varchar\)|s\(varchar\)|
    |------------|------------|
    |abc-bcd|-|
    |hhh|-|

-   测试语句

    ```language-sql
    select d, v 
    from T1, 
    lateral table(string_split(d, s)) as T(v)
    
    ```

-   测试结果

    |d\(varchar\)|v\(varchar\)|
    |------------|------------|
    |abc-bcd|abc|
    |abc-bcd|bcd|
    |hhh|hhh|

    **说明：** 分隔符separator暂不支持多字符串形式，只支持单个字符串形式。


