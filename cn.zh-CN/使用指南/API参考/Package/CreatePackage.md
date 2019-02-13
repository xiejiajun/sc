# CreatePackage {#concept_bbs_s1r_tgb .concept}

CreatePackage API可以获取到您在OSS中的package，将该package传送到作业的执行节点上来。

**说明：** package的版本信息由您自行维护，上传时可以选填md5值，foas协助校验。公有云共享集群无法使用CreatePackage API，独享集群用户只能使用创建集群时的OSS信息，填写时无需填写完整信息（foas可以自动读取），只需要填写Osspath。

## CreatePackage请求参数 {#section_nd5_qd1_rgb .section}

|参数|类型|是否必选|示例值|描述|
|ossBucket|String|否|`blinktest2.oss-cn-hangzhou-internal.aliyuncs.com`|OSSBucket|
|ossEndpoint|String|否|`oss-cn-hangzhou-internal.aliyuncs.com`|OSS接入点|
|ossOwner|String|否|user1|OSS所有者|
|ossPath|String|是|`path1/path2/a.jar`|package在oss中的路径|
|packageName|String|是|`package1.jar`|package名称|
|projectName|String|是|project1|项目名称|
|type|String|是|JAR|package 类型：-   JAR：jar包
-   DICTIONARY：普通文件
-   SCRIPT：脚本
-   PYTHON：python文件或者zip包

|
|description|String|否|test|package的备注描述|
|md5|String|否|md5值|package的md5值|
|originName|String|否|package2|package别名|

## CreatePackage返回参数 {#section_lww_xd1_rgb .section}

|参数|类型|示例值|描述|
|RequestId|String|`FD0FF8C0-779A-45EB-9674-FF3E127B10D2`|请求ID，方便foas定位问题。|

## CreatePackage请求示例 {#section_gwg_121_rgb .section}

```
http(s)://[Endpoint]/?Action=undefined
&<公共请求参数>
```

