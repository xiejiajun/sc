# GetInstanceResource {#concept_fwc_s3r_tgb .concept}

GetInstanceResource API可以获取运行实例使用的CPU、Memory信息和提交时申请的CPU、Memory信息。未运行的作业调用GetInstanceResource API会报错。

## GetInstanceResource请求参数 {#section_nd5_qd1_rgb .section}

|参数|类型|是否必选|示例值|描述|
|instanceId|Long|是|-1|InstanceID，流作业只有一个运行实例，此处填`-1L`，指在线上运行的，批作业可以通过ListInstance、Startjob等接口获得|
|jobName|String|是|job1|作业名称|
|projectName|String|是|project1|项目名称|

## GetInstanceResource返回参数 {#section_lww_xd1_rgb .section}

|参数|类型|示例值|描述|
|RequestId|String|`FD0FF8C0-779A-45EB-9674-FF3E127B10D2`|请求ID，方便foas定位问题。|
|Resource| | |资源情况|
|  └TotalMB|Long|1024|实际运行使用的内存（MB）|
|  └AllocatedMB|Long|2048|作业分配的内存（MB）|
|  └TotalVirtualCores|Long|50|实际运行使用的vcore|
|  └AllocatedVirtualCores|Long|100|作业分配的vcore|

## GetInstanceResource请求示例 {#section_gwg_121_rgb .section}

```
http(s)://[Endpoint]/?Action=undefined
&<公共请求参数>
```

