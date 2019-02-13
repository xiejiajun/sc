# CreateJob {#concept_cjb_2sq_tgb .concept}

CreateJob API可以在指定项目下创建作业。

## CreateJob请求参数 {#section_nd5_qd1_rgb .section}

|参数|类型|是否必选|示例值|描述|
|apiType|String|是|SQL|创建API类型： -   DATASTREAM： Datastream作业
-   SQL ：SQL作业

|
|clusterId|String|是|d6wxwo5tnrmuamx2ly3m7vkz|集群ID|
|code|String|是|code1|SQL作业填写SQL代码，DataStream作业填写Foas上提交DataStream作业的相关参数。|
|engineVersion|String|是|``blink_2.2.4``|使用引擎的版本|
|jobName|String|是|job1|作业名称|
|jobType|String|是|FLINK\_STREAM|作业类型：-   FLINK\_STREAM：流作业
-   FLINK\_BATCH：批作业

|
|planJson|String|否|`{a:b}`|执行计划|
|projectName|String|是|project1|项目名称|
|queueName|String|是|`root.default`|队列名称|
|description|String|否|test|对作业相关描述备注|
|folderId|Long|是|123|文件夹ID|
|packages|String|否|`package1,package2`|jar包全名称，多个使用逗号分隔。|
|properties|String|否|`{k:v}`|作业运行的相关参数|

## CreateJob返回参数 {#section_lww_xd1_rgb .section}

|参数|类型|示例值|描述|
|RequestId|String|`FD0FF8C0-779A-45EB-9674-FF3E127B10D2`|请求ID，方便foas排查问题。|

## CreateJob请求示例 {#section_gwg_121_rgb .section}

```
http(s)://[Endpoint]/?Action=undefined
&<公共请求参数>
```

