# CommitJob {#concept_hmh_yqq_tgb .concept}

CommitJob API可以提交项目下作业的运行请求。

## CommitJob请求参数 {#section_nd5_qd1_rgb .section}

|参数|类型|是否必选|示例值|描述|
|jobName|String|是|job1|作业名称|
|projectName|String|是|project1|项目名称|

## CommitJob返回参数 {#section_lww_xd1_rgb .section}

|参数|类型|示例值|描述|
|RequestId|String|`FD0FF8C0-779A-45EB-9674-FF3E127B10D2`|请求ID，方便foas排查问题。|

## CommitJob请求示例 {#section_gwg_121_rgb .section}

```
https://[endpoint]/api/v2/projects/[projectName]/jobs/[jobName]/commit
&<公共请求参数>
```

