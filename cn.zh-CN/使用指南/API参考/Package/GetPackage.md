# GetPackage {#concept_xls_cbr_tgb .concept}

GetPackage API可以获得package的详细信息。

**说明：** GetPackage API只能获取到通过web或CreatePackage接口在foas上保存过相关信息的package。

## GetPackage请求参数 {#section_nd5_qd1_rgb .section}

|参数|类型|是否必选|示例值|描述|
|packageName|String|是|`package1.jar`|package名称|
|projectName|String|是|project1|项目名称|

## GetPackage返回参数 {#section_lww_xd1_rgb .section}

|参数|类型|示例值|描述|
|RequestId|String|`FD0FF8C0-779A-45EB-9674-FF3E127B10D2`|请求ID，方便foas定位问题|
|Package| | |package详情|
|  └ProjectName|String|project1|项目名称|
|  └PackageName|String|`package1.jar`|package名称|
|  └Creator|String|xxxx|创建者|
|  └Modifier|String|xxxx|最近修改者|
|  └CreateTime|Long|1548397575000|创建时间（13位时间戳，精确到毫秒）。|
|  └ModifyTime|Long|1548397575000|最近修改时间|
|  └OriginName|String|`package2.jar`|package别名|
|  └Type|String|JAR|package 类型：-   JAR：jar包
-   DICTIONARY：普通文件
-   SCRIPT：脚本
-   PYTHON：python文件或者zip包

|
|  └Md5|String|md5zhi|package的md5值|
|  └Description|String|test|package备注描述|
|  └OssEndpoint|String|`oss-cn-hangzhou-internal.aliyuncs.com`|Oss接入点|
|  └OssBucket|String|`blinktest2.oss-cn-hangzhou-internal.aliyuncs.com`|Ossbucket|
|  └OssOwner|String|xxxx|Oss所有者|
|  └OssPath|String|`path1/path2/a.jar`|Oss路径|

## GetPackage请求示例 {#section_gwg_121_rgb .section}

```
http(s)://[Endpoint]/?Action=undefined
&<公共请求参数>
```

