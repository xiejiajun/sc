# VPC访问授权 {#concept_66270_zh .concept}

本文为您介绍VPC访问授权的流程。

## 背景 {#section_a3c_swz_zfb .section}

实时计算共享模式集群位于阿里云的经典网络，若需要访问阿里云VPC网络下的存储资源（如VPC下的RDS、VPC下ECS上自建的MySQL、VPC下的HybridDB等数据库），则需要通过VPC访问授权。

**说明：** 

-   由于独享模式集群本身就处于阿里云的VPC网络，因此无需进行VPC访问授权。
-   VPC访问授权后，实时计算访问对应的存储资源可能存在带宽受限等性能问题，一般不建议使用。

## 实时计算VPC访问授权 {#section_c3c_swz_zfb .section}

在实时计算开发平台界面，点击右上方账号，选择**项目管理** \> **VPC访问授权** \> **新增授权**，即可进入**VPC访问授权**界面。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40858/154814954133653_zh-CN.png)

以VPC下的RDS为例:

-   名称

    请填写自定义的名称。

-   地域

    选择RDS所在的地域。

-   VPC ID和Instance ID

    登录[RDS控制台](https://rdsnew.console.aliyun.com/?spm=a2c4g.11186623.2.20.42313d2betYPAZ#/rdsList/cn-shanghai/basic/) ，在**实例列表** \> **基本信息**查找实例的VPC ID和Instance ID信息，如下图：

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40858/154814954133654_zh-CN.png)

-   Instance Port

    登录[RDS控制台](https://rdsnew.console.aliyun.com/?spm=a2c4g.11186623.2.22.42313d2betYPAZ#/rdsList/cn-shanghai/basic/) ，在**实例列表** \> **基本信息** \> **管理** \> **基本信息** \> **内网端口**查询端口ID。如下图：

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40858/154814954133655_zh-CN.png)


VPC访问授权成功后，如下图所示：

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40858/154814954133656_zh-CN.png)

**说明：** 

当使用的是VPC下的存储时，URL需填写VPC授权页面的Mapping IP：Mapping Port。以RDS为例：URL=`'jdbc:mysql//Mapping IP:Mapping Port/数据库名'`。

Mapping IP和Mapping Port的查询如下图:

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40858/154814954136284_zh-CN.png)

