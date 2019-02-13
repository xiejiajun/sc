# ListJob {#concept_n5p_msq_tgb .concept}

ListJob API可以搜索某个project下满足条件的Job信息。

## ListJob请求参数 {#section_nd5_qd1_rgb .section}

|参数|类型|是否必选|示例值|描述|
|projectName|String|是|project1|项目名称|
|apiType|String|否|SQL|API类型：DATASTREAM或SQL|
|clusterId|String|否|d6wxwo5tnrmuamx2ly3m7vkz|集群ID|
|engineVersion|String|否|`blink_2.2.4`|引擎版本|
|folderId|Long|否|123|文件夹ID|
|jobName|String|否|job1|作业名称|
|jobType|String|否|FLINK\_STREAM|作业种类： -   FLINK\_STREAM：流作业
-   FLINK\_BATCH：批作业

|
|pageIndex|Integer|否|1|分页属性，第几页|
|pageSize|Integer|否|10|分页属性，每页返回job数|
|queueName|String|否|root.default|队列名称|

## ListJob返回参数 {#section_lww_xd1_rgb .section}

|参数|类型|示例值|描述|
|RequestId|String|`FD0FF8C0-779A-45EB-9674-FF3E127B10D2`|请求ID，方便foas定位问题。|
|PageIndex|Integer|1|分页属性，第几页|
|PageSize|Integer|10|分页属性，每页多少个job|
|TotalPage|Integer|5|分页属性，总页数|
|TotalCount|Long|50|分页属性，总job数|
|Jobs| | |作业详情|
|  └JobName|String|job1|作业名称|
|  └ProjectName|String|project1|项目名称|
|  └JobType|String|FLINK\_STREAM|作业种类： -   FLINK\_STREAM：流作业
-   FLINK\_BATCH：批作业

|
|  └ApiType|String|SQL|API类型：DATASTREAM或SQL|
|  └Code|String|code|作业代码|
|  └PlanJson|String|\{a:b\}|执行计划|
|  └Properties|String|k:v|作业运行配置|
|  └Packages|String|package1.jar|package名称|
|  └IsCommitted|Boolean|true|作业是否已经提交运行|
|  └Creator|String|xxxx|创建者|
|  └CreateTime|Long|1548397575000|作业创建时间|
|  └Modifier|String|xxxx|最近修改者|
|  └ModifyTime|Long|1548397575000|最近修改时间|
|  └Description|String|test|作业备注描述|
|  └EngineVersion|String|blink\_2.2.4|引擎版本|
|  └ClusterId|String|d6wxwo5tnrmuamx2ly3m7vkz|集群ID|
|  └QueueName|String|root.default|队列名称|
|  └FolderId|Long|123|文件夹ID|

## ListJob请求示例 {#section_gwg_121_rgb .section}

```
http(s)://[Endpoint]/?Action=undefined
&<公共请求参数>
```

