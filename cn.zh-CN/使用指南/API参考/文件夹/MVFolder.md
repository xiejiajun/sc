# MVFolder {#concept_hdl_p4q_tgb .concept}

MVFolder API可以移动文件夹，需要注意的是非空文件夹移动时，会将文件夹下的作业一起移动。

## MVFolder请求参数 {#section_nd5_qd1_rgb .section}

|参数|类型|是否必选|示例值|描述|
|destPath|String|是|`/folder1`|目标路径|
|projectName|String|是|p|项目名称|
|srcPath|String|是|`/dets`|原路径|

## MVFolder返回参数 {#section_lww_xd1_rgb .section}

|参数|类型|示例值|描述|
|RequestId|String|`FD0FF8C0-779A-45EB-9674-FF3E127B10D2`|请求ID，方便foas定位问题。|

## MVFolder请求示例 {#section_gwg_121_rgb .section}

```
http(s)://[Endpoint]/?destPath=/folder1
&projectName=p
&RegionId=region
&srcPath=/dets
&<公共请求参数>
```

