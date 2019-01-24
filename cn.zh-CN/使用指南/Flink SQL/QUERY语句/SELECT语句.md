# SELECT语句 {#concept_62501_zh .concept}

SELECT语句用于从表中选取数据。

## 语法 {#section_ccz_zj4_cgb .section}

```language-sql
SELECT [ DISTINCT ]
{ * | projectItem [, projectItem ]* }
FROM tableExpression;

```

## 测试数据 {#section_imd_ck4_cgb .section}

|a（VARCHAR）|b（INT）|c（DATE）|
|----------|------|-------|
|a1|211|1990-02-20|
|b1|120|2018-05-12|
|c1|89|2010-06-14|
|a1|46|2016-04-05|

## 示例一 {#section_emt_ck4_cgb .section}

-   测试语句

    ```language-sql
    SELECT * FROM 表名；
    
    ```

-   测试结果

    |a（VARCHAR）|b（INT）|c（DATE）|
    |----------|------|-------|
    |a1|211|1990-02-20|
    |b1|120|2018-05-12|
    |c1|89|2010-06-14|
    |a1|46|2016-04-05|


## 示例二 {#section_qgc_bl4_cgb .section}

-   测试语句

    ```language-sql
    SELECT a, c AS d FROM 表名；
    
    ```

-   测试结果

    |a（VARCHAR）|d（DATE）|
    |----------|-------|
    |a1|1990-02-20|
    |b1|2018-05-12|
    |c1|2010-06-14|
    |a1|2016-04-05|


## 示例三 {#section_axp_bl4_cgb .section}

-   测试语句

    ```language-sql
    SELECT DISTINCT a FROM 表名；
    
    ```

-   测试结果

    |a（VARCHAR）|
    |----------|
    |a1|
    |b1|
    |c1|


## 子查询 {#section_fk5_yk4_cgb .section}

普通的SELECT是从几张表中读数据，如`SELECT column_1, column_2 … FROM table_name`，但查询的对象也可以是另外一个SELECT操作。

**说明：** 当查询的对象是另一个SELECT操作时，必须为子查询加别名。示例如下。

-   示例语句

    ```language-sql
    INSERT INTO result_table
    SELECT * FROM
                   (SELECT   t.a,
                             sum(t.b) AS sum_b
                    FROM     t1 t
                    GROUP BY t.a
                   ) t1 
    WHERE  t1.sum_b > 100;
    
    
    ```

-   示例结果

    |a（VARCHAR）|b（INT）|
    |----------|------|
    |a1|211|
    |b1|120|
    |a1|257|


