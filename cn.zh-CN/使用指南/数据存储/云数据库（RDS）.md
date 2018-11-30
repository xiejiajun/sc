# 云数据库（RDS） {#concept_62478_zh .concept}

本文为您介绍如何使用注册数据存储的方式或明文的方式连接云数据库（RDS）数据存储，以及连接过程中的常见问题。

## 什么是RDS {#section_s24_32d_yfb .section}

云数据库RDS（ApsaraDB for RDS，简称RDS）是一种稳定可靠、可弹性伸缩的在线数据库服务。基于飞天分布式系统和全SSD盘高性能存储，支持MySQL、SQL Server、PostgreSQL和PPAS（高度兼容Oracle\)引擎。目前实时计算支持包括MySQL在内的RDS引擎。

通常受限于关系型模型，RDS对于大容量高并发请求的支持不如TableStore，因此在实时计算中，大部分情况是将RDS作为结果表，查看[RDS维表DDL定义]()。但对于小批量数据、低并发的情况下，实时计算可以使用RDS作为维表，[RDS输出表DDL定义]()。

**说明：** 

-   RDS在公有云使用需要您授予实时计算代为您访问RDS权限，具体请参看[角色授权]()。否则可能出现报错`No Permission`的情况。
-   在实时计算中，下游数据库使用MySQL等关系数据库（对应的CONNECTOR为TDDL和RDS），当实时计算频繁写某个表或者资源时，存在死锁风险。高QPS/TPS或高并发写入情况场景，一般不建议使用TDDL或者RDS作为实时计算作业的结果表，建议使用[表格存储（TableStore）](https://help.aliyun.com/document_detail/62477.html?spm=a2c4g.11186631.6.582.h0SEK4)作为结果表，可以避免死锁的问题。

## 注册数据存储方式 {#section_vnh_vgd_yfb .section}

**说明：** 如果您使用当前主账号的RDS存储资源，建议您使用注册数据存储的方式，避免在升级扩容后无法连接存储资源而造成的不必要的损失。

。

![74](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40857/154354767333158_zh-CN.png)

1.  信息填写如下：
    -   Region

        选择RDS的地域，建议您尽可能选择和自己购买实时计算相同的地域。

    -   Instance

        填写RDS实例ID，注意是实例ID，非实例名称。目前实时计算支持RDS的VPC网络，请参看下属章节实时计算使用RDS的VPC模式。

    -   DataBase

        填写连接的数据库名称。

        **说明：** DataBase是RDS的数据库名称，不是Instance的名称。

        目前RDS使用白名单进行安全保证，实时计算需要为RDS自动添加白名单，请参看实时计算使用RDS的VPC模式。

    -   Username

        数据库登录名称。为了和数据库通信协议兼容，RDS在登录用户名/密码处，不支持使用阿里云的AccessId、AccessKey方式，推荐您为实时计算读写RDS提供单独的用户名和密码，分账号管理更容易保证RDS系统安全。

    -   Password

        数据库登录密码。为了和数据库通信协议兼容，RDS在登录用户名/密码处，不支持使用阿里云的AccessId、AccessKey方式，推荐您为实时计算读写RDS提供单独的用户名和密码，分账号管理更容易保证RDS系统安全。

2.  注册成功后，选择想要注册的结果表，引用为结果表。

    ![2](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40857/154354767433196_zh-CN.png)

3.  成功引用为结果表。

    ![2222](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40857/154354767433244_zh-CN.png)


## 注册数据存储常用问题 {#section_ejy_2zg_yfb .section}

Q：注册数据存储过程中出现报错`操作错误：未在VPC中授权`？

![66](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40857/154354767433245_zh-CN.png)

A：引起该报错的原因是，您在创建RDS实例时选择的不是经典网络，而是专有网络（VPC模式）。对于这种实例应该进行授权操作。授权操作步骤如下：

1.  登录控制台在总览页面，如下图选择**系统设置**。

    ![系统设置](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40857/154354767433246_zh-CN.png)

2.  点击**VPC访问权限** \> **新增授权**。

    ![新增授权](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40857/154354767433248_zh-CN.png)

3.  填写授权信息。

    ![1](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/62478/cn_zh/1525331208036/1.png)

    -   地域

        地域即RDS地域 。

    -   VPC ID/Instance ID

        登录[RDS控制台](https://rdsnew.console.aliyun.com/#/rdsList/cn-shanghai/basic/) ，在**实例列表** \> **基本信息**查找实例的VPC ID/Instance ID信息，r如下图。

        ![31345](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40857/154354767433250_zh-CN.png)

    -   Instance Port

        登录[RDS控制台](https://rdsnew.console.aliyun.com/#/rdsList/cn-shanghai/basic/) ，在**实例列表** \> **基本信息** \> **管理** \> **基本信息** \> **内网端口**查询端口ID。如下图：

        ![点击管理](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40857/154354767433251_zh-CN.png)

        ![端口号](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40857/154354767433252_zh-CN.png)


## 明文方式 {#section_qny_wgd_yfb .section}

-   明文方式使用场景

    **说明：** 

    若使用主账号下的存储资源，不建议使用明文方式连接RDS，请使用注册数据存储方式。

    若使用的不是当前主账号的存储资源，跨属主的数据存储不能注册。例如A用户拥有RDS的实例A，但B用户希望在实时计算使用实例A，目前实时计算暂不支持这种情况下使用数据存储注册的方式注入，需要使用明文的方式。

-   明文方式使用方法

    明文方式下，B用户需要将作业的with参数中的`url`、`username`、`password`、`tablename`参数按照实例A的信息填写，如下图：

    ![明文](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40857/154354767433253_zh-CN.png)

    **说明：** 

    使用VPC下的RDS时，url需填写VPC授权页面的Mapping IP：Mapping Port。 例如：`url='jdbc:mysql//Mapping IP:Mapping Port/库名`。 Mapping IP和Mapping Port的查询如下图:

    ![Mappingport](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40857/154354767433254_zh-CN.png)

-   RDS白名单设置

    使用明文的方式需要用户进行白名单设置。

    -   什么是白名单

        部分数据存储为了自我的安全需求，提供白名单机制，即仅允许用户指定的白名单IP地址中的访问链接，其他不在白名单均予以拒绝，但同样机制会拦住其他阿里云产品写入数据存储的需求。以RDS为例，新建的RDS数据库是完全拒绝任何外部连接的，此时必须要添加访问白名单，即加入访问者的IP才能够访问RDS。同样，对于实时计算需要将RDS作为维表、结果表使用时候，需要多次读写数据库，因此对于存在白名单机制的数据存储而言，必须将实时计算的WebConsole以及执行节点\(Worker\)加入白名单才能够连接RDS。

    -   RDS白名单网段地址

        RDS支持两种访问地址，包括内网地址、外网地址。针对RDS，您只需要补充实时计算白名单网段地址即可，具体范围如下:

         **华东：** 

        ```language-SQL
        10.152.0.0/16,10.154.0.0/16,11.132.0.0/16,11.178.0.0/16,11.217.0.0/16,11.219.0.0/16,11.223.69.0/24,11.223.70.0/24,11.222.0.0/16
        
        ```

         **华北：** 

        ```language-SQL
        11.223.0.0/16
        
        ```

         **华南：** 

        ```language-SQL
        11.200.0.0/16
        
        ```

    -   白名单的设置步骤如下：

        1.  登录[RDS管理控制台](https://rdsnext.console.aliyun.com)，选择目标实例。
        2.  在实例菜单中选择**数据安全性**。
        3.  -   在**白名单设置** \> **defaul**点击**修改**，如下图所示。

    ![d35](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40857/154354767433255_zh-CN.png)

-   您也可以使用自定义分组，点击**default** \> **清空** \> **添加白名单分组**新建自定义白名单分组。在添加白名单分组页面删除默认白名单`127.0.0.1`，填写自定义白名单后，单击**确定**，如下图所示。

    **说明：** `127.0.0.1` 为新建 RDS 实例的默认值，需要您在使用前进行修改。如果白名单只包含了`127.0.0.1`，则该实例禁止任何 IP 访问。

    ![e2](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40857/154354767433256_zh-CN.png)

    参数说明：

    -   分组名称：由 2~32 个字符的小写字母，数字或下划线组成、开头需为小写字母，结尾需为字母或数字。默认分组不可修改，且不可删除。
    -   组内白名单：填写可以访问数据库的IP地址或者IP段，IP地址或者IP段间用英文逗号分隔。
        有关RDS白名单详细说明请参看[设置白名单](https://help.aliyun.com/document_detail/43185.html)。


## 明文方式常见问题 {#section_gsl_xgd_yfb .section}

Q：运行中报错异常栈，如图所示：

![异常信息](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40857/154354767433257_zh-CN.png)

A：将您所在区域的IP添加至白名单，具体步骤请参看[RDS白名单设置](#ul_ddq_kxh_yfb)。

华东

```language-SQL
10.152.0.0/16,10.154.0.0/16,11.132.0.0/16,11.178.0.0/16,11.217.0.0/16,11.219.0.0/16,11.223.69.0/24,11.223.70.0/24,11.222.0.0/16

```

华北

```language-SQL
11.223.0.0/16

```

华南

```language-SQL
11.200.0.0/16

```

