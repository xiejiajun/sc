# ListInstance {#concept_ygs_sk4_tgb .concept}

ListInstance API可以获取某个project下所有实例的详细信息。

## ListInstance请求参数 {#section_nd5_qd1_rgb .section}

|参数|类型|是否必选|示例值|描述|
|jobName|String|是|job1|作业名称|
|jobType|String|是|FLINK\_STREAM|作业种类： -   FLINK\_STREAM：流作业
-   FLINK\_BATCH：批作业

|
|projectName|String|是|project1|项目名称|
|actualState|String|否|RUNNING|Instance的实际状态： -   WAITING：等待
-   RUNNING：运行中
-   PAUSED：暂停
-   TERMINATED：停止
-   SUCCESS：成功（批作业）
-   FAILED：失败（批作业）

|
|apiType|String|否|SQL|api类型：DATASTREAM，SQL。|
|endBeginTs|Long|否|1548397575000|停止时间范围上限，和停止时间下限配合使用，搜索停止时间在该范围内的运行实例。使用13位时间戳，单位到毫秒。|
|endEndTs|Long|否|1548397575000|停止时间范围下限，和停止时间上限配合使用，搜索停止时间在该范围内的运行实例。使用13位时间戳，单位到毫秒。|
|expectState|String|否|RUNNING|期望状态： -   RUNNING ：运行中
-   PAUSED：暂停
-   TERMINATED： 停止
-   SUCCESS：成功（批作业）
-   FAILED：失败（流作业）

|
|isArchived|Boolean|否|false|是否搜索归档实例，默认搜索非归档实例（默认值false）。对于流作业来说，同时只存在一个实例，所以当作业启动或者恢复运行的时候，就生成了新的实例，旧实例会被归档；对于批作业，运行结束两天的实例会被归档。|
|pageIndex|Integer|否|1|分页选项，第几页，从1开始。|
|pageSize|Integer|否|10|分页选项，每页的实例数|
|startBeginTs|Long|否|1548397575000|启动时间范围上限，和启动时间下限配合使用，搜索启动时间在该范围内的运行实例。使用13位时间戳，单位到毫秒|
|startEndTs|Long|否|1548397575000| 启动时间范围下限，和启动时间上限配合使用，搜索启动时间在该范围内的运行实例。使用13位时间戳，单位到毫秒

 |

## ListInstance返回参数 {#section_lww_xd1_rgb .section}

|参数|类型|示例值|描述|
|RequestId|String|`FD0FF8C0-779A-45EB-9674-FF3E127B10D2`|请求ID，方便foas定位问题|
|PageIndex|Integer|1|分页属性，第几页，从1开始|
|PageSize|Integer|10|分页属性，每页多少实例|
|TotalPage|Integer|5|总页数|
|TotalCount|Long|50|总实例数目|
|Instances| | |实例详情|
|  └Id|Long|123|InstanceID|
|  └ProjectName|String|project1|项目名称|
|  └JobName|String|job1|作业名称|
|  └ActualState|String|RUNNING|Instance的实际状态： -   WAITING：等待
-   RUNNING：运行中
-   PAUSED：暂停
-   TERMINATED：停止
-   SUCCESS：成功（批作业）
-   FAILED：失败（批作业）

|
|  └ExpectState|String|RUNNING|期望状态： -   RUNNING ：运行中
-   PAUSED：暂停
-   TERMINATED： 停止
-   SUCCESS：成功（批作业）
-   FAILED：失败（流作业）

|
|  └JobType|String|FLINK\_STREAM|作业种类： -   FLINK\_STREAM：流作业
-   FLINK\_BATCH：批作业

|
|  └ApiType|String|SQL|API类型：DATASTREAM；SQL|
|  └Code|String|code|实例代码|
|  └Properties|String|`k=v`|作业运行配置参数|
|  └Packages|String|`package1.jar`|Instance引用的package，多个逗号分隔，未引用为空|
|  └Starter|String|xxxx|启动人|
|  └StartTime|Long|1548397575000|作业启动时间|
|  └LastErrorTime|Long|1548397575000|最近错误时间|
|  └LastErrorMessage|String|error|最近一次错误信息|
|  └LastOperator|String|xxxx|最近操作者|
|  └LastOperateTime|Long|1548397575000|最近操作时间|
|  └PlanJson|String|`{a:b}`|作业上线执行计划|
|  └EngineVersion|String|`blink_2.2.4`|引擎版本|
|  └EngineJobHandler|String|application\_xxxx | xxxx|Instance在YARN中的名称：`ApplicaitonID | jobID（JM分配的Job ID）`。|
|  └InputDelay|Long|20|业务延时（毫秒）|
|  └ClusterId|String|d6wxwo5tnrmuamx2ly3m7vkz|集群ID|
|  └QueueName|String|root.default|队列名称|
|  └EndTime|Long|1548397575000|结束时间|

## ListInstance请求示例 {#section_gwg_121_rgb .section}

```
http(s)://[Endpoint]/?Action=undefined
&<公共请求参数>
```

