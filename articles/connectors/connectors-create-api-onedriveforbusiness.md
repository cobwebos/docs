---
title: OneDrive for Business | Microsoft Docs
description: "使用 Azure App Service 创建逻辑应用。 连接到 OneDrive for Business 管理你的文件。 你可以执行各种操作，例如上传、更新、获取和删除文件。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: cf9484e9-7a20-4de0-93c8-0fa132221f2b
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: 992b4b7bab8878355cbe936b75768e67b086286e


---
# <a name="get-started-with-the-onedrive-for-business-connector"></a>OneDrive for Business 连接器入门
连接到 OneDrive for Business 管理你的文件。 你可以执行各种操作，例如上传、更新、获取和删除文件。

> [!NOTE]
> 此文章版本适用于 2015 年 8 月 1 日预览版架构的逻辑应用。
> 
> 

若要立即开始创建逻辑应用，请参阅[创建逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)。

## <a name="triggers-and-actions"></a>触发器和操作
OneDrive for Business 连接器可用作操作；它具有触发器。 所有连接器都支持采用 JSON 和 XML 格式的数据。

 OneDrive for Business 连接器具有以下可用操作和/或触发器：

### <a name="onedrive-for-business-actions"></a>OneDrive for Business 操作
可执行以下操作：

| 操作 | 说明 |
| --- | --- |
| [GetFileMetadata](connectors-create-api-onedriveforbusiness.md#getfilemetadata) |使用 ID 在 OneDrive for Business 中检索文件的元数据 |
| [UpdateFile](connectors-create-api-onedriveforbusiness.md#updatefile) |在 OneDrive for Business 中更新文件 |
| [DeleteFile](connectors-create-api-onedriveforbusiness.md#deletefile) |从 OneDrive for Business 中删除文件 |
| [GetFileMetadataByPath](connectors-create-api-onedriveforbusiness.md#getfilemetadatabypath) |使用路径在 OneDrive for Business 中检索文件的元数据 |
| [GetFileContentByPath](connectors-create-api-onedriveforbusiness.md#getfilecontentbypath) |使用路径在 OneDrive for Business 中检索文件的内容 |
| [GetFileContent](connectors-create-api-onedriveforbusiness.md#getfilecontent) |使用 ID 在 OneDrive for Business 中检索文件的内容 |
| [CreateFile](connectors-create-api-onedriveforbusiness.md#createfile) |将文件上传到 OneDrive for Business |
| [CopyFile](connectors-create-api-onedriveforbusiness.md#copyfile) |将文件复制到 OneDrive for Business |
| [ListFolder](connectors-create-api-onedriveforbusiness.md#listfolder) |列出 OneDrive for Business 文件夹中的文件 |
| [ListRootFolder](connectors-create-api-onedriveforbusiness.md#listrootfolder) |列出 OneDrive for Business 根文件夹中的文件 |
| [ExtractFolderV2](connectors-create-api-onedriveforbusiness.md#extractfolderv2) |将文件夹提取到 OneDrive for Business |

### <a name="onedrive-for-business-triggers"></a>OneDrive for Business 触发器
可侦听以下事件：

| 触发器 | 说明 |
| --- | --- |
| 创建文件时 |在 OneDrive for Business 文件夹中新建文件时，触发流 |
| 修改文件时 |在 OneDrive for Business 文件夹中修改文件时，触发流 |

## <a name="create-a-connection-to-onedrive-for-business"></a>创建到 OneDrive for Business 的连接
若要使用 OneDrive for Business 创建逻辑应用，必须先创建**连接**，然后提供以下属性的详细信息：

| 属性 | 必选 | 说明 |
| --- | --- | --- |
| 令牌 |是 |提供 OneDrive for Business 凭据 |

创建连接后，可使用它执行操作并侦听触发器，如本文所述。

> [!INCLUDE [Steps to create a connection to OneDrive for Business](../../includes/connectors-create-api-onedriveforbusiness.md)]
> 
> [!TIP]
> 可在其他逻辑应用中使用此连接。
> 
> 

## <a name="reference-for-onedrive-for-business"></a>OneDrive for Business 的参考
适用于版本：1.0

## <a name="getfilemetadata"></a>GetFileMetadata
使用 ID 获取文件元数据：使用 ID 在 OneDrive for Business 中检索文件的元数据

```GET: /datasets/default/files/{id}```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| id |字符串 |是 |路径 |无 |指定文件 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

## <a name="updatefile"></a>UpdateFile
更新文件：在 OneDrive for Business 中更新文件

```PUT: /datasets/default/files/{id}```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| id |字符串 |是 |路径 |无 |指定要更新的文件 |
| body | |是 |body |无 |要在 OneDrive for Business 中更新的文件的内容 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

## <a name="deletefile"></a>DeleteFile
删除文件：从 OneDrive for Business 中删除文件

```DELETE: /datasets/default/files/{id}```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| id |字符串 |是 |路径 |无 |指定要删除的文件 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

## <a name="getfilemetadatabypath"></a>GetFileMetadataByPath
使用路径获取文件元数据：使用路径在 OneDrive for Business 中检索文件的元数据

```GET: /datasets/default/GetFileByPath```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| 路径 |字符串 |是 |查询 |无 |OneDrive for Business 中的文件的唯一路径 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

## <a name="getfilecontentbypath"></a>GetFileContentByPath
使用路径获取文件内容：使用路径在 OneDrive for Business 中检索文件的内容

```GET: /datasets/default/GetFileContentByPath```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| 路径 |字符串 |是 |查询 |无 |OneDrive for Business 中的文件的唯一路径 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

## <a name="getfilecontent"></a>GetFileContent
使用 ID 获取文件内容：使用 ID 在 OneDrive for Business 中检索文件的内容

```GET: /datasets/default/files/{id}/content```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| id |字符串 |是 |路径 |无 |指定文件 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

## <a name="createfile"></a>CreateFile
创建文件：将文件上传到 OneDrive for Business

```POST: /datasets/default/files```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| folderPath |字符串 |是 |查询 |无 |要将文件上传到 OneDrive for Business 的文件夹路径 |
| 名称 |字符串 |是 |查询 |无 |要在 OneDrive for Business 中创建的文件的名称 |
| body | |是 |body |无 |要上传到 OneDrive for Business 的文件的内容 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

## <a name="copyfile"></a>CopyFile
复制文件：将文件复制到 OneDrive for Business

```POST: /datasets/default/copyFile```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| 源 |字符串 |是 |查询 |无 |源文件的 URL |
| 目标 |字符串 |是 |查询 |无 |OneDrive for Business 中的目标文件路径，包括目标文件名 |
| overwrite |布尔值 |否 |查询 |false |如果设置为“true”，覆盖目标文件 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

## <a name="onnewfile"></a>OnNewFile
创建文件时：在 OneDrive for Business 文件夹中新建文件时，触发流

```GET: /datasets/default/triggers/onnewfile```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| folderId |字符串 |是 |查询 |无 |指定文件夹 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

## <a name="onupdatedfile"></a>OnUpdatedFile
修改文件时：在 OneDrive for Business 文件夹中修改文件时，触发流

```GET: /datasets/default/triggers/onupdatedfile```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| folderId |字符串 |是 |查询 |无 |指定文件夹 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

## <a name="listfolder"></a>ListFolder
列出文件夹中的文件：列出 OneDrive for Business 文件夹中的文件

```GET: /datasets/default/folders/{id}```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| id |字符串 |是 |路径 |无 |指定文件夹 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

## <a name="listrootfolder"></a>ListRootFolder
列出根文件夹：列出 OneDrive for Business 根文件夹中的文件

```GET: /datasets/default/folders```

此调用没有任何参数

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

## <a name="extractfolderv2"></a>ExtractFolderV2
提取文件夹：将文件夹提取到 OneDrive for Business

```POST: /datasets/default/extractFolderV2```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| 源 |字符串 |是 |查询 |无 |存档文件的路径 |
| 目标 |字符串 |是 |查询 |无 |OneDrive for Business 中要提取存档内容的路径 |
| overwrite |布尔值 |否 |查询 |false |如果设置为“true”，覆盖目标文件 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

## <a name="object-definitions"></a>对象定义
### <a name="datasetsmetadata"></a>DataSetsMetadata
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| tabular |未定义 |否 |
| Blob |未定义 |否 |

### <a name="tabulardatasetsmetadata"></a>TabularDataSetsMetadata
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| 源 |字符串 |否 |
| displayName |字符串 |否 |
| urlEncoding |字符串 |否 |
| tableDisplayName |字符串 |否 |
| tablePluralName |字符串 |否 |

### <a name="blobdatasetsmetadata"></a>BlobDataSetsMetadata
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| 源 |字符串 |否 |
| displayName |字符串 |否 |
| urlEncoding |字符串 |否 |

### <a name="blobmetadata"></a>BlobMetadata
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| ID |字符串 |否 |
| Name |字符串 |否 |
| DisplayName |字符串 |否 |
| 路径 |字符串 |否 |
| LastModified |字符串 |否 |
| 大小 |integer |否 |
| MediaType |字符串 |否 |
| IsFolder |布尔值 |否 |
| ETag |字符串 |否 |
| FileLocator |字符串 |否 |

### <a name="object"></a>对象
## <a name="next-steps"></a>后续步骤
[创建逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)




<!--HONumber=Jan17_HO3-->


