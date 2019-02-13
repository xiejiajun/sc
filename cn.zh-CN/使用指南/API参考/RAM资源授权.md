# RAM资源授权 {#concept_kyr_dll_qgb .concept}

默认情况下，您对自己创建的资源拥有完整的操作权限，可以使用本文档中列举的所有 API 对资源进行操作。

但在主子账号的场景下，子账号刚创建时没有资格去操作主账号的资源。需要通过 RAM 授权的方式，给予子账号操作主账号资源的权限。

在了解如何使用 RAM 来授权和访问实例之前，请确保您已详细阅读了实时计算产品文档和 [RAM 的 API 参考](http://docs.aliyun.com/?&spm=5176.730001.3.4.9ewuZb#/pub/ram/ram-api-reference/intro&intro)。

**说明：** 如果您不需要跨账户进行实例资源的授权和访问，您可以跳过此章节。跳过这些部分并不影响您对文档中其余部分的理解和使用。

## 授权策略（Policy） {#section_kln_4pm_qgb .section}

授权策略（Policy）描述授权的具体内容。授权内容主要包含效果（Effect）、资源（Resource）、对资源所授予的操作权限（Action）以及限制条件（Condition）这几个基本元素。以下内容以API网关为例，为您介绍授权策略（Policy）。

-   **系统授权策略** 

    API 网关已经预置了两个系统权限，`AliyunApiGatewayFullAccess`和`AliyunApiGatewayReadOnlyAccess`，可以在**RAM控制台** \> **策略管理** 进行查看。

    -   AliyunApiGatewayFullAccess: 管理员权限，拥有主帐号下包含API分组、API、流控策略、应用等所有资源的管理权限。
    -   AliyunApiGatewayReadOnlyAccess：可以查看主帐号下包含API 分组、API、流控策略、应用等所有资源，但不可以操作。
-   **授权策略** 

    您可以根据需要自定义管理权限，支持更为精细化的授权。可以为某个操作，也可以是某个资源。如：`GetUsers`的编辑权限。

    您可以在**RAM控制台** \> **策略管理** \> **自定义授权策略**查看已经定义好的自定义授权。

    **说明：** 

    -   自定义授权查看、创建、修改、删除方法请参见授权策略管理。
    -   授权策略内容填写方法请参见Policy 基本元素、Policy语法结构和下文的授权策略。
-   **示例:**

    ```json
    {
      "Version": "1",
      "Statement": [
        {
      "Action": "apigateway:Describe*",
          "Resource": "*",
          "Effect": "Allow"
        }
      ]
    }
    ```

    此示例表示：允许所有的查看操作。

-   **Action（操作名称列表）格式为：**

    ```
    `"Action":"<service-name>:<action-name>"`
    ```

    其中：

    -   `service-name`为：阿里云产品名称，请填写`apigateway`。
    -   `action-name`为：API 接口名称，请参照下表，支持通配符 `*`。

        ```
        "Action": "apigateway:Describe*" --表示所有的查询操作。 
        "Action": "apigateway:*" --表示API网关所有操作。 
        ```

-   **Resource（操作对象列表）** 

    Resource通常指操作对象， API网关中的API分组、流控策略、应用都被称为Resource，语法格式：

    `acs:<service-name>:<region>:<account-id>:<relative-id>`

    其中：

    -   `acs`：Alibaba Cloud Service的首字母缩写，表示阿里云的公有云平台。
    -   `service-name`为：阿里云产品名称，请填写`apigateway`。
    -   `region`：地区信息，可以使用通配符号`*`来表示所有区域。
    -   `account-id`：账号ID，比如`1234567890123456`，也可以用`*`代替。
    -   `relative-id`：与API 网关相关的资源描述部分，这部分的格式描述支持类似于一个文件路径的树状结构。
    Resource示例：

    `acs:apigateway:$regionid:$accountid:apigroup/$groupId`

    Resource语法：

    `acs:apigateway:*:$accountid:apigroup/`


## 鉴权规则 {#section_i21_2tl_qgb .section}

以API网关为例：

|action-name|资源（Resource）|
|-----------|------------|
|AbolishApi|`acs:apigateway:$regionid:$accountid:apigroup/$groupId`|

