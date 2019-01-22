# 注册日志服务（Log Service） {#concept_62476_zh .concept}

本文为您介绍实时计算如何使用存储注册的方式连接日志服务（Log Service），以及存储注册过程中的常见问题。

## 注册 {#section_rxr_1hb_yfb .section}

日志服务（Log Service）简称LOG，原称SLS。是针对日志场景的一站式解决方案。提供海量日志数据采集、订阅、转储与查询功能。日志服务是阿里云的日志管理平台。在您使用日志服务完成了对ECS日志的管理的前提下，实时计算可以直接对接日志服务的LogHub存储，无需对数据进行迁移。

**说明：** 日志服务在公有云使用时，需要您授予实时计算代为您访问日志服务的权限（具体请参看[角色授权]()），否则可能出现报错`No Permission`。

![4](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40855/154814941933132_zh-CN.png)

-   Endpoint填写

    填写日志服务的Endpoint。不同的地域下日志服务有不同的Endpoint，请参见日志服务服务入口[服务入口](../../../../../cn.zh-CN/API 参考/服务入口.md#)。

    **说明：** 

    -   Endpoint需增加`http://`开头，且不能使用`/`结尾，如`http://cn-hangzhou-intranet.log.aliyuncs.com`。
    -   实时计算和日志服务同处于阿里云内网，建议您填写经典网络或VPC网络服务入口。为了避免消耗大量外网带宽和导致可能的性能问题，不建议填写公网服务入口。
    -   有关专有云日志服务的Endpoint填写，请咨询专有云系统管理员。
-   Project填写

    填写日志服务的Project。

    **说明：** 跨属主的数据存储不能注册。例如A用户拥有日志服务的Project A，但B用户希望在实时计算使用Project A。目前，实时计算暂不支持这类场景下的数据存储注册方式，您可以使用明文方式，具体参见[明文方式](cn.zh-CN/使用指南/数据存储/数据存储概述.md#section_qx2_4tz_zfb)。


## 常见问题 { .section}

Q: 为什么我注册失败，失败原因提示XXX？

A: 实时计算的数据存储页面能够协助您完成数据管理，其本身就是使用相关存储SDK代为访问各类存储。因此很多情况下可能是您注册过程出现问题导致。请排查如下原因。

-   请确认是否已经开通并拥有日志服务的Project。请登录[日志服务控制台](https://sls.console.aliyun.com/)，公有云用户可以访问日志服务控制台看您是否有权限访问您的Project。
-   请确认您是日志服务Project的属主。跨属主的数据存储不能注册。
-   请确认您填写的日志服务的Endpoint和Project完全正确。特别注意的是日志服务 Endpoint必须以`http`开头，且不能以`/`结尾，例如`http://cn-hangzhou.log.aliyuncs.com`是正确的，但`http://cn-hangzhou.log.aliyuncs.com/`是错误的。
-   请不要重复注册。实时计算提供注册检测机制，避免您重复注册。

Q: 为什么数据抽样仅仅针对时间抽样，不支持其他字段抽样？

A: 日志服务定位是流数据存储，对外提供的接口也只有时间参数。因此，实时计算也只能提供基于时间的抽样。如果希望使用日志服务的检索功能，请登录并确认是否已经开通并拥有日志服务的Project。请登录[日志服务控制台](https://sls.console.aliyun.com/#/)使用检索。

