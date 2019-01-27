# 自定义标量函数（UDF） {#concept_69472_zh .concept}

本文为您介绍如何为实时计算自定义标量函数（UDF）搭建开发环境、编写业务代码以及上线。

**说明：** 目前阿里云实时计算共享模式暂不支持自定义函数，仅独享模式支持自定义函数。

## 定义 {#section_qrh_dpx_dgb .section}

用户定义的标量函数（UDF）将0个、1个或多个标量值映射到一个新的标量值。

## 开发环境搭建 {#section_ikd_3px_dgb .section}

请您参见[环境搭建](cn.zh-CN/使用指南/Flink SQL/自定义函数（UDX）/UDX概述.md#section_ck2_gcm_cgb)。

## 编写业务逻辑代码 {#section_hsh_jpx_dgb .section}

UDF需要在ScalarFunction类中实现`eval`方法。`open`方法和`close`方法可选。以Java为例，示例代码如下。

```language-java
package com.hjc.test.blink.sql.udx;


import org.apache.flink.table.functions.FunctionContext;
import org.apache.flink.table.functions.ScalarFunction;

public class StringLengthUdf extends ScalarFunction {
    // 可选，open方法可以不写
    // 需要import org.apache.flink.table.functions.FunctionContext;
    @Override
    public void open(FunctionContext context) {
        }
    public long eval(String a) {
        return a == null ? 0 : a.length();
    }
    public long eval(String b, String c) {
        return eval(b) + eval(c);
    }
    //可选，close方法可以不写
    @Override
    public void close() {
        }
}

```

## 上线 {#section_cwx_jpx_dgb .section}

找到您指定的Class编写SQL语句后，点击**上线**，进入运维页面点击**启动**即可。

```language-sql
-- udf str.length()
CREATE FUNCTION stringLengthUdf AS 'com.hjc.test.blink.sql.udx.StringLengthUdf';
create table sls_stream(
	a int,
	b int,
	c varchar
) with (
	type='sls',
	endPoint='xxxxxxx',
	accessKeyId='xxxxx',
	accessKeySecret='xxxxxx',
	startTime = '2017-07-04 00:00:00',
	project='xxxxx',
	logStore='stream-test2',
	consumerGroup='consumerGroupTest1'
);
create table rds_output(
	id int,
	len bigint,
	content VARCHAR
) with (
	type='rds',
	url='xxxxxxx',
	tableName='xxxx',
	userName='xxxx',
	password='123456'
);
insert into rds_output
select
	a,
	stringLengthUdf(c),
	c as content
from sls_stream


```

## 常见问题 {#section_m5p_kpx_dgb .section}

Q：为什么实现了一个随机数生成函数，在运行时产生的值却是一样的？

A：如果自定义函数是无参的，并且没有声明是非确定性函数，编译期间可能会被优化掉变成一个常量值。所以如果想让函数变成非确定性函数 ，不被优化成常量，可以在自定义函数中override`isDeterministic()` 方法， 返回 `false` 即可。

