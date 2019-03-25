# Flink SQL开发概述 {#concept_kzv_b4d_2hb .concept}

阿里实时计算开发平台为实时计算产品Flink SQL作业提供了一站式的存储管理、作业开发、作业调试、运维管理和监控报警的功能。

Flink SQL开发指南主要包含以下内容。

-   数据存储
    -   提供了实时计算主流的上下游存储（RDS、DataHub、OTS等）的管理界面，通过存储注册方式引入的上下存储，可以实现数据预览、数据抽样以及自动生成DDL的功能，请参见[数据存储概述](cn.zh-CN/Flink SQL开发指南/数据存储/数据存储概述.md#)。
    -   若共享模式集群需访问阿里云VPC网络下的存储资源，请参见[VPC访问授权](cn.zh-CN/Flink SQL开发指南/数据存储/VPC访问授权.md#)。
    -   若实时计算访问的上下游存储存在白名单机制，请参见[如何配置数据存储白名单](cn.zh-CN/Flink SQL开发指南/数据存储/如何配置数据存储白名单.md#)。
-   作业开发

    介绍Flink SQL作业[开发](cn.zh-CN/Flink SQL开发指南/作业开发/开发.md#)、[上线](cn.zh-CN/Flink SQL开发指南/作业开发/上线.md#)到[启动](cn.zh-CN/Flink SQL开发指南/作业开发/启动.md#)的整个流程。

-   作业调试

    介绍Flink SQL作业调试功能，包括[本地调试](cn.zh-CN/Flink SQL开发指南/作业调试/本地调试.md#)和[线上调试](cn.zh-CN/Flink SQL开发指南/作业调试/线上调试.md#)。

-   数据运维

    介绍[作业状态](cn.zh-CN/Flink SQL开发指南/作业运维/作业状态.md#)、[数据曲线](cn.zh-CN/Flink SQL开发指南/作业运维/数据曲线.md#)、[Failover](cn.zh-CN/Flink SQL开发指南/作业运维/FailOver.md#)等数据运维相关内容。

-   [监控报警](cn.zh-CN/Flink SQL开发指南/监控报警.md#)

    介绍如何创建和启动报警规则。

-   配置调优

    介绍Flink SQL作业的调优功能，主要包括[SQL调优](cn.zh-CN/Flink SQL开发指南/配置调优/高性能Flink SQL优化技巧.md#)、[autoconf自动调优](cn.zh-CN/Flink SQL开发指南/配置调优/autoconf自动配置调优.md#)、[autoscale自动调优](cn.zh-CN/Flink SQL开发指南/配置调优/autoscale自动配置调试.md#)和[手动配置调优](cn.zh-CN/Flink SQL开发指南/配置调优/手动配置调优.md#)。

-   Flink SQL介绍

    Flink SQL语法，具体请参见[Flink SQL概述](https://help.aliyun.com/document_detail/103076.html?spm=a2c4g.11186623.6.600.356228a076WG8E)


