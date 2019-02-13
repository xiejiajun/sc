# DeletePackage {#concept_clp_1br_tgb .concept}

DeletePackage API可以删除foas上通过createpackage保存的oss信息，删除后foas无法再获取对应的package信息。

**说明：** 使用DeletePackage API删除foas上不存在的oss package信息时系统会报错。已经上线的作业如已经引用该package，则删除时也会报错。

## DeletePackage请求参数 {#section_nd5_qd1_rgb .section}

|参数|类型|是否必选|示例值|描述|
|packageName|String|是|`package.jar`|package名称|
|projectName|String|是|project1|项目名称|

## DeletePackage返回参数 {#section_lww_xd1_rgb .section}

|参数|类型|示例值|描述|
|RequestId|String|`FD0FF8C0-779A-45EB-9674-FF3E127B10D2`|请求ID，方便foas定位问题。|

## DeletePackage请求示例 {#section_gwg_121_rgb .section}

```
http(s)://[Endpoint]/?Action=undefined
&<公共请求参数>
```

