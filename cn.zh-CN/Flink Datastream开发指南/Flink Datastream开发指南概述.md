# Flink Datastream开发指南概述 {#concept_kp5_2sd_2hb .concept}

阿里实时计算开发平台为实时计算产品Flink Datastream作业提供了作业提交、上线和启动功能，并提供了便利的运维管理和监控报警等功能。

**说明：** 

-   仅独享模式Blink3.x及以上版本，支持Flink Datastream功能。
-   若Datastream作业访问的上下游存储提供了白名单机制，请参见[如何配置数据存储白名单](../../../../../cn.zh-CN/Flink SQL开发指南/数据存储/如何配置数据存储白名单.md#)。
-   目前实时计算产品支持的Datastream作业基于开源的blink版本，开发相关的内容请参见[开源blink版本](https://github.com/apache/flink/tree/blink)。

Flink Datastream暂不支持Flink SQL作业中的注册数据存储、作业开发、作业调试、配置调优等功能。Flink Datastream开发指南主要包含如下内容：

-   作业开发

    介绍在阿里实时计算开发平台上提交、上线和启动Flink Datastream作业的流程。

-   数据运维

    介绍Flink Datastream作业数据运维相关内容，其中数据曲线仅支持runtime相关metric的展示，而connector相关的指标若需要展示，需在Flink Datastream作业针对性进行metric埋点。

-   监控报警

    介绍如何创建和启动报警规则，目前仅支持FailoverRate指标的监控报警。


