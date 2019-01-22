# 注册云数据库（RDS） {#concept_62478_zh .concept}

本文为您介绍如何使用存储注册的方式连接云数据库（RDS）数据存储，以及连接过程中的常见问题。

云数据库RDS（ApsaraDB for RDS，简称RDS）是一种稳定可靠、可弹性伸缩的在线数据库服务。目前实时计算支持包括MySQL在内的RDS引擎。

**说明：** 

-   RDS在公有云使用需要您授予实时计算访问RDS的权限，具体请参看[角色授权]()。否则可能出现报错`No Permission`的情况。
-   在实时计算中，下游数据库使用MySQL等关系数据库（对应的CONNECTOR为TDDL和RDS），当实时计算频繁写某个表或者资源时，存在死锁风险。高QPS/TPS或高并发写入情况场景，一般不建议使用TDDL或者RDS作为实时计算作业的结果表，建议使用[表格存储（TableStore）](cn.zh-CN/使用指南/Flink SQL/DDL语句/创建数据结果表/创建表格存储（Table Store）结果表.md#)作为结果表，可以避免死锁的问题。

## 注册 {#section_vnh_vgd_yfb .section}

**说明：** 存储注册过程中系统会为RDS自动配置IP白名单。

![74](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40857/154814943733158_zh-CN.png)

信息填写：

-   Region

    选择RDS的地域，请您选择RDS所在地域。

-   Instance

    填写RDS实例ID。

    **说明：** 请填写实例ID，不是实例名称。

-   DBName

    填写RDS下DataBase的名称。

    **说明：** DataBase是RDS的数据库名称，不是Instance的名称。

-   Username

    数据库登录名称。

-   Password

    数据库登录密码。

-   白名单授权

    目前RDS使用白名单进行安全保证，实时计算存储注册方式为RDS自动添加IP白名单。


## 常见问题 {#section_ejy_2zg_yfb .section}

Q：存储注册过程中出现报错`操作错误：未在VPC中授权`？

![66](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40857/154814943733245_zh-CN.png)

A：引起该报错的原因是，您在创建RDS实例时选择的不是经典网络，而是专有网络（VPC模式）。对于这种实例应该进行VPC访问授权。请参看[VPC访问授权](cn.zh-CN/使用指南/数据存储/VPC访问授权.md#)。

