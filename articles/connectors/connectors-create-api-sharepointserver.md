---
title: "在逻辑应用中使用 SharePoint Online 连接器 | Microsoft Docs"
description: "在逻辑应用中开始使用 Azure App Service SharePoint Online 连接器。"
services: 
documentationcenter: 
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: 0238a060-d592-4719-b7a2-26064c437a1a
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 76b6f627efdf189b873d9a6bdd722e73e837077d


---
# <a name="get-started-with-the-sharepoint-online-connector"></a>SharePoint Online 连接器入门
SharePoint 连接器提供在 SharePoint 上处理列表的方法。

> [!NOTE]
> 此文章版本适用于 2015 年 8 月 1 日预览版架构的逻辑应用。
> 
> 

若要在逻辑应用中添加操作，请参阅[创建逻辑应用](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## <a name="lets-talk-about-triggers-and-actions"></a>让我们谈谈触发器和操作
SharePoint 连接器可用作操作；它具有触发器。 所有连接器都支持采用 JSON 和 XML 格式的数据。 

SharePoint 连接器具有以下可用操作和/或触发器：

### <a name="sharepoint-actions"></a>SharePoint 操作
可执行以下操作：

| 操作 | 说明 |
| --- | --- |
| GetFileMetadata |用于在文档库上获取文件元数据 |
| UpdateFile |用于在文档库上更新文件 |
| DeleteFile |用于在文档库上删除文件 |
| GetFileMetadataByPath |用于在文档库上获取文件元数据 |
| GetFileContentByPath |用于在文档库上获取文件 |
| GetFileContent |用于在文档库上获取文件 |
| CreateFile |用于在文档库上上传文件 |
| CopyFile |用于在文档库上复制文件 |
| ExtractFolderV2 |用于在文档库上提取文件夹 |
| PostItem |在 SharePoint 列表中新建项目 |
| GetItem |从 SharePoint 列表中检索单个项目 |
| DeleteItem |从 SharePoint 列表中删除项目 |
| PatchItem |在 SharePoint 列表中更新项目 |

### <a name="sharepoint-triggers"></a>SharePoint 触发器
可侦听以下事件：

| 触发器 | 说明 |
| --- | --- |
| OnNewFile |在 SharePoint 文件夹中新建文件时触发流 |
| OnUpdatedFile |在 SharePoint 文件夹中修改文件时触发流 |
| GetOnNewItems |在 SharePoint 列表中新建项目时 |
| GetOnUpdatedItems |在 SharePoint 列表中修改现有项目时 |

## <a name="create-a-connection-to-sharepoint"></a>创建到 SharePoint 的连接
若要使用 SharePoint 连接器，首先创建**连接**，然后提供以下属性的详细信息： 

| 属性 | 必选 | 说明 |
| --- | --- | --- |
| 令牌 |是 |提供 SharePoint 凭据 |

若要连接到 **SharePoint Online**，需要向 SharePoint Online 提供你的标识（用户名和密码、智能卡凭据等）。 经过身份验证后，可以继续在逻辑应用中使用 SharePoint Online 连接器。 

逻辑应用的设计器打开时，按照以下步骤登录到 SharePoint Online，创建要在逻辑应用中使用的连接**连接**：

1. 在搜索框中输入“SharePoint”，然后等待搜索返回在名称中带有 SharePoint 的所有项：   
   ![配置 SharePoint][1]  
2. 选择“SharePoint Online - 创建文件时”   
3. 选择“登录到 SharePoint Online”：   
   ![配置 SharePoint][2]    
4. 提供你的 SharePoint 凭据进行登录，以使用 SharePoint 进行身份验证   
   ![配置 SharePoint][3]     
5. 完成身份验证后，你将重定向到逻辑应用，通过配置 SharePoint 的“创建文件时”对话框完成它。          
   ![配置 SharePoint][4]  
6. 然后可以添加完成逻辑应用所需的其他触发器和操作。   
7. 通过选择上方菜单栏中的“保存” 保存工作。  

## <a name="sharepoint-rest-api-reference"></a>SharePoint REST API 参考
#### <a name="this-documentation-is-for-version-10"></a>此文档用于版本：1.0
### <a name="used-for-getting-a-file-metadata-on-document-library"></a>用于在文档库上获取文件元数据
**```GET: /datasets/{dataset}/files/{id}```** 

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| dataset |字符串 |是 |路径 |无 |SharePoint 站点 URL。 例如 http://contoso.sharepoint.com/sites/mysite |
| id |字符串 |是 |路径 |无 |文件的唯一标识符 |

### <a name="here-are-the-possible-responses"></a>下面是可能的响应：
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

- - -
### <a name="used-for-updating-a-file-on-document-library"></a>用于在文档库上更新文件
**```PUT: /datasets/{dataset}/files/{id}```** 

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| dataset |字符串 |是 |路径 |无 |SharePoint 站点 URL。 例如 http://contoso.sharepoint.com/sites/mysite |
| id |字符串 |是 |路径 |无 |文件的唯一标识符 |
| body | |是 |body |无 |文件的内容 |

### <a name="here-are-the-possible-responses"></a>下面是可能的响应：
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

- - -
### <a name="used-for-deleting-a-file-on-document-library"></a>用于在文档库上删除文件
**```DELETE: /datasets/{dataset}/files/{id}```** 

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| dataset |字符串 |是 |路径 |无 |SharePoint 站点 URL。 例如 http://contoso.sharepoint.com/sites/mysite |
| id |字符串 |是 |路径 |无 |文件的唯一标识符 |

### <a name="here-are-the-possible-responses"></a>下面是可能的响应：
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

- - -
### <a name="used-for-getting-a-file-metadata-on-document-library"></a>用于在文档库上获取文件元数据
**```GET: /datasets/{dataset}/GetFileByPath```** 

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| dataset |字符串 |是 |路径 |无 |SharePoint 站点 URL。 例如 http://contoso.sharepoint.com/sites/mysite |
| path |字符串 |是 |查询 |无 |文件的路径 |

### <a name="here-are-the-possible-responses"></a>下面是可能的响应：
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

- - -
### <a name="used-for-getting-a-file-on-document-library"></a>用于在文档库上获取文件
**```GET: /datasets/{dataset}/GetFileContentByPath```** 

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| dataset |字符串 |是 |路径 |无 |SharePoint 站点 URL。 例如 http://contoso.sharepoint.com/sites/mysite |
| path |字符串 |是 |查询 |无 |文件的路径 |

### <a name="here-are-the-possible-responses"></a>下面是可能的响应：
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

- - -
### <a name="used-for-getting-a-file-on-document-library"></a>用于在文档库上获取文件
**```GET: /datasets/{dataset}/files/{id}/content```** 

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| dataset |字符串 |是 |路径 |无 |SharePoint 站点 URL。 例如 http://contoso.sharepoint.com/sites/mysite |
| id |字符串 |是 |路径 |无 |文件的唯一标识符 |

### <a name="here-are-the-possible-responses"></a>下面是可能的响应：
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

- - -
### <a name="used-for-uploading-a-file-on-document-library"></a>用于在文档库上上传文件
**```POST: /datasets/{dataset}/files```** 

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| dataset |字符串 |是 |路径 |无 |SharePoint 站点 URL。 例如 http://contoso.sharepoint.com/sites/mysite |
| folderPath |字符串 |是 |查询 |无 |文件夹的路径 |
| 名称 |字符串 |是 |查询 |无 |文件的名称 |
| body | |是 |body |无 |文件的内容 |

### <a name="here-are-the-possible-responses"></a>下面是可能的响应：
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

- - -
### <a name="used-for-copying-a-file-on-document-library"></a>用于在文档库上复制文件
**```POST: /datasets/{dataset}/copyFile```** 

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| dataset |字符串 |是 |路径 |无 |SharePoint 站点 URL。 例如 http://contoso.sharepoint.com/sites/mysite |
| 源 |字符串 |是 |查询 |无 |源文件的路径 |
| 目标 |字符串 |是 |查询 |无 |目标文件的路径 |
| overwrite |布尔值 |否 |查询 |false |是否覆盖现有文件 |

### <a name="here-are-the-possible-responses"></a>下面是可能的响应：
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

- - -
### <a name="triggers-a-flow-when-a-new-file-is-created-in-a-sharepoint-folder"></a>在 SharePoint 文件夹中新建文件时触发流
**```GET: /datasets/{dataset}/triggers/onnewfile```** 

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| dataset |字符串 |是 |路径 |无 |SharePoint 站点 URL |
| folderId |字符串 |是 |查询 |无 |SharePoint 中的文件夹的唯一标识符 |

### <a name="here-are-the-possible-responses"></a>下面是可能的响应：
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

- - -
### <a name="triggers-a-flow-when-a-file-is-modified-in-a-sharepoint-folder"></a>在 SharePoint 文件夹中修改文件时触发流
**```GET: /datasets/{dataset}/triggers/onupdatedfile```** 

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| dataset |字符串 |是 |路径 |无 |SharePoint 站点 URL |
| folderId |字符串 |是 |查询 |无 |SharePoint 中的文件夹的唯一标识符 |

### <a name="here-are-the-possible-responses"></a>下面是可能的响应：
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

- - -
### <a name="used-for-extracting-a-folder-on-document-library"></a>用于在文档库上提取文件夹
**```POST: /datasets/{dataset}/extractFolderV2```** 

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| dataset |字符串 |是 |路径 |无 |SharePoint 站点 URL。 例如 http://contoso.sharepoint.com/sites/mysite |
| 源 |字符串 |是 |查询 |无 |源文件的路径 |
| 目标 |字符串 |是 |查询 |无 |目标文件夹的路径 |
| overwrite |布尔值 |否 |查询 |false |是否覆盖现有文件 |

### <a name="here-are-the-possible-responses"></a>下面是可能的响应：
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

- - -
### <a name="when-a-new-item-is-created-in-a-sharepoint-list"></a>在 SharePoint 列表中新建项目时
**```GET: /datasets/{dataset}/tables/{table}/onnewitems```** 

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| dataset |字符串 |是 |路径 |无 |SharePoint 站点 URL（示例：http://contoso.sharepoint.com/sites/mysite） |
| 表 |字符串 |是 |路径 |无 |Sharepoint 列表名称 |
| $skip |integer |否 |查询 |无 |要跳过的项数（默认值 = 0） |
| $top |integer |否 |查询 |无 |要检索的最大项数（默认值 = 256） |
| $filter |字符串 |否 |查询 |无 |要限制项数的 ODATA 筛选查询 |
| $orderby |字符串 |否 |查询 |无 |用于指定项顺序的 ODATA orderBy 查询 |

### <a name="here-are-the-possible-responses"></a>下面是可能的响应：
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

- - -
### <a name="when-an-existing-item-is-modified-in-a-sharepoint-list"></a>在 SharePoint 列表中修改现有项目时
**```GET: /datasets/{dataset}/tables/{table}/onupdateditems```** 

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| dataset |字符串 |是 |路径 |无 |SharePoint 站点 URL（示例：http://contoso.sharepoint.com/sites/mysite） |
| 表 |字符串 |是 |路径 |无 |Sharepoint 列表名称 |
| $skip |integer |否 |查询 |无 |要跳过的项数（默认值 = 0） |
| $top |integer |否 |查询 |无 |要检索的最大项数（默认值 = 256） |
| $filter |字符串 |否 |查询 |无 |要限制项数的 ODATA 筛选查询 |
| $orderby |字符串 |否 |查询 |无 |用于指定项顺序的 ODATA orderBy 查询 |

### <a name="here-are-the-possible-responses"></a>下面是可能的响应：
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

- - -
### <a name="creates-a-new-item-in-a-sharepoint-list"></a>在 SharePoint 列表中新建项目
**```POST: /datasets/{dataset}/tables/{table}/items```** 

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| dataset |字符串 |是 |路径 |无 |SharePoint 站点 URL（示例：http://contoso.sharepoint.com/sites/mysite） |
| 表 |字符串 |是 |路径 |无 |Sharepoint 列表名称 |
| item | |是 |body |无 |要创建的项目 |

### <a name="here-are-the-possible-responses"></a>下面是可能的响应：
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

- - -
### <a name="retrieves-a-single-item-from-a-sharepoint-list"></a>从 SharePoint 列表中检索单个项目
**```GET: /datasets/{dataset}/tables/{table}/items/{id}```** 

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| dataset |字符串 |是 |路径 |无 |SharePoint 站点 URL（示例：http://contoso.sharepoint.com/sites/mysite） |
| 表 |字符串 |是 |路径 |无 |Sharepoint 列表名称 |
| id |integer |是 |路径 |无 |要检索的项目的唯一标识符 |

### <a name="here-are-the-possible-responses"></a>下面是可能的响应：
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

- - -
### <a name="deletes-an-item-from-a-sharepoint-list"></a>从 SharePoint 列表中删除项目
**```DELETE: /datasets/{dataset}/tables/{table}/items/{id}```** 

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| dataset |字符串 |是 |路径 |无 |SharePoint 站点 URL（示例：http://contoso.sharepoint.com/sites/mysite） |
| 表 |字符串 |是 |路径 |无 |Sharepoint 列表名称 |
| id |integer |是 |路径 |无 |要删除的项目的唯一标识符 |

### <a name="here-are-the-possible-responses"></a>下面是可能的响应：
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

- - -
### <a name="updates-an-item-in-a-sharepoint-list"></a>在 SharePoint 列表中更新项目
**```PATCH: /datasets/{dataset}/tables/{table}/items/{id}```** 

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| dataset |字符串 |是 |路径 |无 |SharePoint 站点 URL（示例：http://contoso.sharepoint.com/sites/mysite） |
| 表 |字符串 |是 |路径 |无 |Sharepoint 列表名称 |
| id |integer |是 |路径 |无 |要更新的项目的唯一标识符 |
| item | |是 |body |无 |已更改属性的项目 |

### <a name="here-are-the-possible-responses"></a>下面是可能的响应：
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

- - -
## <a name="object-definitions"></a>对象定义：
 **DataSetsMetadata**：

DataSetsMetadata 的必需属性：

没有一个属性是必需属性。 

**所有属性**： 

| Name | 数据类型 |
| --- | --- |
| tabular |未定义 |
| Blob |未定义 |

 **TabularDataSetsMetadata**：

TabularDataSetsMetadata 的必需属性：

没有一个属性是必需属性。 

**所有属性**： 

| Name | 数据类型 |
| --- | --- |
| 源 |字符串 |
| displayName |字符串 |
| urlEncoding |字符串 |
| tableDisplayName |字符串 |
| tablePluralName |字符串 |

 **BlobDataSetsMetadata**：

BlobDataSetsMetadata 的必需属性：

没有一个属性是必需属性。 

**所有属性**： 

| Name | 数据类型 |
| --- | --- |
| 源 |字符串 |
| displayName |字符串 |
| urlEncoding |字符串 |

 **BlobMetadata**：

BlobMetadata 的必需属性：

没有一个属性是必需属性。 

**所有属性**： 

| Name | 数据类型 |
| --- | --- |
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

 **Object**：

Object 的必需属性：

没有一个属性是必需属性。 

**所有属性**： 

| Name | 数据类型 |
| --- | --- |
|  | |

 **TableMetadata**：

TableMetadata 的必需属性：

没有一个属性是必需属性。 

**所有属性**： 

| Name | 数据类型 |
| --- | --- |
| 名称 |字符串 |
| title |字符串 |
| x-ms-permission |字符串 |
| schema |未定义 |

 **DataSetsList**：

DataSetsList 的必需属性：

没有一个属性是必需属性。 

**所有属性**： 

| Name | 数据类型 |
| --- | --- |
| value |数组 |

 **DataSet**：

DataSet 的必需属性：

没有一个属性是必需属性。 

**所有属性**： 

| Name | 数据类型 |
| --- | --- |
| Name |字符串 |
| DisplayName |字符串 |

 **Table**：

Table 的必需属性：

没有一个属性是必需属性。 

**所有属性**： 

| Name | 数据类型 |
| --- | --- |
| Name |字符串 |
| DisplayName |字符串 |

 **Item**：

Item 的必需属性：

没有一个属性是必需属性。 

**所有属性**： 

| Name | 数据类型 |
| --- | --- |
| ItemInternalId |字符串 |

 **ItemsList**：

ItemsList 的必需属性：

没有一个属性是必需属性。 

**所有属性**： 

| Name | 数据类型 |
| --- | --- |
| value |数组 |

 **TablesList**：

TablesList 的必需属性：

没有一个属性是必需属性。 

**所有属性**： 

| Name | 数据类型 |
| --- | --- |
| value |数组 |

## <a name="next-steps"></a>后续步骤
[创建逻辑应用](../app-service-logic/app-service-logic-create-a-logic-app.md)  

[1]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig1.png  
[2]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig2.png 
[3]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig3.png
[4]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig4.png
[5]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig5.png



<!--HONumber=Nov16_HO3-->


