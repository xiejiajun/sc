# StartJob {#concept_fzz_psq_tgb .concept}

StartJob API可以启动指定项目下某个作业。

## StartJob请求参数 {#section_nd5_qd1_rgb .section}

|参数|类型|是否必选|示例值|描述|
|jobName|String|是|job1|作业名称|
|projectName|String|是|project1|项目名称|
|parameterJson|Json|否|`{"a":"b"}`|作业启动相关参数，使用json格式，参数有如startOffset（启动位点，13位时间戳）。|

## StartJob返回参数 {#section_lww_xd1_rgb .section}

|参数|类型|示例值|描述|
|RequestId|String|`FD0FF8C0-779A-45EB-9674-FF3E127B10D2`|请求ID，方便foas定位问题。|
|instanceId|Long|123|实例ID|

## StartJob请求示例 {#section_gwg_121_rgb .section}

```
http(s)://[Endpoint]/?Action=undefined
&<公共请求参数>
```

