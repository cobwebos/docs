---
title: "了解如何在逻辑应用中使用 SharePoint Online 连接器 | Microsoft Docs"
description: "使用 SharePoint Online 连接器创建逻辑应用以管理 SharePoint 上的列表。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: e0ec3149-507a-409d-8e7b-d5fbded006ce
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/19/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 76a37a567ae077e3d0fee7a1bc7f763d4d1c7cf2


---
# <a name="get-started-with-the-sharepoint-online-connector"></a>SharePoint Online 连接器入门
使用 SharePoint Online 连接器管理 SharePoint 列表。  

若要使用“任何连接器”[](apis-list.md)，首先需要创建逻辑应用。 可通过“立即创建逻辑应用”[](../app-service-logic/app-service-logic-create-a-logic-app.md)开始操作。

## <a name="connect-to-sharepoint-online"></a>连接到 SharePoint Online
需要先创建到任何服务的*连接*，然后逻辑应用才能访问该服务。 [连接](connectors-overview.md)提供逻辑应用和其他服务之间的连接性。  

### <a name="create-a-connection-to-sharepoint-online"></a>创建到 SharePoint Online 的连接
> [!INCLUDE [Steps to create a connection to SharePoint](../../includes/connectors-create-api-sharepointonline.md)]
> 
> 

## <a name="use-a-sharepoint-online-trigger"></a>使用 SharePoint Online 触发器
触发器是用于启动在逻辑应用中定义的工作流的事件。 [了解有关触发器的详细信息](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts)。  

> [!INCLUDE [Steps to create a SharePoint Online trigger](../../includes/connectors-create-api-sharepointonline-trigger.md)]
> 
> 

## <a name="use-a-sharepoint-online-action"></a>使用 SharePoint Online 操作
操作是指在逻辑应用中定义的由工作流执行的操作。 [了解有关操作的详细信息](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts)。  

> [!INCLUDE [Steps to create a SharePoint Online action](../../includes/connectors-create-api-sharepointonline-action.md)]
> 
> 

## <a name="technical-details"></a>技术详细信息
下面详细介绍了此连接支持的触发器、操作和响应：

## <a name="sharepoint-online-triggers"></a>SharePoint Online 触发器
SharePoint 具有以下触发器：  

