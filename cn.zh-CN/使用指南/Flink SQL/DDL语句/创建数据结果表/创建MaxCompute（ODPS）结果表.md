# 创建MaxCompute（ODPS）结果表 {#concept_pql_cdz_lgb .concept}

本文为您介绍如何创建MaxCompute（ODPS）结果表以及创建过程中的常见问题。

## DDL定义 {#section_jnb_3qf_cgb .section}

实时计算支持使用ODPS 作为结果输出。示例代码如下：

```language-sql
create table odps_output(
    id int,
    user_name VARCHAR,
    content VARCHAR
) with (
    type = 'odps',
    endPoint = 'xxx',
    project = 'xxx',
    tableName = 'xx',
    accessId = 'xx',
    accessKey = 'xxx',
    `partition` = 'xx'
);

```

## WITH参数 {#section_glb_mqf_cgb .section}

|参数|注释说明|备注|
|--|----|--|
|endPoint| |必选。参见[配置Endpoint](../../../../../cn.zh-CN/准备工作/配置Endpoint.md#)。|
|project| |必选|
|tableName|表名|必选|
|accessId| |必选|
|accessKey| |必选|
|partition|写入分区|可选，分区表必填，具体分区信息到[数据地图](https://meta.dw.alibaba-inc.com/store/index.html)查看。例如: 一个表的分区信息为`ds=20180905`，则可以写 ``partition` = 'ds=20180905'`。多级分区之间用逗号分隔，示例: ``partition` = 'ds=20180912,dt=xxxyyy'`|

**说明：** 

实时计算数据写入odps的方式是每次做checkPoint的时候将缓存数据进行输入。

## 常见问题 {#section_lsc_fgz_lgb .section}

1.  Q: Stream模式的ODPS Sink是否支持isOverwrite为true的情况？

    isOverwrite为true，写入sink之前会把结果表或者结果数据清空。具体来说，就是每次启动后和暂停恢复后，写入之前会把原来结果表或者结果分区里的内容删除掉。流上暂停恢复后清空数据会导致丢数据。

2.  Q: MaxCompute中的数据类型和Blink中数据类型的对应关系是什么？

    |MaxCompute|Blink|
    |----------|-----|
    |TINYINT|TINYINT|
    |SMALLINT|SMALLINT|
    |INT|INT|
    |BIGINT|BIGINT|
    |FLOAT|FLOAT|
    |DOUBLE|DOUBLE|
    |BOOLEAN|BOOLEAN|
    |DATETIME|TIMESTAMP|
    |TIMESTAMP|TIMESTAMP|
    |VARCHAR|VARCHAR|
    |STRING|STRING|
    |DECIMAL|DECIMAL|
    |BINARY|VARBINARY|

    **说明：** 其他MaxCompute类型，ODPS connector暂时还没有支持转换。


