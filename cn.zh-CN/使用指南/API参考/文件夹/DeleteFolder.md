# DeleteFolder {#concept_afl_2mq_tgb .concept}

DeleteFolder API可以删除空文件夹，对于非空文件夹会报错。

## DeleteFolder请求参数 {#section_nd5_qd1_rgb .section}

|参数|类型|是否必选|示例值|描述|
|path|String|是|`/path1/path2`|文件夹路径|
|projectName|String|是|project1|项目名称|

## DeleteFolder返回参数 {#section_lww_xd1_rgb .section}

|参数|类型|示例值|描述|
|RequestId|String|`FD0FF8C0-779A-45EB-9674-FF3E127B10D2`|请求ID，方便foas定位问题。|

## DeleteFolder请求示例 {#section_gwg_121_rgb .section}

```
http(s)://[Endpoint]/?Action=undefined
&<公共请求参数>
```

