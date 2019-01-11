# JSON\_TUPLE {#concept_bcs_pvr_dgb .concept}

本文为您介绍如何使用实时计算表值函数JSON\_TUPLE。

## 语法 {#section_dks_qgp_dgb .section}

```
JSON_TUPLE(str, path1, path2 ..., pathN)

```

## 入参 {#section_eks_qgp_dgb .section}

|参数|数据类型|说明|
|--|----|--|
|str|VARCHAR|JSON字符串|
|path1～pathN|VARCHAR|表示路径的字符串，前面不需要`$.`。|

## 功能描述 {#section_hks_qgp_dgb .section}

从JSON字符串中取出各路径字符串所表示的值。

## 示例 {#section_iks_qgp_dgb .section}

-   测试数据

    |d\(VARCHAR\)|s\(VARCHAR\)|
    |------------|------------|
    |\{“qwe”:”asd”,”qwe2”:”asd2”,”qwe3”:”asd3”\}|qwe3|
    |\{“qwe”:”asd4”,”qwe2”:”asd5”,”qwe3”:”asd3”\}|qwe2|

-   测试语句

    ```language-sql
    SELECT d, v 
    FROM T1, lateral table(JSON_TUPLE(d, 'qwe', s))
     as T(v)
    
    ```

-   测试结果

    |d\(VARCHAR\)|v\(VARCHAR\)|
    |------------|------------|
    |\{“qwe”:”asd”,”qwe2”:”asd2”,”qwe3”:”asd3”\}|asd|
    |\{“qwe”:”asd”,”qwe2”:”asd2”,”qwe3”:”asd3”\}|asd3|
    |\{“qwe”:”asd4”,”qwe2”:”asd5”,”qwe3”:”asd3”\}|asd4|
    |\{“qwe”:”asd4”,”qwe2”:”asd5”,”qwe3”:”asd3”\}|asd5|


