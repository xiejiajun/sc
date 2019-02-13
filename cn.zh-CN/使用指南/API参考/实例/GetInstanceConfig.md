# GetInstanceConfig {#concept_tdd_hvz_qgb .concept}

GetInstanceConfig API可以获取到运行实例的运行相关参数，返回值Config中是kv对，可以提取自己想要的运行参数。对于没有运行的作业调用本方法会报错。

|参数|类型|是否必选|示例值|描述|
|instanceId|Long|是|`-1`|InstanceID，流作业只有一个运行实例，此处填`-1L`，指在线上运行的，批作业可以通过ListInstance、Startjob等接口获得。|
|jobName|String|是|`job1`|作业名称|
|projectName|String|是|`project1`|项目名称|

## GetInstanceConfig返回参数 {#section_gvd_vvz_qgb .section}

|参数|类型|示例值|描述|
|RequestId|String|`FD0FF8C0-779A-45EB-9674-FF3E127B10D2`|请求ID，方便foas定位问题。|
|Config|String|`{a:b}`|作业运行的相关参数。|

## GetInstanceConfig请求示例 {#section_xpg_xvz_qgb .section}

```
http(s)://[Endpoint]/?Action=undefined
&<公共请求参数>
```

