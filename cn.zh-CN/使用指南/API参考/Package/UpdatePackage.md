# UpdatePackage {#concept_bp5_hbr_tgb .concept}

UpdatePackage API可以更新foas上保存的指定项目下package相关信息。

## UpdatePackage请求参数 {#section_nd5_qd1_rgb .section}

|参数|类型|是否必选|示例值|描述|
|ossBucket|String|否|`blinktest2.oss-cn-hangzhou-internal.aliyuncs.com`|ossBucket|
|ossEndpoint|String|否|`oss-cn-hangzhou-internal.aliyuncs.com`|oss接入点|
|ossOwner|String|否|xxxx|oss所有者|
|ossPath|String|否|`path1/path2/a.jar`|oss路径|
|packageName|String|是|`package1.jar`|package名称|
|projectName|String|是|project1|项目名称|
|description|String|否|test|package注释描述|
|md5|String|否|md5值|package的md5值|
|originName|String|否|`package2.jar`|package别名|
|tag|String|否|xxxx|package的标记|

## UpdatePackage返回参数 {#section_lww_xd1_rgb .section}

|参数|类型|示例值|描述|
|RequestId|String|`FD0FF8C0-779A-45EB-9674-FF3E127B10D2`|请求ID，方便foas定位问题。|

## UpdatePackage请求示例 {#section_gwg_121_rgb .section}

```
http(s)://[Endpoint]/?Action=undefined
&<公共请求参数>
```

