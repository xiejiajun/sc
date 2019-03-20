# 创建消息队列（MQ）源表 {#concept_62523_zh .concept}

本文为您介绍如何为实时计算创建消息队列（MQ）源表以及创建过程涉及到的CSV类格式、WITH参数和类型映射。

## 什么是消息队列MQ {#section_ezv_dkz_bgb .section}

消息队列（Message Queue），简称MQ，是阿里云专业消息中间件，是企业级互联网架构的核心产品。实时计算可以将消息队列作为流式数据输入，示例如下。

## 示例 {#section_h3x_mkz_bgb .section}

```language-sql
create table metaq_stream(
 x varchar,
 y varchar,
 z varchar
) with (
 type='mq',
 topic='******',
 endpoint='******',
 pullIntervalMs='******',
 accessId='******',
 accessKey='******',
 startMessageOffset='******',
 consumerGroup='******',
 fieldDelimiter='******'
);


```

**说明：** MQ实际上是一个非结构化存储格式，对于数据的Schema不提供强制定义，完全由业务层指定。目前实时计算支持类CSV格式文本和二进制格式。

## CSV类格式 {#section_h3m_bsg_chb .section}

假设您的1条CSV格式消息记录如下。

```
1,name,male 
2,name,female
```

**说明：** 1条MQ消息可以包括0条到多条数据记录，记录之间使用`\n`分隔。

在实时计算作业中，声明MQ数据源表的DDL如下。

```language-sql
create table metaq_stream(
 id varchar,
 name varchar,
 gender varchar
) with (
 type='mq',
 topic='******',
 endpoint='******',
 pullIntervalMs='******',
 accessId='******',
 accessKey='******',
 startMessageOffset='******',
 consumerGroup='******',
 fieldDelimiter='******'
);


```

## 二进制格式 {#section_xw5_5qg_chb .section}

二进制格式测试代码如下。

```language-sql

create table source_table (
  mess varbinary
) with (
  type = 'mq',
  endpoint = '******',
  pullIntervalMs='500',
  accessId='******',
  accessKey='******',
  topic = '******',
  consumerGroup='******'
);

create table out_table (
  commodity varchar
)with(
  type='print'
);

INSERT INTO out_table
SELECT 
  cast(mess as varchar)
FROM source_table

```

**说明：** 

-   `cast(mess as varbinary)`需在实时计算2.0及以上版本使用，若版本低于2.0，请先升级。
-   VARBINARY只能入参一次。

## WITH参数 {#section_nnh_4kz_bgb .section}

|参数|注释说明|备注|
|--|----|--|
|topic|topic名称|无|
|endPoint|endPoint地址| -   公共云内网接入（阿里云经典网络/VPC）：华东1、华东2、华北1、华北2、华南1、香港的区域endpoint的地址是：`onsaddr-internal.aliyun.com:8080` 
-   公共云公网接入地址是：`http://onsaddr-internet.aliyun.com/rocketmq/nsaddr4client-internet`

 |
|accessId|accessId|无|
|accessKey|accessKey|无|
|consumerGroup|订阅消费group名称|无|
|pullIntervalMs|拉取时间间隔|单位为毫秒|
|startTime|消息消费启动的时间点|可选|
|startMessageOffset|消息开始的偏移量|可选，如果填写，将优先以startMessageoffset的位点开始加载。|
|tag|订阅的标签|可选|
|lineDelimiter|解析block时行分隔符|可选，默认为 `\n`|
|fieldDelimiter|字段分隔符|可选，默认为`\u0001`表示`Crtl+A`和 `\001`|
|encoding|编码格式|可选，默认为 `utf-8`|
|lengthCheck|单行字段条数检查策略|可选，默认为SKIP，其它可选值为EXCEPTION和PAD。-   SKIP：字段数目不符合时跳过 。
-   EXCEPTION：字段数目不符合时抛出异常。
-   PAD：按顺序填充，不存在的置为null。

|
|columnErrorDebug|是否打开调试开关|可选，默认为false。如果设置为ture，则打印解析异常的log。|

## 类型映射 {#section_hkx_4kz_bgb .section}

|MQ字段类型|实时计算字段类型|
|------|--------|
|STRING|VARCHAR|

