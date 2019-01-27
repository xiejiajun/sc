# UDX概述 {#concept_69463_zh .concept}

本文为您介绍如何为实时计算自定义函数搭建环境和使用。

**说明：** 目前阿里云实时计算共享模式暂不支持自定义函数，仅独享模式支持自定义函数。

## 简介 {#section_pyp_ccm_cgb .section}

实时计算支持三种自定义函数（UDX），分别是：

-   UDF（User Defined Function）

    自定义标量函数，输入一条记录的0个、1个或者多个值，返回一个值。

-   UDAF（User Defined Aggregation Function）

    自定义聚合函数，将多条记录聚合成一条值。

-   UDTF（User Defined Table Function）

    自定义表值函数，能将多条记录转换后再输出，输出记录的个数和输入记录数不需要一一对应，也是唯一能返回多个字段的自定义函数。


## 环境搭建 {#section_ck2_gcm_cgb .section}

自定义函数（UDX）的开发需要依赖实时计算的相关jar包，为了便于用户快速的搭建环境，我们提供了一个UDX开发Demo（`RealtimeCompute-udxDemo.gz`），该Demo为Maven的project，您可使用IntelliJ IDEA直接打开，并在此基础上进行开发。

Demo中已经分别有三个简单的UAF、UDAF和UDTF的实现，供参考。

 `[RealtimeCompute-udxDemo.gz](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/69463/cn_zh/1535104736459/RealtimeCompute-udxDemo.gz)` 

以上Demo主要使用的依赖jar包如下，如您需要单独使用，可以直接下载：

 `[flink-streaming-java\_2.11](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/98378/cn_zh/1543327398632/flink-streaming-java_2.11-blink-2.2.4.jar)` 

 `[flink-table\_2.11](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/98378/cn_zh/1543327437386/flink-table_2.11-blink-2.2.4.jar)` 

 `[flink-core-blink-2.2.4](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/98378/cn_zh/1543326995841/flink-core-blink-2.2.4.jar)` 

**说明：** 上文中的Demo包下载后，要将`pom.xml`按照下边示例进行更改。

```language-java
    <dependency>
        <groupId>org.apache.flink</groupId>
        <artifactId>flink-core</artifactId>
        <version>blink-2.2.4-SNAPSHOT</version>
        <scope>provided</scope>
    </dependency>
    <dependency>
        <groupId>org.apache.flink</groupId>
        <artifactId>flink-table_2.11</artifactId>
        <version>blink-2.2.4-SNAPSHOT</version>
        <scope>provided</scope>
    </dependency> 
    <dependency>
         <groupId>org.apache.flink</groupId>
         <artifactId>flink-streaming-java_2.11</artifactId>
         <version>blink-2.2.4-SNAPSHOT</version>
         <scope>provided</scope>
    </dependency>

```

## 注册使用 {#section_ks5_gcm_cgb .section}

将写好的UDF、UDAF或UDTF打成JAR包，在开发页面点击**上传**。

![2314](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/41057/154856423434464_zh-CN.png)

上传完资源后，选择相应的作业进行引用，然后在作业里声明自定义的函数。

```language-SQL
CREATE FUNCTION stringLengthUdf AS 'com.hjc.test.blink.sql.udx.StringLengthUdf';

```

![2314](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/41057/154856423434466_zh-CN.png)

