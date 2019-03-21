# 创建日志服务（Log Service）结果表 {#concept_62529_zh .concept}

本文为您介绍如何创建实时计算日志服务（Log Service）结果表。

## 什么是日志服务 {#section_fzz_tnf_cgb .section}

日志服务（Log Service）是针对日志类数据的一站式服务，在阿里巴巴集团经历大量大数据场景锤炼而成。

## DDL定义 {#section_ivj_wnf_cgb .section}

实时计算支持使用日志服务作为结果输出，志服务结果表声明示例如下。

```language-sql
create table sls_stream(
 name VARCHAR,
 age BIGINT,
 birthday BIGINT
)with(
 type='sls',
 endPoint='******',
 accessId='******',
 accessKey='******',
 project='******',
 logStore='******'
);

```

**说明：** 建议使用日志服务存储注册功能，参见[注册日志服务（Log Service）](cn.zh-CN/使用指南/数据存储/注册数据存储/注册日志服务（Log Service）.md#)。

## WITH参数 {#section_njl_xnf_cgb .section}

|参数|注释说明|备注|
|--|----|--|
|endPoint|endPoint地址|[服务入口](../../../../../cn.zh-CN/API 参考/服务入口.md#)|
|project|项目名|无|
|topic|topic表名|无|
|accessId|accessId|无|
|accessKey|accessKey|无|
|mode|写入方式|可选，默认为`random`模式，选择`partition`则会按分区写入。|
|partitionColumn|分区列|如果`mode`为`partition`则必填。|
|topic|日志服务的topic|可选，默认为空。|
|source|日志的来源地，例如产生该日志机器的IP地址。|可选，默认为空。|

