# GetJob {#concept_yhk_ksq_tgb .concept}

GetJob API可以获得作业的详细信息。

## GetJob请求参数 {#section_nd5_qd1_rgb .section}

|参数|类型|是否必选|示例值|描述|
|jobName|String|是|job1|作业名称|
|projectName|String|是|project1|项目名称|

## GetJob返回参数 {#section_lww_xd1_rgb .section}

|参数|类型|示例值|描述|
|RequestId|String|`FD0FF8C0-779A-45EB-9674-FF3E127B10D2`|请求ID，方便foas定位问题。|
|Job| | |作业详情|
|  └JobName|String|job1|作业名称|
|  └ProjectName|String|project|项目名称|
|  └JobType|String|FLINK\_STREAM|作业种类： -   FLINK\_STREAM：流作业
-   FLINK\_BATCH：批作业

|
|  └ApiType|String|SQL|API类型： -   DATASTREAM： Datastream作业
-   SQL ：SQL作业

|
|  └Code|String|code|作业详情： SQL作业返回SQL代码；datastream作业返回foas上datdastream作业的配置。|
|  └PlanJson|String|`{a:b}`|作业上线的执行计划|
|  └Properties|String|`k:v`|作业运行配置参数|
|  └Packages|String|package1.jar|引用package名称，多个逗号分隔。|
|  └IsCommitted|Boolean|true|是否上线状态|
|  └Creator|String|xxxx|作业创建者|
|  └CreateTime|Long|1548397575000|作业创建时间|
|  └Modifier|String|xxxx|最近修改者|
|  └ModifyTime|Long|1548397575000|最近修改时间|
|  └Description|String|test|作业备注描述|
|  └EngineVersion|String|`blink_2.2.4`|引擎版本|
|  └ClusterId|String|d6wxwo5tnrmuamx2ly3m7vkz|集群ID|
|  └QueueName|String|`root.default`|队列名称|
|  └FolderId|Long|123|文件夹ID|

## GetJob请求示例 {#section_gwg_121_rgb .section}

```
http(s)://[Endpoint]/?Action=undefined
&<公共请求参数>
```

