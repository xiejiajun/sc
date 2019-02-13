# GetInstanceRunSummary {#concept_qrg_fwn_tgb .concept}

GetInstanceRunSummary API是getInstance接口的轻量级版本，会返回作业运行时的部分信息，相比getInstance接口更加轻量级。

## GetInstanceMetric请求参数 {#section_nd5_qd1_rgb .section}

|参数|类型|是否必选|示例值|描述|
|instanceId|Long|是|`-1`|InstanceID，流作业只有一个运行实例，此处填`-1L`，指在线上运行的，批作业可以通过`ListInstance`、`Startjob`等接口获得。|
|jobName|String|是|`job1`|作业名称|
|projectName|String|是|`project1`|项目名称|

## GetInstanceMetric返回参数 {#section_lww_xd1_rgb .section}

|参数|类型|示例值|描述|
|RequestId|String|`FD0FF8C0-779A-45EB-9674-FF3E127B10D2`|请求ID，方便foas定位问题|
|RunSummary| | |运行概要信息|
|  └Id|Long|`123`|InstanceID|
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
|  └LastErrorTime|Long|`1548397575000`|最近错误时间|
|  └LastErrorMessage|String|`error`|最近错误信息|
|  └EngineJobHandler|String|`application_xxxxx | xxxxx`|Instance在YARN中的名称：`ApplicaitonID | jobID（JM分配的Job ID）`。|
|  └InputDelay|Long|`20`|业务延时（毫秒）|
|  └JobName|String|`job1`|作业名称|

## GetInstanceMetric请求示例 {#section_gwg_121_rgb .section}

```
http(s)://[Endpoint]/?Action=undefined
&<公共请求参数>
```

