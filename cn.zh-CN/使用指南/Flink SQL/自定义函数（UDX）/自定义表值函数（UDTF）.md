# 自定义表值函数（UDTF） {#concept_69559_zh .concept}

本文为您介绍如何为实时计算自定义表值函数（UDTF）搭建开发环境、编写业务代码以及上线。

**说明：** 目前阿里云实时计算共享模式暂不支持自定义函数，仅独享模式支持自定义函数。

## 定义 {#section_ml5_qwx_dgb .section}

与自定义的标量函数类似，自定义的表值函数（UDTF）将0个、1个或多个标量值作为输入参数。与标量函数不同，表值函数可以返回任意数量的行作为输出，而不仅是1个值。返回的行可以由1个或多个列组成。

## 开发环境搭建 {#section_rdj_3xx_dgb .section}

参见[环境搭建](cn.zh-CN/使用指南/Flink SQL/自定义函数（UDX）/UDX概述.md#section_ck2_gcm_cgb)。

## 编写业务逻辑代码 {#section_d1j_kxx_dgb .section}

UDTF需要在TableFunction类中实现eval方法。open方法和close方法可选。以Java为例，示例代码如下。

```language-java
package com.hjc.test.blink.sql.udx;

import org.apache.flink.table.functions.FunctionContext;
import org.apache.flink.table.functions.TableFunction;

public class SplitUdtf extends TableFunction<String> {

    // 可选， open方法可以不写,若写需要import org.apache.flink.table.functions.FunctionContext;
    @Override
    public void open(FunctionContext context) {
        // ... ...
        }

    public void eval(String str) {
        String[] split = str.split("\\|");
        for (String s : split) {
            collect(s);
        }
    }

    // 可选，close方法可以不写
    @Override
    public void close() {
        // ... ...
        }

}

```

## 多行返回 {#section_mss_kxx_dgb .section}

UDTF可以通过多次调用`collect\(\)`实现将1行的数据转为多行返回。

## 多列返回 {#section_xjg_lxx_dgb .section}

UDTF不仅可以做到1行转多行，还可以1列转多列。如果您需要UDTF返回多列，只需要将返回值声明成Tuple或Row即可。实时计算支持 Tuple1 ~ Tuple25 ，分别定义1个字段到25个字段。 如下示例，用Tuple3来返回3个字段的UDTF。

```language-java
import org.apache.flink.api.java.tuple.Tuple3;
import org.apache.flink.table.functions.TableFunction;

// 使用Tuple作为返回值，一定要显式声明Tuple的泛型类型, 如此例的 String, Long, Integer
public class ParseUdtf extends TableFunction<Tuple3<String, Long, Integer>> {

public void eval(String str) {
String[] split = str.split(",");
// 仅作示例，实际业务需要添加更多的校验逻辑
String first = split[0];
long second = Long.parseLong(split[1]);
int third = Integer.parseInt(split[2]);
Tuple3<String, Long, Integer> tuple3 = Tuple3.of(first, second, third);
collect(tuple3);
}
}

```

**说明：** 使用Tuple时，字段值不能为null，且最多只能有25个字段。

如果使用Row来实现返回3个字段的UDTF，需使用以下代码。

```language-java
import org.apache.flink.table.types.DataType;
import org.apache.flink.table.types.DataTypes;
import org.apache.flink.table.functions.TableFunction;
import org.apache.flink.types.Row;

public class ParseUdtf extends TableFunction<Row> {

public void eval(String str) {
String[] split = str.split(",");
String first = split[0];
long second = Long.parseLong(split[1]);
int third = Integer.parseInt(split[2]);
Row row = new Row(3);
row.setField(0, first);
row.setField(1, second);
row.setField(2, third);
collect(row);
}

@Override
// 如果返回值是Row，就必须重载实现这个方法，显式地告诉系统返回的字段类型
public DataType getResultType(Object[] arguments, Class[] argTypes) {
return DataTypes.createRowType(DataTypes.STRING, DataTypes.LONG, DataTypes.INT);
}
}

```

**说明：** 

Row的字段值可以是null，但如果需要使用Row，必须重载实现`getResultType`方法。

## SQL 语法 {#section_dlq_lxx_dgb .section}

UDTF支持CORSS JOIN和LEFT JOIN，在使用UDTF时需要带上 `LATERAL`和`TABLE`两个关键字，以上述的`ParseUdtf`为示例，首先注册一个function名字。

```language-sql
CREATE FUNCTION parseUdtf AS 'com.alibaba.blink.sql.udtf.ParseUdtf';

```

CROSS JOIN：左表的每一行数据都会关联上UDTF 产出的每一行数据，如果UDTF 不产出任何数据，那么这一行不会输出。

```language-sql
select S.id, S.content, T.a, T.b, T.c
from input_stream as S,
LATERAL TABLE(parseUdtf(content)) as T(a, b, c);

```

LEFT JOIN：左表的每一行数据都会关联上UDTF 产出的每一行数据，如果UDTF 不产出任何数据，那么这一行的UDTF的字段会用null值填充。

**说明：** LEFT JOIN UDTF 语句后面必须接 `ON TRUE`参数。

```language-sql
select S.id, S.content, T.a, T.b, T.c
from input_stream as S
LEFT JOIN LATERAL TABLE(parseUdtf(content)) as T(a, b, c) ON TRUE;

```

## 上线 {#section_yxf_mxx_dgb .section}

找到您指定的Class编写SQL语句后，点击**上线**，进入**运维**页面点击**启动**即可。

```language-sql
-- udtf str.split("\\|");
CREATE FUNCTION splitUdtf AS 'com.hjc.test.blink.sql.udx.SplitUdtf';

create table sls_stream(
a int,
b bigint,
c varchar
) with (
type='sls',
endPoint='xxxxxxx',
accessKeyId='xxxxxx',
accessKeySecret='xxxxxx',
startTime = '2017-07-04 00:00:00',
project='xxxxxxx',
logStore='stream-test2',
consumerGroup='consumerGroupTest2'
);

-- 将c字段传入splitUdtf，切分后得到多行一列的表T(s)，s表示字段名字
create view v1 as
select a,b,c,s
from sls_stream,
LATERAL TABLE(splitUdtf(c)) as T(s);

create table rds_output(
id int,
len bigint,
content VARCHAR
) with (
type='rds',
url='xxxxxxxx',
tableName='xxxxx',
userName='xxxxx',
password='123456'
);

insert into rds_output
select
a,b,s
from v1

```

