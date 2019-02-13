# OfflineJob {#concept_cs3_4sq_tgb .concept}

OfflineJob API可以下线作业，作业需要没有运行实例。

## ListJob请求参数 {#section_nd5_qd1_rgb .section}

|参数|类型|是否必选|示例值|描述|
|jobName|String|是|job1|作业名称|
|projectName|String|是|project1|项目名称|

## ListJob返回参数 {#section_lww_xd1_rgb .section}

|参数|类型|示例值|描述|
|RequestId|String|`FD0FF8C0-779A-45EB-9674-FF3E127B10D2`|请求ID，方便foas定位问题。|

## ListJob请求示例 {#section_gwg_121_rgb .section}

```
http(s)://[Endpoint]/?Action=undefined
&<公共请求参数>
```

