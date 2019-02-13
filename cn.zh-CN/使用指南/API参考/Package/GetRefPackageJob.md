# GetRefPackageJob {#concept_wnn_2br_tgb .concept}

GetRefPackageJob API可以获取引用指定package的所有作业详情。

## GetRefPackageJob请求参数 {#section_nd5_qd1_rgb .section}

|参数|类型|是否必选|示例值|描述|
|packageName|String|是|`package1.jar`|package名称|
|projectName|String|是|project1|项目名称|

## GetRefPackageJob返回参数 {#section_lww_xd1_rgb .section}

|参数|类型|示例值|描述|
|RequestId|String|`FD0FF8C0-779A-45EB-9674-FF3E127B10D2`|请求ID，方便foas定位问题|
|PageIndex|Integer|1|返回第几页|
|PageSize|Integer|10|每页包含的job数|
|TotalCount|Long|50|总计多少job|
|TotalPage|Integer|5|总计多少页|
|Jobs| | |作业详情|
|  └JobName|String|job1|作业名称|
|  └ProjectName|String|project1|项目名称|
|  └JobType|String|FLINK\_STREAM|作业类型：FLINK\_STREAM（流作业）或FLINK\_BATCH（批作业）|
|  └ApiType|String|SQL|API类型：DATASTREAM或SQL|
|  └Code|String|code|作业code|
|  └PlanJson|String|`{a:b}`|作业上线的执行计划|
|  └Properties|String|`k:v`|作业运行参数配置|
|  └Packages|String|`package1.jar`|作业引用的package名称，多个逗号分隔|
|  └IsCommitted|Boolean|true|是否上线|
|  └Creator|String|xxxx|创建者|
|  └CreateTime|Long|1548397575000|作业创建时间|
|  └Modifier|String|xxxx|最近修改者|
|  └ModifyTime|Long|1548397575000|最近修改时间|
|  └Description|String|test|作业备注描述|
|  └EngineVersion|String|`blink_2.2.4`|引擎版本|
|  └ClusterId|String|d6wxwo5tnrmuamx2ly3m7vkz|集群ID|
|  └QueueName|String|`root.default`|队列名称|
|  └FolderId|Long|123|文件夹ID|

## GetRefPackageJob请求示例 {#section_gwg_121_rgb .section}

```
http(s)://[Endpoint]/?Action=undefined
&<公共请求参数>
```

