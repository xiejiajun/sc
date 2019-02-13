# ValidateJob {#concept_jf5_tsq_tgb .concept}

ValidateJob API可以对作业进行语法检查。

## ValidateJob请求参数 {#section_nd5_qd1_rgb .section}

|参数|类型|是否必选|示例值|描述|
|jobName|String|是|job1|作业名称|
|projectName|String|是|project1|项目名称|

## ValidateJob返回参数 {#section_lww_xd1_rgb .section}

|参数|类型|示例值|描述|
|RequestId|String|`FD0FF8C0-779A-45EB-9674-FF3E127B10D2`|请求ID，方便foas定位问题。|
|JobInOut| | |作业输入输出|
|  └Inputs| | |输入数组信息|
|    └Type|String|sls|表类型|
|    └Workspace|String|xxxx|表所属项目|
|    └Name|String|xxxx|表名|
|    └Properties|Map|`k:v`|配置组|
|  └Outputs| | |返回数组信息|
|    └Type|String|sls|表类型|
|    └Workspace|String|xxxx|表所属项目|
|    └Name|String|xxxx|表名|
|    └Properties|Map|`k:v`|配置组|

## ValidateJob请求示例 {#section_gwg_121_rgb .section}

```
http(s)://[Endpoint]/?Action=undefined
&<公共请求参数>
```

