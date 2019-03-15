# 创建消息队列（MQ）结果表 {#concept_62528_zh .concept}

本文为您介绍如何创建实时计算消息队列（MQ）结果表。

## 什么是消息列队（MQ） {#section_jnb_3qf_cgb .section}

消息队列（Message Queue）简称MQ，是阿里云商用的专业消息中间件，是企业级互联网架构的核心产品。消息列队是基于高可用分布式集群技术，搭建了包括发布订阅、消息轨迹、资源统计、定时（延时）、监控报警等一套完整的消息云服务。 实时计算可以将消息队列作为流式数据输出，如下所示。

```language-sql
CREATE TABLE stream_test_hotline_agent (
id INTEGER,
len BIGINT,
content varchar
) WITH (
type='mq',
endpoint='xxxxxx',
accessID='xxxxxx',
accessKey='xxxxxx',
topic='xxxxxx',
producerGroup='xxxxxx',
tag='xxxxxx',
encoding='utf-8',
fieldDelimiter=',',
retryTimes='5',
sleepTimeMs='500'
);

```

## WITH参数 {#section_glb_mqf_cgb .section}

|参数|注释说明|备注|
|--|----|--|
|topic|写入的Message Queue队列名|无|
|endpoint|地址| -   公共云内网接入（阿里云经典网络/VPC）：华东1、华东2、华北1、华北2、华南1香港的区域endpoint的地址是：`onsaddr-internal.aliyun.com:8080` 。
-   公共云公网接入地址是：`http://onsaddr-internet.aliyun.com/rocketmq/nsaddr4client-internet`。

 |
|accessID|填写自己的ID|无　|
|accessKey|填写自己的Key|无|
|producerGroup|写入的群组|无|
|tag|写入的标签|可选，默认为空。|
|fieldDelimiter|字段分割符|可选，默认为`\u0001` 。表示 `Crtl+A` 和 `\001`，\(暂不支持`\001`写法）。|
|encoding|编码|可选，默认为`utf-8`。|
|retryTimes|写入重试次数|可选，默认为10。|
|sleepTimeMs|重试间隔时间|可选，默认为1000（毫秒）。|

