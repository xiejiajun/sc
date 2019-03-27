# 创建分析型数据库（AnalyticDB）结果表 {#concept_62527_zh .concept}

本文为您介绍如何创建实时计算分析型数据库（AnalyticDB）结果表以及分析型数据库和实时计算字段类型之间的映射关系。

## 什么是分析型数据库（AnalyticDB） {#section_pzl_l42_cgb .section}

分析型数据库（AnalyticDB）是阿里巴巴自主研发的海量数据实时高并发在线分析（Realtime OLAP）云计算服务，使得您可以在毫秒级时单位时间内，对千亿级数据进行即时的多维分析透视和业务探索。

## DDL定义 {#section_zvg_442_cgb .section}

实时计算支持使用AnalyticDB作为结果输出。示例代码如下。

```language-sql
CREATE TABLE stream_test_hotline_agent (
id INTEGER,
len BIGINT,
content VARCHAR，
PRIMARY KEY(id)
) WITH (
type='ads',
url='******',
tableName='******',
userName='******',
password='******',
batchSize='******'
);

```

**说明：** 

-   建议使用存储注册功能，参见[注册分析型数据库（AnalyticDB）](cn.zh-CN/Flink SQL开发指南/数据存储/注册数据存储/注册分析型数据库（AnalyticDB）.md#)。
-   实时计算AnalyticDB声明中的主键`primary key`要和AnalyticDB数据库里的主键一致，大小写敏感。不一致会导致数组索引越界的异常现象。

## WITH参数 {#section_qy1_p42_cgb .section}

|参数|注释说明|备注|
|--|----|--|
|url|jdbc连接地址|AnalyticDB数据库地址 。示例：`url ='jdbc:mysql://databaseName****-cn-shenzhen-a.ads.aliyuncs.com:10014/databaseName'`。**说明：** 

-   AnalyticDB数据库连接信息请参见[连接信息](cn.zh-CN/Flink SQL开发指南/数据存储/注册数据存储/注册分析型数据库（AnalyticDB）.md#ol_q5b_s5w_dhb)。
-   AnalyticDB数据库名称（databaseName）即AnalyticDB实例名称。

|
|tableName|表名|无|
|username|账号|无|
|password|密码|无|
|maxRetryTimes|写入重试次数|可选，默认为10|
|bufferSize|去重后的buffer大小|可选，默认为5000，表示输入的数据达到5000条就开始输出。|
|batchSize|一次批量写入的条数|可选，默认值为1000。|
|batchWriteTimeoutMs|写入超时时间|可选，单位为毫秒，默认值为5000。表示如果缓存中的数据在等待5秒后，依然没有达到输出条件，系统会自动输出缓存中的所有数据。|
|connectionMaxActive|单连接池最大连接数|可选，默认值30|
|ignoreDelete|是否忽略delete操作|默认为false|

**说明：** 如果错代码是20015，则表示batchSize设置的过大。AnalyticDB单次batch不能超过1M。例如，batchSize设置为`1000`，平均每条记录大小不能超过1Kb。

## 类型映射 {#section_lqg_q42_cgb .section}

建议使用AnalyticDB和实时计算字段类型对应关系进行DDL声明。

|AnalyticDB字段类型|实时计算字段类型|
|--------------|--------|
|BOOLEAN|BOOLEAN|
|TINYINT|INT|
|SAMLLINT|
|INT|
|BIGINT|BIGINT|
|DOUBEL|DOUBLE|
|VARCHAR|VARCHAR|
|DATE|DATE|

