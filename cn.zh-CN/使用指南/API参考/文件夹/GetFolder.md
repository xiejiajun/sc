# GetFolder {#concept_qql_bnq_tgb .concept}

GetFolder API能够获取文件夹信息，保留文件夹ID和路径。

## GetFolder请求参数 {#section_nd5_qd1_rgb .section}

|参数|类型|是否必选|示例值|描述|
|path|String|是|`/path1/path2`|文件夹路径|
|projectName|String|是|project1|项目名称|

## GetFolder返回参数 {#section_lww_xd1_rgb .section}

|参数|类型|示例值|描述|
|RequestId|String|`FD0FF8C0-779A-45EB-9674-FF3E127B10D2`|请求ID，方便foas定位问题。|
|Folder| | |文件夹详情|
|  └FolderId|Long|123|文件夹ID|
|  └Path|String|`/path1/path2`|文件夹路径|

## GetFolder请求示例 {#section_gwg_121_rgb .section}

```
http(s)://[Endpoint]/?path=/aliyun
&projectName=myproject
&RegionId=reg
&<公共请求参数>
```

