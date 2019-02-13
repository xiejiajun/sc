# ListChildFolder {#concept_mjp_5nq_tgb .concept}

ListChildFolder API可以获取某个主目录下所有子目录的ID和路径。

## ListChildFolder请求参数 {#section_nd5_qd1_rgb .section}

|参数|类型|是否必选|示例值|描述|
|path|String|是|`/path`|主目录路径|
|projectName|String|是|project|项目名称|

## ListChildFolder返回参数 {#section_lww_xd1_rgb .section}

|参数|类型|示例值|描述|
|RequestId|String|`FD0FF8C0-779A-45EB-9674-FF3E127B10D2`|请求ID：方便foas定位问题。|
|Folders| | |文件目录详情|
|  └FolderId|Long|123|文件夹ID|
|  └Path|String|`/path`|文件夹目录|

## ListChildFolder请求示例 {#section_gwg_121_rgb .section}

```
http(s)://[Endpoint]/?Action=undefined
&<公共请求参数>
```

