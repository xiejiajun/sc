# 创建数据总线（DataHub）源表 {#concept_62522_zh .concept}

本文为您介绍如何为实时计算创建数据总线（DataHub）源表以及创建过程涉及到的属性字段、WITH参数和类型映射。

## 什么是数据总线 {#section_ptb_bvy_bgb .section}

DataHub作为一个流式数据总线，为阿里云数加平台提供了大数据的入口服务，共同构建一站式的数据处理平台。实时计算通常使用DataHub作为流式数据存储头和输出目的端。同时，上游众多流式数据，包括DTS、IoT等均选择DataHub作为大数据平台的数据入口。 DataHub本身是流数据存储，实时计算只能将其作为流式数据输入。

## 示例 {#section_adm_zym_cgb .section}

DataHub可以作为实时计算的数据输入，示例如下:

```language-sql
create table datahub_stream(
  name varchar,
  age BIGINT,
  birthday BIGINT
) with (
  type='datahub',
  endPoint='xxxxxxxxx',
  project='xxxxxxxxxx',
  topic='xxxxxxx',
  accessId='xXXXXXXXX',
  accessKey='XXXXXXXXX',
  startTime='2017-07-21 00:00:00'
); 

```

## 属性字段 {#section_yyk_rwy_bgb .section}

Flink SQL支持获取DataHub的属性字段。能够记录每条信息写入DataHub的系统时间。

如图所示：

![12421](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/62522/cn_zh/1522727303925/1122.png)

|字段名|注释说明|
|---|----|
|timestamp|每条记录入datahub的systemtime|

**属性字段使用说明**

为了获取这些属性字段，除了按照正常逻辑声明外，还需要在类型声明后面加上`HEADER`关键字以示区分。

例如，属性字段并不存在于DataHub的字段声明里。想获取每条记录入DataHub的`systemtime`，可以将`timestamp`作为字段名，在后面加上`HEADER`就可以取出想要的属性值。示例如下：

-   测试数据

    |name\(VARCHAT\)|MsgID\(VARCHAT\)|
    |---------------|----------------|
    |ens\_altar\_flow|ems0a|

-   测试语句

    ```language-sql
    CREATE TABLE datahub_log (
      `timestamp`  varchar HEADER,
      name       varchar，
      MsgID        varchar
    )
    WITH
    (
      type ='datahub'
      
    );
    
    CREATE TABLE RDS_out (
      `timestamp`     varchar,
      MsgID    varchar,
      name  varchar 
    )
    WITH
    (
      type ='RDS'
      
    );
    
    INSERT INTO RDS_out
    SELECT 
    `timestamp`,
    MsgID,
    name
    FROM datahub_log;
    
    ```

-   测试结果

    |TIME\(VARCHAT\)|MsgID\(VARCHAT\)|name\(VARCHAT\)|
    |---------------|----------------|---------------|
    |1522652455625|ems0a|ens\_altar\_flow|


## WITH参数 {#section_dr2_4wy_bgb .section}

目前只支持tuple模式的topic。

|参数|注释说明|备注|
|--|----|--|
|endPoint|消费端点信息|参看[DataHub域名列表](https://help.aliyun.com/document_detail/47442.html?spm=5176.doc47439.6.542.w2TEz3) |
|accessId|读取的accessId|无|
|accessKey|读取的密钥|无|
|project|读取的项目|无|
|topic|project下的具体的topic|无|
|startTime|启动位点的时间|格式为`yyyy-MM-dd hh:mm:ss`|
|maxRetryTimes|读取最大尝试次数|可选，默认为20。|
|retryIntervalMs|重试间隔|可选，默认为1000。|
|batchReadSize|单次读取条数|可选，默认为10，可设置的最大值为1000。|
|lengthCheck|单行字段条数检查策略|可选，默认为SKIP,其它可选值为EXCEPTION、PAD。-   SKIP：字段数目不符合时跳过 。
-   EXCEPTION:字段数目不符合时抛出异常。
-   PAD:按顺序填充，不存在的置为null。

|
|columnErrorDebug|是否打开调试开关。如果选择打开，会把解析异常的log打印出来|可选，默认为false。|

## 类型映射 {#section_cwm_twy_bgb .section}

DataHub和实时计算字段类型对应关系如下，建议使用该对应关系时进行DDL声明:

|DataHub字段类型|实时计算字段类型|
|-----------|--------|
|bigint|bigint|
|string|varchar|
|double|double|
|timestamp|bigint|
|boolean|boolean|
|decimal|decimal|

**说明：** DataHub的TIMESTAMP是精确到微妙级别的，在Unix时间戳里是16位的。而实时计算定义的TIMESTAMP是精确到毫秒级别的，在Unix时间戳里是13位的所以建议大家使用BIGINT来映射。如果一定是要用TIMESTAMP建议使用[计算列](cn.zh-CN/使用指南/Flink SQL/DDL语句/创建数据源表/数据源表概述.md#section_zxh_jhy_bgb)来做转换。

