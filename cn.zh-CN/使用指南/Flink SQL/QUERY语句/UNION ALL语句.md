# UNION ALL语句 {#concept_62507_zh .concept}

UNION ALL语句将两个流式数据合并。要求两个流式数据的字段完全一致，包括字段类型和字段顺序。

## 语法 {#section_sgg_czs_cgb .section}

```language-sql
select_statement
UNION ALL
select_statement;

```

**说明：** 实时计算同样支持`UNION`函数。`UNION ALL`允许重复值，`UNION`不允许重复值。在实时计算底层，`UNION`是`UNION ALL` + `Distinct` ，运行效率较低，一般不推荐使用`UNION`。

## 示例 {#section_qgk_hzs_cgb .section}

-   测试数据

    test\_source\_union1：

    |a（varchar）|b（bigint）|c（bigint）|
    |----------|---------|---------|
    |test1|1|10|

    test\_source\_union2：

    |a（varchar）|b（bigint）|c（bigint）|
    |----------|---------|---------|
    |test1|1|10|
    |test2|2|20|

    test\_source\_union3：

    |a（varchar）|b（bigint）|c（bigint）|
    |----------|---------|---------|
    |test1|1|10|
    |test2|2|20|
    |test1|1|10|

-   测试语句

    ```language-SQL
    SELECT
        a,
        sum(b),
        sum(c)
    FROM 
        (SELECT * from test_source_union1
        UNION ALL
        SELECT * from test_source_union2
        UNION ALL
        SELECT * from test_source_union3
        )t
     GROUP BY a;
    
    ```

-   测试结果

    |d（varchar）|e（bigint）|f（bigint）|
    |----------|---------|---------|
    |test1|1|10|
    |test2|2|20|
    |test1|2|20|
    |test1|3|30|
    |test2|4|40|
    |test1|4|40|