| 触发器 | 说明 |
| --- | --- |
| [创建文件时](connectors-create-api-sharepointonline.md#when-a-file-is-created) |此操作在 SharePoint 文件夹中新建文件时触发流。 |
| [修改文件时](connectors-create-api-sharepointonline.md#when-a-file-is-modified) |此操作在 SharePoint 文件夹中修改文件时触发流。 |
| [新建项目时](connectors-create-api-sharepointonline.md#when-a-new-item-is-created) |此操作在 SharePoint 列表中新建项目时触发流。 |
| [修改现有项目时](connectors-create-api-sharepointonline.md#when-an-existing-item-is-modified) |此操作在 SharePoint 列表中修改现有项目时触发流。 |

## <a name="sharepoint-online-actions"></a>SharePoint Online 操作
SharePoint 具有以下操作：

| 操作 | 说明 |
| --- | --- |
| [获取文件元数据](connectors-create-api-sharepointonline.md#get-file-metadata) |此操作使用文件 ID 获取文件元数据。 |
| [更新文件](connectors-create-api-sharepointonline.md#update-file) |此操作更新文件内容。 |
| [删除文件](connectors-create-api-sharepointonline.md#delete-file) |此操作可删除文件。 |
| [使用路径获取文件元数据](connectors-create-api-sharepointonline.md#get-file-metadata-using-path) |此操作使用文件路径获取文件元数据。 |
| [使用路径获取文件内容](connectors-create-api-sharepointonline.md#get-file-content-using-path) |此操作使用文件路径获取文件内容。 |
| [获取文件内容](connectors-create-api-sharepointonline.md#get-file-content) |此操作使用文件 ID 获取文件内容。 |
| [创建文件](connectors-create-api-sharepointonline.md#create-file) |此操作将文件上传到 SharePoint 站点。 |
| [复制文件](connectors-create-api-sharepointonline.md#copy-file) |此操作将文件复制到 SharePoint 站点。 |
| [列出文件夹](connectors-create-api-sharepointonline.md#list-folder) |此操作获取包含在 SharePoint 文件夹中的文件。 |
| [列出根文件夹](connectors-create-api-sharepointonline.md#list-root-folder) |此操作获取根 SharePoint 文件夹中的文件。 |
| [提取文件夹](connectors-create-api-sharepointonline.md#extract-folder) |此操作将存档文件提取到 SharePoint 文件夹中（示例：.zip）。 |
| [获取项目](connectors-create-api-sharepointonline.md#get-items) |此操作从 SharePoint 列表中获取项目。 |
| [创建项目](connectors-create-api-sharepointonline.md#create-item) |此操作在 SharePoint 列表中新建项目。 |
| [获取项目](connectors-create-api-sharepointonline.md#get-item) |此操作从 SharePoint 列表中按 ID 获取单个项目。 |
| [删除项目](connectors-create-api-sharepointonline.md#delete-item) |此操作从 SharePoint 列表中删除项目。 |
| [更新项目](connectors-create-api-sharepointonline.md#update-item) |此操作在 SharePoint 列表中更新项目。 |
| [获取实体值](connectors-create-api-sharepointonline.md#get-entity-values) |此操作获取有关 SharePoint 实体的可能值。 |
| [获取列表](connectors-create-api-sharepointonline.md#get-lists) |此操作从站点获取 SharePoint 列表。 |

### <a name="action-details"></a>操作详细信息
下面详细介绍了此连接器的操作和触发器及其响应：

### <a name="get-file-metadata"></a>获取文件元数据
此操作使用文件 ID 获取文件元数据。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| dataset* |站点 URL |SharePoint 站点 URL，如 http://contoso.sharepoint.com/sites/mysite |
| id* |文件标识符 |选择文件 |

* 指示属性是必需的

#### <a name="output-details"></a>输出详细信息
BlobMetadata

| 属性名称 | 数据类型 |
| --- | --- | --- |
| ID |字符串 |
| Name |字符串 |
| DisplayName |字符串 |
| 路径 |字符串 |
| LastModified |字符串 |
| 大小 |integer |
| MediaType |字符串 |
| IsFolder |布尔值 |
| ETag |字符串 |
| FileLocator |字符串 |

### <a name="update-file"></a>更新文件
此操作更新文件内容。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| dataset* |站点 URL |SharePoint 站点 URL，如 http://contoso.sharepoint.com/sites/mysite |
| id* |文件标识符 |选择文件 |
| body* |文件内容 |文件的内容 |

* 指示属性是必需的

#### <a name="output-details"></a>输出详细信息
BlobMetadata

| 属性名称 | 数据类型 |
| --- | --- | --- |
| ID |字符串 |
| Name |字符串 |
| DisplayName |字符串 |
| 路径 |字符串 |
| LastModified |字符串 |
| 大小 |integer |
| MediaType |字符串 |
| IsFolder |布尔值 |
| ETag |字符串 |
| FileLocator |字符串 |

### <a name="delete-file"></a>删除文件
此操作可删除文件。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| dataset* |站点 URL |SharePoint 站点 URL，如 http://contoso.sharepoint.com/sites/mysite |
| id* |文件标识符 |选择文件 |

* 指示属性是必需的

### <a name="get-file-metadata-using-path"></a>使用路径获取文件元数据
此操作使用文件路径获取文件元数据。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| dataset* |站点 URL |SharePoint 站点 URL，如 http://contoso.sharepoint.com/sites/mysite |
| path* |文件路径 |选择文件 |

* 指示属性是必需的

#### <a name="output-details"></a>输出详细信息
BlobMetadata

| 属性名称 | 数据类型 |
| --- | --- | --- |
| ID |字符串 |
| Name |字符串 |
| DisplayName |字符串 |
| 路径 |字符串 |
| LastModified |字符串 |
| 大小 |integer |
| MediaType |字符串 |
| IsFolder |布尔值 |
| ETag |字符串 |
| FileLocator |字符串 |

### <a name="get-file-content-using-path"></a>使用路径获取文件内容
此操作使用文件路径获取文件内容。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| dataset* |站点 URL |SharePoint 站点 URL，如 http://contoso.sharepoint.com/sites/mysite |
| path* |文件路径 |选择文件 |

* 指示属性是必需的

### <a name="get-file-content"></a>获取文件内容
此操作使用文件 ID 获取文件内容。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| dataset* |站点 URL |SharePoint 站点 URL，如 http://contoso.sharepoint.com/sites/mysite |
| id* |文件标识符 |选择文件 |

* 指示属性是必需的

### <a name="create-file"></a>创建文件
此操作将文件上传到 SharePoint 站点。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| dataset* |站点 URL |SharePoint 站点 URL，如 http://contoso.sharepoint.com/sites/mysite |
| folderPath* |文件夹路径 |选择文件 |
| name* |文件名 |文件的名称 |
| body* |文件内容 |文件的内容 |

* 指示属性是必需的

#### <a name="output-details"></a>输出详细信息
BlobMetadata

| 属性名称 | 数据类型 |
| --- | --- | --- |
| ID |字符串 |
| Name |字符串 |
| DisplayName |字符串 |
| 路径 |字符串 |
| LastModified |字符串 |
| 大小 |integer |
| MediaType |字符串 |
| IsFolder |布尔值 |
| ETag |字符串 |
| FileLocator |字符串 |

### <a name="copy-file"></a>复制文件
此操作将文件复制到 SharePoint 站点。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| dataset* |站点 URL |SharePoint 站点 URL，如 http://contoso.sharepoint.com/sites/mysite |
| source* |源文件路径 |源文件的路径 |
| destination* |目标文件路径 |目标文件的路径 |
| overwrite |覆盖标志 |是否覆盖目标文件（如果存在） |

* 指示属性是必需的

#### <a name="output-details"></a>输出详细信息
BlobMetadata

| 属性名称 | 数据类型 |
| --- | --- | --- |
| ID |字符串 |
| Name |字符串 |
| DisplayName |字符串 |
| 路径 |字符串 |
| LastModified |字符串 |
| 大小 |integer |
| MediaType |字符串 |
| IsFolder |布尔值 |
| ETag |字符串 |
| FileLocator |字符串 |

### <a name="when-a-file-is-created"></a>创建文件时
此操作在 SharePoint 文件夹中新建文件时触发流。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| dataset* |站点 URL |SharePoint 站点 URL |
| folderId* |文件夹 ID |选择文件夹 |

* 指示属性是必需的

### <a name="when-a-file-is-modified"></a>修改文件时
此操作在 SharePoint 文件夹中修改文件时触发流。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| dataset* |站点 URL |SharePoint 站点 URL |
| folderId* |文件夹 ID |选择文件夹 |

* 指示属性是必需的

### <a name="list-folder"></a>列出文件夹
此操作获取包含在 SharePoint 文件夹中的文件。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| dataset* |站点 URL |SharePoint 站点 URL，如 http://contoso.sharepoint.com/sites/mysite |
| id* |文件标识符 |文件夹的唯一标识符 |

* 指示属性是必需的

#### <a name="output-details"></a>输出详细信息
BlobMetadata

| 属性名称 | 数据类型 |
| --- | --- | --- |
| ID |字符串 |
| Name |字符串 |
| DisplayName |字符串 |
| 路径 |字符串 |
| LastModified |字符串 |
| 大小 |integer |
| MediaType |字符串 |
| IsFolder |布尔值 |
| ETag |字符串 |
| FileLocator |字符串 |

### <a name="list-root-folder"></a>列出根文件夹
此操作获取根 SharePoint 文件夹中的文件。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| dataset* |站点 URL |SharePoint 站点 URL，如 http://contoso.sharepoint.com/sites/mysite |

* 指示属性是必需的

#### <a name="output-details"></a>输出详细信息
BlobMetadata

| 属性名称 | 数据类型 |
| --- | --- | --- |
| ID |字符串 |
| Name |字符串 |
| DisplayName |字符串 |
| 路径 |字符串 |
| LastModified |字符串 |
| 大小 |integer |
| MediaType |字符串 |
| IsFolder |布尔值 |
| ETag |字符串 |
| FileLocator |字符串 |

### <a name="extract-folder"></a>提取文件夹
此操作将存档文件提取到 SharePoint 文件夹中（示例：.zip）。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| dataset* |站点 URL |SharePoint 站点 URL，如 http://contoso.sharepoint.com/sites/mysite |
| source* |源文件路径 |源文件的路径 |
| destination* |目标文件夹路径 |目标文件夹的路径 |
| overwrite |覆盖标志 |是否覆盖目标文件（如果存在） |

* 指示属性是必需的

#### <a name="output-details"></a>输出详细信息
BlobMetadata

| 属性名称 | 数据类型 |
| --- | --- | --- |
| ID |字符串 |
| Name |字符串 |
| DisplayName |字符串 |
| 路径 |字符串 |
| LastModified |字符串 |
| 大小 |integer |
| MediaType |字符串 |
| IsFolder |布尔值 |
| ETag |字符串 |
| FileLocator |字符串 |

### <a name="when-a-new-item-is-created"></a>新建项目时
此操作在 SharePoint 列表中新建项目时触发流。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| dataset* |站点 URL |SharePoint 站点 URL（示例：http://contoso.sharepoint.com/sites/mysite） |
| table* |列表名称 |Sharepoint 列表名称 |
| $filter |筛选查询 |要限制已返回项的 ODATA 筛选查询 |
| $orderby |排序依据 |用于指定项顺序的 ODATA orderBy 查询 |
| $skip |跳过计数 |要跳过的项数（默认值 = 0） |
| $top |最大获取计数 |要检索的最大项数（默认值 = 256） |

* 指示属性是必需的

#### <a name="output-details"></a>输出详细信息
ItemsList

| 属性名称 | 数据类型 |
| --- | --- |
| value |数组 |

### <a name="when-an-existing-item-is-modified"></a>修改现有项目时
此操作在 SharePoint 列表中修改现有项目时触发流。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| dataset* |站点 URL |SharePoint 站点 URL（示例：http://contoso.sharepoint.com/sites/mysite） |
| table* |列表名称 |Sharepoint 列表名称 |
| $filter |筛选查询 |要限制已返回项的 ODATA 筛选查询 |
| $orderby |排序依据 |用于指定项顺序的 ODATA orderBy 查询 |
| $skip |跳过计数 |要跳过的项数（默认值 = 0） |
| $top |最大获取计数 |要检索的最大项数（默认值 = 256） |

* 指示属性是必需的

#### <a name="output-details"></a>输出详细信息
ItemsList

| 属性名称 | 数据类型 |
| --- | --- |
| value |数组 |

### <a name="get-items"></a>获取项目
此操作从 SharePoint 列表中获取项目。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| dataset* |站点 URL |SharePoint 站点 URL（示例：http://contoso.sharepoint.com/sites/mysite） |
| table* |列表名称 |Sharepoint 列表名称 |
| $filter |筛选查询 |要限制已返回项的 ODATA 筛选查询 |
| $orderby |排序依据 |用于指定项顺序的 ODATA orderBy 查询 |
| $skip |跳过计数 |要跳过的项数（默认值 = 0） |
| $top |最大获取计数 |要检索的最大项数（默认值 = 256） |

* 指示属性是必需的

#### <a name="output-details"></a>输出详细信息
ItemsList

| 属性名称 | 数据类型 |
| --- | --- |
| value |数组 |

### <a name="create-item"></a>创建项目
此操作在 SharePoint 列表中新建项目。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| dataset* |站点 URL |SharePoint 站点 URL（示例：http://contoso.sharepoint.com/sites/mysite） |
| table* |列表名称 |Sharepoint 列表名称 |
| item* |项目 |要创建的项目 |

* 指示属性是必需的

#### <a name="output-details"></a>输出详细信息
项目

| 属性名称 | 数据类型 |
| --- | --- |
| ItemInternalId |字符串 |

### <a name="get-item"></a>获取项目
此操作从 SharePoint 列表中按 ID 获取单个项目。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| dataset* |站点 URL |SharePoint 站点 URL（示例：http://contoso.sharepoint.com/sites/mysite） |
| table* |列表名称 |Sharepoint 列表名称 |
| id* |ID |要检索的项目的唯一标识符 |

* 指示属性是必需的

#### <a name="output-details"></a>输出详细信息
项目

| 属性名称 | 数据类型 |
| --- | --- |
| ItemInternalId |字符串 |

### <a name="delete-item"></a>删除项目
此操作从 SharePoint 列表中删除项目。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| dataset* |站点 URL |SharePoint 站点 URL（示例：http://contoso.sharepoint.com/sites/mysite） |
| table* |列表名称 |Sharepoint 列表名称 |
| id* |ID |要删除的项目的唯一标识符 |

* 指示属性是必需的

### <a name="update-item"></a>更新项目
此操作在 SharePoint 列表中更新项目。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| dataset* |站点 URL |SharePoint 站点 URL（示例：http://contoso.sharepoint.com/sites/mysite） |
| table* |列表名称 |Sharepoint 列表名称 |
| id* |ID |要更新的项目的唯一标识符 |
| item* |项目 |已更改属性的项目 |

* 指示属性是必需的

#### <a name="output-details"></a>输出详细信息
项目

| 属性名称 | 数据类型 |
| --- | --- |
| ItemInternalId |字符串 |

### <a name="get-entity-values"></a>获取实体值
此操作获取有关 SharePoint 实体的可能值。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| dataset* |SharePoint 站点 URL |SharePoint 站点 URL |
| table* |表名称 |表名称 |
| id* |实体 ID |实体 ID |

* 指示属性是必需的

#### <a name="output-details"></a>输出详细信息
### <a name="get-lists"></a>获取列表
此操作从站点获取 SharePoint 列表。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| dataset* |站点 URL |SharePoint 站点 URL（示例：http://contoso.sharepoint.com/sites/mysite） |

* 指示属性是必需的

#### <a name="output-details"></a>输出详细信息
TablesList

| 属性名称 | 数据类型 |
| --- | --- |
| value |数组 |

## <a name="http-responses"></a>HTTP 响应
上述操作和触发器可以返回以下一个或多个 HTTP 状态代码： 

| Name | 说明 |
| --- | --- |
| 200 |确定 |
| 202 |已接受 |
| 400 |错误的请求 |
| 401 |未授权 |
| 403 |禁止 |
| 404 |未找到 |
| 500 |内部服务器错误。 发生未知错误。 |
| default |操作失败。 |

## <a name="next-steps"></a>后续步骤
[创建逻辑应用](../app-service-logic/app-service-logic-create-a-logic-app.md)




<!--HONumber=Nov16_HO3-->


