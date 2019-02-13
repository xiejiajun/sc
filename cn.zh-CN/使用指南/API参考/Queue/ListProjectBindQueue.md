# ListProjectBindQueue {#concept_dqd_qbr_tgb .concept}

ListProjectBindQueue API可以查看指定project对应的队列相关的信息。

## ListProjectBindQueue请求参数 {#section_nd5_qd1_rgb .section}

|参数|类型|是否必选|示例值|描述|
|clusterId|String|否|d6wxwo5tnrmuamx2ly3m7vkz|集群ID|
|projectName|String|是|project1|项目名称|
|queueName|String|否|root.default|队列名称|

## ListProjectBindQueue返回参数 {#section_lww_xd1_rgb .section}

|参数|类型|示例值|描述|
|RequestId|String|`FD0FF8C0-779A-45EB-9674-FF3E127B10D2`|请求ID，方便foas定位问题。|
|Queues| | |队列信息|
|  └ClusterId|String|d6wxwo5tnrmuamx2ly3m7vkz|集群ID|
|  └QueueName|String|`root.default`|集群名称|

## ListProjectBindQueue请求示例 {#section_gwg_121_rgb .section}

```
http(s)://[Endpoint]/?Action=undefined
&<公共请求参数>
```

