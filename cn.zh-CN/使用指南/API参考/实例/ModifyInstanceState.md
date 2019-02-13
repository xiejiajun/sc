# ModifyInstanceState {#concept_djd_nlp_tgb .concept}

ModifyInstanceState API可以修改运行实例的状态，实现对实例的操作

## ModifyInstanceState请求参数 {#section_nd5_qd1_rgb .section}

|参数|类型|是否必选|示例值|描述|
|expectState|String|是|RUNNING|期望状态： -   RUNNING ：运行中
-   PAUSED：暂停
-   TERMINATED： 停止
-   SUCCESS：成功（批作业）
-   FAILED：失败（流作业）

|
|instanceId|Long|是|`-1`|InstanceID，流作业只有一个运行实例，此处填-1L，指在线上运行的，批作业可以通过ListInstance、Startjob等接口获得。|
|jobName|String|是|job1|作业名称|
|projectName|String|是|project1|项目名称|
|isFlush|Boolean|否|true|作业恢复时使用，确认是否使用最新配置。其他情况使用该参数无效，默认使用。|

## ModifyInstanceState返回参数 {#section_lww_xd1_rgb .section}

|参数|类型|示例值|描述|
|RequestId|String|`FD0FF8C0-779A-45EB-9674-FF3E127B10D2`|请求ID，方便foas定位问题。|

## ModifyInstanceState请求示例 {#section_gwg_121_rgb .section}

```
http(s)://[Endpoint]/?Action=undefined
&<公共请求参数>
```

