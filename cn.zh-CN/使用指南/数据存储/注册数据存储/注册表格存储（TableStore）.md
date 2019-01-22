# 注册表格存储（TableStore） {#concept_62477_zh .concept}

本文为您介绍实时计算如何用存储注册的方式连接表格存储（TableStore）。

## 注册 {#section_wff_snb_yfb .section}

表格存储（Table Store）是构建在阿里云飞天分布式系统之上的NoSQL数据存储服务。能够提供海量结构化数据的存储服务和实时访问服务。实时计算对于请求访问时延要求较高，同时对于关系型代数需求又较低。因此，实时计算非常适合使用TableStore作为数据维表和结果表。

**说明：** 若您在使用Table Store时，出现`No Permission`类似错误，请参见[角色授权]()授予实时计算访问TableStore的权限。

![65](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40856/154814952333141_zh-CN.png)

-   Endpoint

    -   填写TableStore的Endpoint。请在[OTS控制台](https://ots.console.aliyun.com)查看TableStore的Endpoint信息，填写TableStore私网地址。具体步骤请参见[表格存储（TableStore）Endpoint](../../../../../cn.zh-CN/产品简介/名词解释/服务地址.md#)。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40856/154814952333142_zh-CN.png)

    -   TableStore访问网络类型设置为**允许任意网络访问**。登录[OTS控制台](https://ots.console.aliyun.com/)选择**实例详情** \> **实例网络类型** \> **更改** \> **允许任意网络访问**。如下图:![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40856/154814952333810_zh-CN.png)![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40856/154814952333148_zh-CN.png)
-   实例名称

    填写TableStore的实例名称。


