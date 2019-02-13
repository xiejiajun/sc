# GetInstanceCheckpoint {#concept_bzh_btz_qgb .concept}

GetInstanceCheckpoint API可以获得正在运行实例作业的checkpoint，非运行状态的作业调用该方法会返回错误。

## GetInstanceCheckpoint请求参数 {#section_ajt_ptz_qgb .section}

|参数|类型|是否必选|示例值|描述|
|instanceId|Long|是|`-1`|InstanceID，流作业只有一个运行实例，此处填`-1L`，指在线上运行的，批作业可以通过ListInstance、Startjob等接口获得。|
|jobName|String|是|`job1`|作业名称|
|projectName|String|是|`project1`|项目名称|

## GetInstanceCheckpoint返回参数 {#section_y5j_rtz_qgb .section}

|参数|类型|示例值|描述|
|RequestId|String|`FD0FF8C0-779A-45EB-9674-FF3E127B10D2`|请求ID，方便foas定位问题。|
|Checkpoints|String|`{a:b}`|checkpint的具体信息|

## GetInstanceCheckpoint请求示例 {#section_x5l_p5z_qgb .section}

```language-java
http(s)://[Endpoint]/?Action=undefined
&<公共请求参数>
```

