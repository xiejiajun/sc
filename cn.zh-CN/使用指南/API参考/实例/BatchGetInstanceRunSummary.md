# BatchGetInstanceRunSummary {#concept_qgb_tqt_qgb .concept}

`BatchGetInstanceRunSummary`（批量获取实例运行信息）接口用来批量获取某个项目下，某一类作业（批作业或者流作业）的多个Job的运行实例情况。

## BatchGetInstanceRunSummary请求参数 {#section_a2z_qrt_qgb .section}

|参数|类型|是否必选|示例值|描述|
|jobNames|String|是|`job1,job2`|作业名称，以逗号`,`分隔。|
|jobType|String|是|`FLINK_STREAM`|作业类型，批作业使用`FLINK_BATCH`，流作业使用`FLINK_STREAM`。|
|projectName|String|是|`test_project`|项目名称|
|RegionId|String|否|`cn-hangzhou-pre`|集团内用户使用：-   `cn-hangzhou-pre` 公共云预发；
-   `cn-hangzhou-internal` 集团内生产；
-   `cn-hangzhou-internal-pre`集团内预发。

|

## BatchGetInstanceRunSummary返回参数 {#section_byg_rrt_qgb .section}

|参数|类型|示例值|描述|
|RequestId|String|`FD0FF8C0-779A-45EB-9674-FF3E127B10D2`|请求ID，无实际含义，出现问题时方便foas核查。|
|RunSummarys| | |作业运行状态详情|
|  └Id|Long|`1`|运行实例ID|
|  └ActualState|String|`RUNNING`|作业返回实际运行状态： -   WAITING 等待；
-   RUNNING 正在运行；
-   PAUSED 暂停；
-   TERMINATED 停止；
-   SUCCESS 成功（批作业）；
-   FAILED 失败（批作业）。

|
|  └ExpectState|String|`RUNNING`|作业期望状态： -   WAITING 等待；
-   RUNNING 正在运行；
-   PAUSED 暂停；
-   TERMINATED 停止；
-   SUCCESS 成功（批作业）；
-   FAILED 失败（批作业）。

|
|  └LastErrorTime|Long|`1548397575000`|上次出现错误时间（时间戳，精确到毫秒）。|
|  └LastErrorMessage|String|`error`|上次错误信息|
|  └EngineJobHandler|String|`aplication_x | xxxxx`|YARN中为作业分配的名称：ApplicationID | JobID（JM分配的ID）。|
|  └InputDelay|Long|`300`|业务延时|
|  └JobName|String|`job1`|作业名称|

## BatchGetInstanceRunSummary请求示例 {#section_s3g_vrt_qgb .section}

`https://foas.aliyuncs.com/api/v2/projects/[project_name]/runsummary?ServiceCode=foas&jobType=FLINK_STREAM&jobNames=[job1]%2[job2]`

