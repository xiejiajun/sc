# GetInstanceExceptions {#concept_o3m_jyz_qgb .concept}

GetInstanceExceptions API可以获取作业运行failover的信息。

## GetInstanceExceptions请求参数 {#section_wnz_tyz_qgb .section}

|参数|类型|是否必选|示例值|描述|
|instanceId|Long|是|`-1`|InstanceID，流作业只有一个运行实例，此处填`-1L`，指在线上运行的，批作业可以通过ListInstance、Startjob等接口获得|
|jobName|String|是|`job1`|作业名称|
|projectName|String|是|`project1`|项目名称|

## GetInstanceExceptions返回参数 {#section_ok2_wyz_qgb .section}

|参数|类型|示例值|描述|
|RequestId|String|`FD0FF8C0-779A-45EB-9674-FF3E127B10D2`|请求ID，方便foas定位问题。|
|Exceptions|String|`xxxxerror`|failover的具体信息|

## GetInstanceExceptions请求示例 {#section_qr5_yyz_qgb .section}

```
http(s)://[Endpoint]/?Action=undefined
&<公共请求参数>
```

