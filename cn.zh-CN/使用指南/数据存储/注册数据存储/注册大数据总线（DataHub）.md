# 注册大数据总线（DataHub） {#concept_62475_zh .concept}

本文为您介绍如何注册实时计算大数据总线（DataHub）数据存储，以及注册存储过程中的常见问题。

## 注册 {#section_ifr_4k5_xfb .section}

DataHub作为一个流式数据总线，为阿里云数加平台提供了大数据的入口服务。结合阿里云众多云产品，可以构建一站式的数据处理平台。实时计算通常使用DataHub作为流式数据存储头和输出目的端。

**说明：** DataHub在公有云使用需要用户授予实时计算代为用户访问DataHub权限，具体请参见[共享模式角色授权](../../../../../cn.zh-CN/准备工作/共享模式角色授权.md#)。否则可能出现报错`No Permission`。

![22](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40854/155261294933029_zh-CN.png)

**说明：** 如何进入注册数据存储界面请参见[注册数据存储](cn.zh-CN/使用指南/数据存储/数据存储概述.md#ul_fgq_y41_1gb)。

-   Endpoint

    填写Datahub的Endpoint。需要注意，不同地域下DataHub有不同的Endpoint，具体请参见

    [Datahub访问控制](https://help.aliyun.com/document_detail/47442.html?spm=a2c4g.11186623.6.545.41a01a12lmpjWC)。

    **说明：** `http://dh-cn-hangzhou.aliyun-inc.com`不要使用`/`结尾。

    -   共享模式：使用经典网络ECS Endpoint。如华东1（杭州）使用`http://dh-cn-hangzhou.aliyun-inc.com`。共享模式大集群所在的区域与Datahub所在区域不要求一致，但不一致可能额外增加网络延时。
    -   独享模式：使用VPC ECS Endpoint。如华东1（杭州）使用`http://dh-cn-hangzhou.aliyun-inc.com`。独享模式小集群所在的区域与Datahub所在区域要求一致。
    -   有关专有云的Endpoint填写，请联系您的专有云系统管理员，咨询有关DataHub Endpoint地址。
-   Project

    填写DataHub的Project。

    **说明：** 跨属主的数据存储不能注册。例如A用户拥有DataHub的ProjectA，但B用户希望在实时计算使用ProjectA，目前实时计算暂不支持这类使用场景下注册，若需使用可使用明文方式，具体参考数据存储概述-明文方式[明文方式](cn.zh-CN/使用指南/数据存储/数据存储概述.md#section_qx2_4tz_zfb)。


## 常见问题 { .section}

Q: 为什么我注册失败，失败原因提示XXX？

A: 实时计算的数据存储页面能够协助您完成数据管理，其本身就是使用相关存储SDK代为访问各类存储。因此很多情况下可能是您注册过程出现问题导致，请排查如下原因。

-   请确认是否已经开通并拥有DataHub的Project。请登录[DataHub控制台](https://datahub.console.aliyun.com/datahub?spm=a2c4g.11186623.2.16.2d882a36F2C50S)，公有云客户可以访问DataHub控制台看您是否有权限访问您的Project。
-   请确认您是DataHub Project的属主。跨属主的数据存储不能注册。
-   请确认您填写的DataHub的Endpoint和Project完全正确。DataHub Endpoint必须以http开头，且不能以\(/\)结尾。例如，`http://dh-cn-hangzhou-internal.aliyuncs.com`是正确的，但`http://dh-cn-hangzhou-internal.aliyuncs.com/`是错误的。
-   请确认您填写的DataHub Endpoint是经典网络地址，而非VPC地址。目前实时计算暂不支持VPC内部地址。
-   请不要重复注册，实时计算提供注册检测机制，避免您重复注册。

Q: 为什么数据抽样仅仅针对时间抽样，不支持其他字段抽样？

A: DataHub定位是流数据存储，对外提供的接口也只有时间参数。因此，实时计算也只能提供基于时间的抽样。

