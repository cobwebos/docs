---
title: 使用 Azure 数据工厂从 SharePoint Online 列表复制数据
description: 了解如何通过在 Azure 数据工厂管道中使用复制活动，将数据从 SharePoint Online 列表中复制到受支持的接收器数据存储。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: jingwang
ms.openlocfilehash: f560a01c4ec00649157a9c43aedf0ed6cfc2e050
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "83871917"
---
# <a name="copy-data-from-sharepoint-online-list-by-using-azure-data-factory"></a>使用 Azure 数据工厂从 SharePoint Online 列表复制数据
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述如何使用 Azure 数据工厂中的复制活动从 SharePoint Online 列表复制数据。 本文是根据总体概述复制活动的 [Azure 数据工厂中的复制活动](copy-activity-overview.md)编写的。

## <a name="supported-capabilities"></a>支持的功能

以下活动支持此 SharePoint Online 列表连接器：

- 包含[支持的源/接收器矩阵](copy-activity-overview.md)的 [Copy 活动](copy-activity-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)

可以将数据从 SharePoint Online 列表复制到任何受支持的接收器数据存储。 有关复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储和格式](copy-activity-overview.md#supported-data-stores-and-formats)。

具体而言，此 SharePoint Online 列表连接器使用服务主体身份验证，并通过 OData 协议检索数据。

> [!TIP]
> 此连接器支持从 SharePoint Online 列表而不是文件复制数据。 通过[从 SharePoint Online 复制文件](#copy-file-from-sharepoint-online)部分了解如何复制文件。

## <a name="prerequisites"></a>先决条件

SharePoint Online 列表连接器使用服务主体身份验证连接到 SharePoint。 请按照以下步骤进行设置：

1. 遵循[将应用程序注册到 Azure AD 租户](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant)，在 Azure Active Directory (Azure AD) 中注册一个应用程序实体。 记下下面的值，这些值用于定义链接服务：

    - 应用程序 ID
    - 应用程序密钥
    - 租户 ID

2. 向您注册的应用程序授予 SharePoint Online 网站权限： 

    > [!NOTE]
    > 此操作需要 SharePoint Online 网站所有者权限。 可以通过如下步骤找到所有者：转到网站主页 -> 单击右角的“X 成员”-> 检查谁拥有“所有者”角色。

    1. 打开 SharePoint Online 网站链接，例如 `https://[your_site_url]/_layouts/15/appinv.aspx`（替换网站 URL）。
    2. 搜索已注册的应用程序 ID，填写空字段，然后单击“创建”。

        - 应用程序域：`localhost.com`
        - 重定向 URL：`https://www.localhost.com`
        - 权限请求 XML：

        ```xml
        <AppPermissionRequests AllowAppOnlyPolicy="true">
            <AppPermissionRequest Scope="http://sharepoint/content/sitecollection/web" Right="Read"/>
        </AppPermissionRequests>
        ```

        ![SharePoint 授予权限](media/connector-sharepoint-online-list/sharepoint-online-grant-permission.png)

    3. 对此应用单击“信任它”。

## <a name="get-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

对于特定于 SharePoint Online 列表连接器的数据工厂实体，以下部分提供了有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

SharePoint Online 列表链接的服务支持以下属性：

| **属性**        | **说明**                                              | **必需** |
| ------------------- | ------------------------------------------------------------ | ------------ |
| type                | type 属性必须设置为：SharePointOnlineList。 ****  | 是          |
| siteUrl             | SharePoint Online 网站 URL，例如 `https://contoso.sharepoint.com/sites/siteName`。 | 是          |
| servicePrincipalId  | 在 Azure Active Directory 中注册的应用程序的应用程序（客户端）ID。 | 是          |
| servicePrincipalKey | 应用程序的密钥。 将此字段标记为 **SecureString** 以安全地将其存储在数据工厂中或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 | 是          |
| tenantId            | 应用程序所在的租户 ID。          | 是          |
| connectVia          | 用于连接到数据存储的 [ Integration Runtime](concepts-integration-runtime.md)。 请参阅本文前面的[先决条件](#prerequisites)，了解更多信息。 如果未指定，则使用默认 Azure Integration Runtime。 | 否           |

**示例：**

```json
{
    "name": "SharePointOnlineList",
    "properties": {
        "type": "SharePointOnlineList",
        "typeProperties": {
            "siteUrl": "<site URL>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenantId": "<tenant ID>"
        }
    }
}
```

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集和链接服务](concepts-datasets-linked-services.md)。 以下部分提供 SAP 表数据集支持的属性列表。

| properties | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为 SharePointOnlineLResource。  | 是 |
| listName | SharePoint Online 列表的名称。 | 是 |

**示例**

```json
{
    "name": "SharePointOnlineListDataset",
    "properties":
    {
        "type": "SharePointOnlineListResource",
        "linkedServiceName": {
            "referenceName": "<SharePoint Online List linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties":
        {
            "listName": "<name of the list>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各个部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)。  以下部分提供 SharePoint Online 列表源支持的属性列表。

### <a name="sharepoint-online-list-as-source"></a>SharePoint Online 列表作为源

若要从 SharePoint Online 列表复制数据，复制活动“源”部分支持以下属性：

| properties | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动源的 type 属性必须设置为 SharePointOnlineListSource。  | 是 |
| query | 用于筛选数据的自定义 OData 查询选项。 示例：`"$top=10&$select=Title,Number"`。 | 否 |
| httpRequestTimeout | 用于获取响应的 HTTP 请求的超时（以秒为单位）。 默认值为 300（5 分钟）。 | 否 |

**示例**

```json
"activities":[
    {
        "name": "CopyFromSharePointOnlineList",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SharePoint Online List input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SharePointOnlineListSource",
                "query": "<OData query e.g. $top=10&$select=Title,Number>"
            }, 
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sharepoint-online-list"></a>SharePoint Online 列表的数据类型映射

从 SharePoint Online 列表复制数据时，会在 SharePoint Online 列表数据类型和 Azure 数据工厂临时数据类型之间使用以下映射。 

| SharePoint Online 数据类型                 | OData 数据类型                                  | Azure 数据工厂临时数据类型 |
| ----------------------------------------------- | ---------------------------------------------------- | ---------------------------------------- |
| 单个文本行                             | Edm.String                                           | 字符串                                   |
| 多行文本                          | Edm.String                                           | 字符串                                   |
| 选项（可供选择的菜单）                    | Edm.String                                           | 字符串                                   |
| 数字（1、1.0、100）                            | Edm.Double                                           | Double                                   |
| 货币（$、¥、€）                              | Edm.Double                                           | Double                                   |
| 日期和时间                                   | Edm.DateTime                                         | DateTime                                 |
| 查阅项（此网站已有的信息）       | Edm.Int32                                            | Int32                                    |
| 是/否（复选框）                              | Edm.Boolean                                          | Boolean                                  |
| 个人或团体                                 | Edm.Int32                                            | Int32                                    |
| 超链接或图片                            | Edm.String                                           | 字符串                                   |
| 计算（基于其他列的计算） | Edm.String/Edm.Double/Edm.DateTime/Edm.Boolean | 字符串/Double/DateTime/Boolean     |
| 附件                                      | 不支持                                        |                                          |
| 任务结果                                    | 不支持                                        |                                          |
| 外部数据                                   | 不支持                                        |                                          |
| 托管元数据                                | 不支持                                        |                                          |

## <a name="copy-file-from-sharepoint-online"></a>从 SharePoint Online 复制文件

您可以使用以下方法从 SharePoint Online 复制文件：使用 Web 活动进行身份验证并从 SPO 中获取访问令牌，然后传递到后续的复制活动以使用 HTTP 连接器作为源复制数据。  

![SharePoint 复制文件流](media/connector-sharepoint-online-list/sharepoint-online-copy-file-flow.png)

1. 按照[先决条件](#prerequisites)部分，创建 AAD 应用程序并向 SharePoint Online 授予权限。 

2. 创建 Web 活动以从 SharePoint Online 获取访问令牌：

    - URL：`https://accounts.accesscontrol.windows.net/[Tenant-ID]/tokens/OAuth/2` 替换租户 ID。
    - **方法**：POST
    - 标头：
        - 内容类型：application/x-www-form-urlencoded
    - 正文：`grant_type=client_credentials&client_id=[Client-ID]@[Tenant-ID]&client_secret=[Client-Secret]&resource=00000003-0000-0ff1-ce00-000000000000/[Tenant-Name].sharepoint.com@[Tenant-ID]` 替换客户端 ID、客户端密码、租户 ID 和租户名称。

    > [!CAUTION]
    > 在 Web 活动中，将“安全输出”选项设置为 true，以防止令牌值以纯文本格式记录。 使用此值的任何其他活动都应将其“安全输入”选项设置为 true。

3. 与 HTTP 连接器作为源的复制活动链接以复制 SharePoint Online 文件内容：

    - HTTP 链接服务：
        - 基 URL：`https://[site-url]/_api/web/GetFileByServerRelativeUrl('[relative-path-to-file]')/$value` 替换网站 URL 和文件的相对路径。 文件的相对路径示例为 `/sites/site2/Shared Documents/TestBook.xlsx`。
        - 身份验证类型：匿名（稍后使用复制活动源中配置的持有者令牌）
    - 数据集：选择所需的格式。 若要按原样复制文件，请选择“二进制”类型。
    - 复制活动源：
        - 请求方法：GET
        - 其他标头：使用以下表达式`@{concat('Authorization: Bearer ', activity('<Web-activity-name>').output.access_token)}`，该表达式将上游 Web 活动生成的持有者令牌用作授权标头。 替换 Web 活动名称。
    - 照常配置复制活动接收器。

## <a name="lookup-activity-properties"></a>“查找”活动属性

若要详细了解这些属性，请查看[“查找”活动](control-flow-lookup-activity.md)。

## <a name="next-steps"></a>后续步骤

有关 Azure 数据工厂中复制活动支持用作源和接收器的数据存储的列表，请参阅[支持的数据存储和格式](copy-activity-overview.md#supported-data-stores-and-formats)。
