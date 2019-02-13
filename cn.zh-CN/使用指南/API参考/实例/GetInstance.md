# GetInstance {#concept_bgj_wd5_qgb .concept}

使用GetInstance（获取运行实例详情） API可以获取作业运行的全部信息。

## GetInstance请求参数 {#section_cmy_425_qgb .section}

|参数|类型|是否必选|示例值|描述|
|instanceId|Long|是|123|InstanceID，可以通过`ListInstance`接口、`Startjob`等接口获得，流作业当前运行实例默认填`-1`。|
|jobName|String|是|job1|作业名称|
|projectName|String|是|project1|项目名称|

## GetInstance返回参数 {#section_lhk_s25_qgb .section}

|参数|类型|示例值|描述|
|RequestId|String|`FD0FF8C0-779A-45EB-9674-FF3E127B10D2`|请求ID，方便foas定位问题。|
|Instance| | |Instance详情|
|  └Id|Long|`123`|InstanceID|
|  └ProjectName|String|`project1`|项目名称|
|  └JobName|String|`job1`|作业名称|
|  └ActualState|String|`RUNNING`|Instance的实际状态： -   WAITING：等待
-   RUNNING：运行中
-   PAUSED：暂停
-   TERMINATED：停止
-   SUCCESS：成功（批作业）
-   FAILED：失败（批作业）

|
|  └ExpectState|String|`RUNNING`|期望状态： -   RUNNING ：运行中
-   PAUSED：暂停
-   TERMINATED： 停止
-   SUCCESS：成功（批作业）
-   FAILED：失败（流作业）

|
|  └JobType|String|`FLINK_BATCH`|作业种类： -   FLINK\_STREAM：流作业
-   FLINK\_BATCH：批作业

|
|  └ApiType|String|SQL|API类型： -   DATASTREAM： Datastream作业
-   SQL ：SQL作业

|
|  └Code|String|code|Instance的运行代码。SQL作业返回SQL，datastream作业返回datastream配置。|
|  └Properties|String|`K=V`|作业运行参数|
|  └Packages|String|`package1.jar`|Instance引用的package，引用多个package逗号`,`分隔，未引用为空。|
|  └Starter|String|`starter1`|启动人|
|  └StartTime|Long|`1548397575000`|开始时间|
|  └LastErrorTime|Long|`1548397575000`|最近错误时间|
|  └LastErrorMessage|String|error|最近一次错误信息|
|  └LastOperator|String|`operator1`|最近操作人|
|  └LastOperateTime|Long|1548397575000|最近一次操作时间|
|  └PlanJson|String|\{a:b\}|执行计划|
|  └EngineVersion|String|blink\_2.2.4|引擎版本|
|  └EngineJobHandler|String|`application_xxxx | xxxx`|Instance在YARN中的名称：`ApplicaitonID | jobID（JM分配的Job ID）`。|
|  └InputDelay|Long|`100`|业务延迟（毫秒）|
|  └ClusterId|String|`d6wxwo5tnrmuamx2ly3m7vkz`|集群ID|
|  └QueueName|String|`root.default`|队列名称|
|  └EndTime|Long|`1548397575000`|作业结束时间，未结束作业返回空值。|

## GetInstance请求示例 {#section_wql_y25_qgb .section}

```
http(s)://[Endpoint]/?Action=undefined
&<公共请求参数>
```

