# 注册分析型数据库（AnalyticDB） {#concept_lwm_5ty_zfb .concept}

本文为您介绍如何使用注册数据存储的方式或明文的方式连接分析型数据库（AnalyticDB）数据存储，以及连接过程中的常见问题。

## 什么是AnalyticDB {#section_zrn_yty_zfb .section}

分析型数据库（AnalyticDB）是阿里巴巴自主研发的海量数据实时高并发在线分析（Realtime OLAP）云计算服务。使得您可以在毫秒级时单位时间内针对千亿级数据进行即时的多维分析透视和业务探索。分析型数据库对海量数据的自由计算和极速响应能力，能让您在瞬息之间进行灵活的数据探索，快速发现数据价值，并可直接嵌入业务系统，为终端用户提供分析服务。

## 注册数据存储方式 {#section_kxb_15y_zfb .section}

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/75287/154390625233637_zh-CN.png)

1.  信息填写如下

    -   URL：AnalyticDB控制台链接信息串。
    -   Database：AnalyticDB库名。
    -   AccessId：阿里云账号安全信息页面的AccessID。
    -   AccessKey：阿里云账号安全信息页面的AccessKey。
    **说明：** 

    -   AccessId、AccessKey信息查询参看[如何查看AccessID、AccessKey信息](https://help.aliyun.com/knowledge_detail/94557.html)。
    -   URL​、Database信息查询需到ADS控制台查询，参考下图：

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/75287/154390625233638_zh-CN.png)

2.  注册成功后，选择想要注册的结果表，引用为结果表。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/75287/154390625233639_zh-CN.png)

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/75287/154390625233640_zh-CN.png)


## 明文方式 {#section_spk_r1z_zfb .section}

-   明文方式使用场景

    **说明：** 若使用主账号下的存储资源，不建议使用明文方式连接ADS，请使用注册数据存储方式。

    若使用的不是当前主账号的存储资源，跨属主的数据存储不能注册。例如A用户拥有ADS的实例A，但B用户希望在实时计算使用实例A，目前实时计算暂不支持这种情况下使用数据存储注册的方式注入，需要使用明文的方式。

-   明文方式使用方法

    明文方式下，B用户需要将作业的with参数中的 `url`、 `username`、 `password`、 `tablename`参数按照实例A的信息填写，如下图：

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/75287/154390625233641_zh-CN.png)


