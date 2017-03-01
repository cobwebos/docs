---
title: "在逻辑应用中添加 Google Drive 连接器 | Microsoft Docs"
description: "使用 REST API 参数的 Google Drive 连接器概述"
services: 
suite: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: b2bcebc5-02d2-435b-b0da-ef53bc51c4b6
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: 497f963870d24a335379a7f015a515c425737a73


---
# <a name="get-started-with-the-google-drive-connector"></a>Google Drive 连接器入门
连接到 Google Drive，创建文件、获取行等。 通过 Google Drive，你可以： 

* 根据从搜索中获取的数据生成你的业务流。 
* 使用搜索图像、搜索资讯等操作。 这些操作可获得响应，然后使输出可用于其他操作。 例如，你可以搜索视频，然后使用 Twitter 将该视频发布到 Twitter 源。

若要在逻辑应用中添加操作，请参阅[创建逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)。

## <a name="triggers-and-actions"></a>触发器和操作
Google Drive 包括以下操作。 没有任何触发器。 

| 触发器 | 操作 |
| --- | --- |
| 无 |<ul><li>创建文件</li><li>插入行</li><li>复制文件</li><li>删除文件</li><li>删除行</li><li>将存档提取到文件夹</li><li>使用 ID 获取文件内容</li><li>使用路径获取文件内容</li><li>使用 ID 获取文件元数据</li><li>使用路径获取文件元数据</li><li>获取行</li><li>更新文件</li><li>更新行</li></ul> |

所有连接器都支持采用 JSON 和 XML 格式的数据。

## <a name="create-the-connection-to-google-drive"></a>创建到 Google Drive 的连接
将此连接器添加到逻辑应用时，必须授权逻辑应用连接到你的 Google Drive。

> [!INCLUDE [Steps to create a connection to googledrive](../../includes/connectors-create-api-googledrive.md)]
> 
> 

创建连接后，输入 Google Drive 属性，例如文件夹路径或文件名。 本主题中的 **REST API 参考**介绍了这些属性。

> [!TIP]
> 可在其他逻辑应用中使用此相同 Google Drive 连接。
> 
> 

## <a name="swagger-rest-api-reference"></a>Swagger REST API 参考
适用于版本：1.0。

### <a name="create-file"></a>创建文件
将文件上传到 Google Drive。  
```POST: /datasets/default/files```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| folderPath |字符串 |是 |查询 |无 |要将文件上传到 Google Drive 的文件夹路径 |
| 名称 |字符串 |是 |查询 |无 |要在 Google Drive 中创建的文件的名称 |
| body |字符串（二进制） |是 |body |无 |要上传到 Google Drive 的文件的内容 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

### <a name="insert-row"></a>插入行
在 Google Sheet 中插入行。  
```POST: /datasets/{dataset}/tables/{table}/items```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| dataset |字符串 |是 |路径 |无 |Google Sheet 文件的唯一标识符 |
| 表 |字符串 |是 |路径 |无 |工作表的唯一标识符 |
| item |ItemInternalId：字符串 |是 |body |无 |要插入到指定表中的行 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

### <a name="copy-file"></a>复制文件
复制 Google Drive 上的文件。  
```POST: /datasets/default/copyFile```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| 源 |字符串 |是 |查询 |无 |源文件的 URL |
| 目标 |字符串 |是 |查询 |无 |Google Drive 中的目标文件路径，包括目标文件名 |
| overwrite |布尔值 |否 |查询 |无 |如果设置为“true”，覆盖目标文件 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

### <a name="delete-file"></a>删除文件
从 Google Drive 中删除文件。  
```DELETE: /datasets/default/files/{id}```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| id |字符串 |是 |路径 |无 |要从 Google Drive 中删除的文件的唯一标识符 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

### <a name="delete-row"></a>删除行
从 Google Sheet 表中删除行。  
```DELETE: /datasets/{dataset}/tables/{table}/items/{id}```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| dataset |字符串 |是 |路径 |无 |Google Sheet 文件的唯一标识符 |
| 表 |字符串 |是 |路径 |无 |工作表的唯一标识符 |
| id |字符串 |是 |路径 |无 |要删除的行的唯一标识符 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

### <a name="extract-archive-to-folder"></a>将存档提取到文件夹
将存档文件提取到 Google Drive 中的文件夹（示例：.zip）。  
```POST: /datasets/default/extractFolderV2```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| 源 |字符串 |是 |查询 |无 |存档文件的路径 |
| 目标 |字符串 |是 |查询 |无 |要提取存档内容的 Google Drive 中的路径 |
| overwrite |布尔值 |否 |查询 |无 |如果设置为“true”，覆盖目标文件 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

