# CreateFolder {#concept_jsk_4np_tgb .concept}

CreateFolder API可以为您为在项目对应的路径下创建文件夹，并在foas上保存文件夹ID，路径。

**说明：** 作业在foas控制台上显示的路径都是在作业开发目录之下，实际填写path的使用并不显示作业开发目录，作业开发目录对应根目录`/`。

## CreateFolder请求参数 {#section_nd5_qd1_rgb .section}

|参数|类型|是否必选|示例值|描述|
|path|String|是|`/path`|文件夹路径|
|projectName|String|是|project1|项目名称|

## CreateFolder返回参数 {#section_lww_xd1_rgb .section}

|参数|类型|示例值|描述|
|RequestId|String|`FD0FF8C0-779A-45EB-9674-FF3E127B10D2`|请求ID，方便foas定位问题。|
|FolderId|Long|123|文件夹ID|

## CreateFolder请求示例 {#section_gwg_121_rgb .section}

```
http(s)://[Endpoint]/api/v2/projects/[projectName]/folders 
&<公共请求参数>
```

