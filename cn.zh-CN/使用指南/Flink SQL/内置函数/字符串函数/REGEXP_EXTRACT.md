# REGEXP\_EXTRACT {#concept_62546_zh .concept}

本文为您介绍如何使用实时计算字符串函数REGEXP\_EXTRACT。

## 语法 {#section_khf_cpp_dgb .section}

```language-sql
VARCHAR REGEXP_EXTRACT(VARCHAR str, VARCHAR pattern, INT index)

```

## 入参 {#REGEXP_EXTRACT_01 .section}

|参数|数据类型|说明|
|--|----|--|
|str|VARCHAR|指定的字符串。|
|pattern|VARCHAR|匹配的字符串。|
|index|INT|第几个被匹配的字符串。|

**说明：** 正则常量请按照Java代码来写。codegen会将SQL常量字符串自动转化成Java代码。如果要描述一个数字\(\\d\)，需要写成 '\\d'，也就是像在Java中写正则一样。

## 功能描述 {#REGEXP_EXTRACT_02 .section}

使用正则模式pattern匹配抽取字符串str中的第index个子串，index从1开始，正则匹配提取。参数为null或者正则不合法返回null。

## 示例 {#REGEXP_EXTRACT_03 .section}

-   测试数据

    |str1 \(VARCHAR\)|pattern1\(VARCHAR\)|index1 \(INT\)|
    |----------------|-------------------|--------------|
    |foothebar|foo\(.\*?\)\(bar\)|2|
    |100-200|\(\\\\d+\)-\(\\\\d+\)|1|
    |null|foo\(.\*?\)\(bar\)|2|
    |foothebar|null|2|
    |foothebar|空|2|
    |foothebar|\(|2|

-   测试语句

    ```language-sql
    SELECT  REGEXP_EXTRACT(str1, pattern1, index1) as result
    FROM T1
    
    ```

-   测试结果

    |result\(VARCHAR\)|
    |-----------------|
    |bar|
    |100|
    |null|
    |null|
    |null|
    |null|


