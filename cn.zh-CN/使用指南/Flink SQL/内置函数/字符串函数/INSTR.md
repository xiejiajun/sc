# INSTR {#concept_93126_zh .concept}

本文为您介绍如何使用实时计算字符串函数INSTR。

**说明：** INSTR函数仅支持实时计算2.2.0及以上版本。

## 语法 {#section_t53_4sq_dgb .section}

```language-sql
INT instr( string1, string2 ) 
INT instr( string1, string2 [, start_position [, nth_appearance ] ] )   

```

## 入参 {#INSTR_OG_01 .section}

|参数|数据类型|说明|
|--|----|--|
|string1|VARCHAR|源字符串|
|string2|VARCHAR|目标字符串|
|start\_position|INT|起始位置|
|nth\_appearance|INT|匹配序号|

## 功能描述 {#INSTR_OG_02 .section}

返回子字符串在源字符串中的位置，如果在源串中没有找到子串，则返回0。

## 示例 {#INSTR_OG_03 .section}

-   测试数据T1

    |string1\(VARCHAR\)|
    |------------------|
    |helloworld|

-   测试语句

    ```language-sql
    SELECT 
    instr('helloworld','lo') as res1,
    instr('helloworld','l',-1,1) as res2,   
    instr('helloworld','l',3,2) as res3
    FROM T1
    
    ```

-   测试结果

    |res1\(INT\)|res2\(INT\)|res3\(INT\)|
    |-----------|-----------|-----------|
    |4|9|4|


