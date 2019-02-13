# CheckRawPlanJson {#concept_ejb_fpq_tgb .concept}

CheckRawPlanJson和CheckRawPlanJson接口组合调用以获取某个作业的planjson，CheckRawPlanJson接口发出调用请求后会返回一个session ID，CheckRawPlanJson获取这个session ID可以查询到planjson的详细情况。使用时建议循环调用CheckRawPlanJson方法直到成功获取。

## CheckRawPlanJson请求参数 {#section_nd5_qd1_rgb .section}

|参数|类型|是否必选|示例值|描述|
|jobName|String|是|job1|单个作业名称|
|projectName|String|是|project1|项目名称|
|sessionId|String|是|xxxxxx|当使用CheckRawPlanJson发送请求后，会返回一个sessionId，将该sessionId填在此处。|

## CheckRawPlanJson返回参数 {#section_lww_xd1_rgb .section}

|参数|类型|示例值|描述|
|RequestId|String|`FD0FF8C0-779A-45EB-9674-FF3E127B10D2`|请求ID，方便foas定位问题|
|PlanJsonInfo| | |planjson详情|
|  └Status|String|success|GetRawPlanJson的执行状态： -   Session in run：执行中
-   success ：成功
-   fail：失败

|
|  └PlanJson|String|`{a:b}`|执行计划|
|  └ErrorMessage|String|eror|错误信息|

## CheckRawPlanJson请求示例 {#section_gwg_121_rgb .section}

```
http(s)://[Endpoint]/?Action=CheckRawPlanJson 
&<公共请求参数>
```

