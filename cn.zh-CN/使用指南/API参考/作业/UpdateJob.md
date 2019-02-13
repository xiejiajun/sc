# UpdateJob {#concept_bp3_rsq_tgb .concept}

UpdateJob API可以实现对作业属性的更新。

## UpdateJob请求参数 {#section_nd5_qd1_rgb .section}

|参数|类型|是否必选|示例值|描述|
|clusterId|String|否|d6wxwo5tnrmuamx2ly3m7vkz|集群ID|
|code|String|否|code|作业代码|
|engineVersion|String|否|`blink_2.2.4`|引擎版本|
|jobName|String|是|job1|作业名称|
|planJson|String|否|`{a:b}`|作业执行计划|
|projectName|String|是|project1|项目名称|
|queueName|String|否|`root.default`|队列名称|
|description|String|否|test|作业备注描述|
|folderId|Long|否|123|文件夹ID|
|packages|String|否|`package1.jar`|package名称|
|properties|String|否|`k:v`|作业运行配置参数|

## UpdateJob返回参数 {#section_lww_xd1_rgb .section}

|参数|类型|示例值|描述|
|RequestId|String|`FD0FF8C0-779A-45EB-9674-FF3E127B10D2`|请求ID，方便foas定位问题。|

## UpdateJob请求示例 {#section_gwg_121_rgb .section}

```
http(s)://[Endpoint]/?Action=undefined
&<公共请求参数>
```