### <a name="get-file-content-using-id"></a>使用 ID 获取文件内容
使用 ID 从 Google Drive 中检索文件内容。  
```GET: /datasets/default/files/{id}/content```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| id |字符串 |是 |路径 |无 |要在 Google Drive 中检索的文件的唯一标识符 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

### <a name="get-file-content-using-path"></a>使用路径获取文件内容
使用路径从 Google Drive 中检索文件内容。  
```GET: /datasets/default/GetFileContentByPath```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| 路径 |字符串 |是 |查询 |无 |Google Drive 中的文件的路径 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

### <a name="get-file-metadata-using-id"></a>使用 ID 获取文件元数据
使用 ID 从 Google Drive 中检索文件元数据。  
```GET: /datasets/default/files/{id}```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| id |字符串 |是 |路径 |无 |Google Drive 中的文件的唯一标识符 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

### <a name="get-file-metadata-using-path"></a>使用路径获取文件元数据
使用路径从 Google Drive 中检索文件元数据。  
```GET: /datasets/default/GetFileByPath```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| 路径 |字符串 |是 |查询 |无 |Google Drive 中的文件的路径 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

### <a name="get-row"></a>获取行
从 Google Sheet 中检索单个行。  
```GET: /datasets/{dataset}/tables/{table}/items/{id}```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| dataset |字符串 |是 |路径 |无 |Google Sheet 文件的唯一标识符 |
| 表 |字符串 |是 |路径 |无 |工作表的唯一标识符 |
| id |字符串 |是 |路径 |无 |要检索的行的唯一标识符 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

### <a name="update-file"></a>更新文件
在 Google Drive 中更新文件。  
```PUT: /datasets/default/files/{id}```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| id |字符串 |是 |路径 |无 |要在 Google Drive 中更新的文件的唯一标识符 |
| body |字符串（二进制） |是 |body |无 |要上传到 Google Drive 的文件的内容 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

### <a name="update-row"></a>更新行
在 Google Sheet 中更新行。  
```PATCH: /datasets/{dataset}/tables/{table}/items/{id}```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| dataset |字符串 |是 |路径 |无 |Google Sheet 文件的唯一标识符 |
| 表 |字符串 |是 |路径 |无 |工作表的唯一标识符 |
| id |字符串 |是 |路径 |无 |要更新的行的唯一标识符 |
| item |ItemInternalId：字符串 |是 |body |无 |具有已更新值的行 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

## <a name="object-definitions"></a>对象定义
#### <a name="datasetsmetadata"></a>DataSetsMetadata
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| tabular |未定义 |否 |
| Blob |未定义 |否 |

#### <a name="tabulardatasetsmetadata"></a>TabularDataSetsMetadata
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| 源 |字符串 |否 |
| displayName |字符串 |否 |
| urlEncoding |字符串 |否 |
| tableDisplayName |字符串 |否 |
| tablePluralName |字符串 |否 |

#### <a name="blobdatasetsmetadata"></a>BlobDataSetsMetadata
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| 源 |字符串 |否 |
| displayName |字符串 |否 |
| urlEncoding |字符串 |否 |

#### <a name="blobmetadata"></a>BlobMetadata
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

#### <a name="tablemetadata"></a>TableMetadata
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| 名称 |字符串 |否 |
| title |字符串 |否 |
| x-ms-permission |字符串 |否 |
| schema |未定义 |否 |

#### <a name="tableslist"></a>TablesList
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| value |数组 |否 |

#### <a name="table"></a>表
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| Name |字符串 |否 |
| DisplayName |字符串 |否 |

#### <a name="item"></a>项目
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| ItemInternalId |字符串 |否 |

#### <a name="itemslist"></a>ItemsList
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| value |数组 |否 |

## <a name="next-steps"></a>后续步骤
[创建逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)。

返回到 [API 列表](apis-list.md)。

<!--References-->
[5]: https://console.developers.google.com/
[6]: ./media/connectors-create-api-googledrive/google-developers-console.png
[8]: ./media/connectors-create-api-googledrive/use-google-apis.png
[9]: ./media/connectors-create-api-googledrive/googledrive-api-overview.png
[10]: ./media/connectors-create-api-googledrive/enable-googledrive-api.png
[12]: ./media/connectors-create-api-googledrive/googledrive-api-credentials-add.png
[13]: ./media/connectors-create-api-googledrive/configure-consent-screen.png
[14]: ./media/connectors-create-api-googledrive/create-client-id.png



<!--HONumber=Jan17_HO3-->


