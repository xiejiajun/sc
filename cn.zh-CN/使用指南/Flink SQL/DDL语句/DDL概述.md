# DDL概述 {#concept_62515_zh .concept}

本文为您介绍实时计算DDL语法以及在DDL使用过程中需要的注意的字段映射和大小写敏感问题。

## 语法 {#section_stc_bhx_bgb .section}

```language-sql
CREATE TABLE tableName
      (columnName dataType [, columnName dataType ]*)
      [ WITH (propertyName=propertyValue [, propertyName=propertyValue ]*) ];

```

## 说明 { .section}

阿里云实时计算本身不带有数据存储功能，因此所有涉及表创建DDL的操作，实际上均是对于外部数据表、存储的引用声明，如下。

```language-sql
create table mq_stream(
 a varchar,
 b varchar,
 c varchar
) with (
 type='mq',
 topic='blink_mq_test',
 accessId='xxxxxx',
 accessKey='xxxxxx'
);


```

此处并非是在Flink SQL中创建一个MQ的Topic，而是声明了一个名称为`mq_stream`的表引用。下游所有对这张Topic相关的DML操作均可以使用`mq_stream`别名来进行操作。

-   实时计算对于声明表的作用域是当前作业）一个SQL文件提交后生成一个实时计算作业），即上述有关`mq_stream`的声明仅在当前SQL有效。在同一个Project下的其他SQL文件同样可以声明名称为`mq_stream`的表。
-   按照SQL标准定义，DDL语法中关键字、表名、列名等不区分大小写。
-   表名、列名必须以字母或者数字开头，并且名称中只能包含字母、数字、下划线。
-   DDL声明不完全根据名称进行映射（取决于上游插件的性质）。如果插件支持根据key取值，则不要求字段个数完全一致，但名称需要一致。如果上游插件不支持根据key取值，则对字段数量和顺序有严格要求。建议您引用声明的字段名称、个数和外部表一致，避免出现定义混乱导致数据错乱的情况。

## 字段映射 { .section}

根据外部数据源是否有Schema分为两大类别。

-   顺序映射

    适用于以MQ为代表的不带有Schema系统。这类系统通常是非结构化存储系统。推荐的操作是，您可以在DDL SQL声明中对**字段名称**进行自定义，但需按照外部表字段的类型、列数进行对齐。

    以MQ为例，MQ的一条记录格式：

    ```
    asavfa,sddd32,sdfdsv
    
    ```

    示例，MQ的字段名按照命名规范来设置。

    ```language-sql
    create table mq_stream(
     a varchar,
     b varchar,
     c varchar
    ) with (
     type='mq',
     topic='blink_mq_test',
     accessId='XXXXXX',
     accessKey='XXXXXX'
    );
    
    
    ```

-   名称映射

    适用于带有Schema的系统。这类系统在表存储级别定义了字段名称以及字段类型。建议您在Flink SQL中严格按照外部数据存储Schema进行定义，包括名称、列数以及字段的顺序。

    **说明：** 如果外部数据存储的字段名称是大小写敏感类型（例如OTS），则在Flink SQL中需要在区分大小写的字段名称处使用`｀｀`进行转换。在DDL语法中，声明字段名和目标表的字段名需要名称完全一致。

    Datahub定义的Schema如下：

    |字段名|类型|
    |---|--|
    |name|STRING|
    |age|BIGINT|
    |value|STRING|

    **说明：** DataHub中的STRING数据类型对应的是在实时计中的VARCHAR类型。算

    我们推荐用户将所有列进行声明引用，注意可以少字段，不可以多字段。针对上述DataHub声明的DDL如下：

    ```language-sql
    
    create  table stream_result (
        `name` varchar,  
        age bigint,
        `value` varchar  
      ) with (
          type='datahub',
          endpoint='http://dh-cn-hangzhou.aliyuncs.com',
          accessId='xxxxxx',
          accessKey='xxxxxx',
          project='project',
          topic='topic'
      );
    
    ```


## 处理大小写敏感 {#section_n3j_ckx_bgb .section}

SQL标准定义中，大小写是不敏感的，例如：

```language-sql
create table stream_result (
    name varchar,
    value varchar
);

```

和下列语句含义一致：

```language-sql
create table STREAM_RESULT (
    NAME varchar,
    VALUE varchar
);

```

但实时计算引用的大量外部数据源，可能要求大小写敏感。例如，TableStore对于大小写是敏感的。如果在TableStore定义了一个`NAME`的大写字段，我们应该如下定义：

```language-sql
create  table STREAM_RESULT (
    `NAME` varchar,
    `VALUE` varchar
);

```

在之后所有的DML操作中，对于这个字段引用均需要添加反引号，如下：

```language-sql
INSERT INTO xxx
SELECT
  `NAME`,
  `VALUE`
FROM
  XXX;

```

