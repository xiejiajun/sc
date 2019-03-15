# Watermark {#concept_lkg_hsy_bhb .concept}

实时计算可以基于时间属性对数据进行窗口聚合。基于的Event Time时间属性的窗口函数作业中，数据源表的声明中需要使用watermark方法。

watermark是一种衡量[Event Time](cn.zh-CN/使用指南/Flink SQL/基本概念/时间属性.md#section_jf3_mhf_5fb)进展的机制，它是数据本身的一个隐藏属性，watermark的定义是数据源表DDL定义的一部分。Flink提供了如下语法定义watermark。

**说明：** 实时计算时间属性详情，请参见[时间属性](cn.zh-CN/使用指南/Flink SQL/基本概念/时间属性.md#)。

```language-sql
WATERMARK [watermarkName] FOR <rowtime_field> AS withOffset(<rowtime_field>, offset)

```

|参数|说明|
|--|--|
|watermarkName|标识watermark的名字，可选。|
|<rowtime\_field\>|必须是表中已定义的一列（当前仅支持为TIMESTAMP类型），含义是基于该列生成watermark，并且标识该列为Event Time列，可以在后续query中用来定义窗口。|
|withOffset|是目前提供的watermark的生成策略，是根据`<rowtime_field> - offset`生成watermark的值。`withOffset`的第一个参数必须是`<rowtime_field>`。|
|offset|单位为毫秒，含义为watermark值与Event Time值的偏移量。|

通常一条记录中的某个字段就代表了该记录的发生时间。例如，表中有个`rowtime`字段，类型为TIMESTAMP，其中某个字段为`1501750584000（2017-08-03 08:56:24.000）`。如果您需要定义一个基于该`rowtime`列的watermark，watermark策略为偏移4秒，需要如下定义。

```
WATERMARK FOR rowtime AS withOffset(rowtime, 4000)

```

在这种情况下，这条数据的watermark时间为 `1501750584000 - 4000 = 1501750580000（2017-08-03 08:56:20.000）`。这条数据的watermark时间含义即：时间戳小于`1501750580000（2017-08-03 08:56:20.000）`的数据，都已经到达了。

**说明：** 

-   在使用Event Time Watermark时的`rowtime`必须是TIMESTAMP类型。当前支持毫秒级别的、在Unix时间戳里是13位。如果是其他类型或是在Unix时间戳不是13位，建议使用[计算列](cn.zh-CN/使用指南/Flink SQL/基本概念/计算列.md#)来做转换。
-   [Event Time](cn.zh-CN/使用指南/Flink SQL/基本概念/时间属性.md#section_jf3_mhf_5fb)和[Processing Time](cn.zh-CN/使用指南/Flink SQL/基本概念/时间属性.md#section_lv4_5kf_5fb)的声明只能在源表上声明。

watermark使用总结

-   Watermark含义是所有时间戳`t'< t`的事件都已经发生。假如watermark`t`已经生效，那么后续Event Time小于`t`的记录都会被丢弃掉（目前实时计算的处理方式是丢弃这些来的更晚的数据，后续支持用户配置让更晚的数据也能继续更新）。
-   针对乱序的的流，watermark至关重要。即使一些事件延迟到达，也不至于过于影响窗口计算的正确性。
-   并行数据流中，当算子（Operator）有多个输入流时，算子的Event Time以最小流Event Time为准。

    ![](images/40782_zh-CN.svg)


