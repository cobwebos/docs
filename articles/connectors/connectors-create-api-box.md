---
title: "将 Box 连接器添加到逻辑应用 | Microsoft Docs"
description: "使用 REST API 参数的 Box 连接器概述"
services: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 949579cf-a81c-4790-9ef5-fe39b4fbd0c5
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: 71f9dd111ebdbe885f33d162b2ea320dfaa167bb
ms.openlocfilehash: e211d0878a7f9bd43c95c727935ad883fae0db3a


---
# <a name="get-started-with-the-box-connector"></a>Box 连接器入门
连接到 Box，创建文件、删除文件等。 

> [!NOTE]
> 此文章版本适用于 2015 年 8 月 1 日预览版架构的逻辑应用。
> 
> 

通过 Box，你可以：

* 根据从 Box 中获取的数据生成你的业务流。 
* 在创建或更新文件时使用触发器。
* 使用复制文件、删除文件等操作。 这些操作可获得响应，然后使输出可用于其他操作。 例如，某个文件在 Box 上发生更改后，你可以获取该文件，并使用 Office 365 通过电子邮件发送它。

若要在逻辑应用中添加操作，请参阅[创建逻辑应用](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## <a name="triggers-and-actions"></a>触发器和操作
Box 包括以下触发器和操作。

| 触发器 | 操作 |
| --- | --- |
| <ul><li>创建文件时</li><li>修改文件时</li></ul> |<ul><li>创建文件</li><li>创建文件时</li><li>复制文件</li><li>删除文件</li><li>将存档提取到文件夹</li><li>使用 ID 获取文件内容</li><li>使用路径获取文件内容</li><li>使用 ID 获取文件元数据</li><li>使用路径获取文件元数据</li><li>更新文件</li><li>修改文件时</li></ul> |

所有连接器都支持采用 JSON 和 XML 格式的数据。

## <a name="create-a-connection-to-box"></a>创建到 Box 的连接
将此连接器添加到逻辑应用时，必须授权逻辑应用连接到你的 Box。

> [!INCLUDE [Steps to create a connection to box](../../includes/connectors-create-api-box.md)]
> 
> 

创建连接后，输入 Box 属性。 本主题中的 **REST API 参考**介绍了这些属性。

> [!TIP]
> 可在其他逻辑应用中使用此相同 Box 连接。
> 
> 

## <a name="swagger-rest-api-reference"></a>Swagger REST API 参考
适用于版本：1.0。

### <a name="create-file"></a>创建文件
将文件上传到 Box。  
```POST: /datasets/default/files```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| folderPath |字符串 |是 |查询 |无 |要将文件上传到 Box 的文件夹路径 |
| 名称 |字符串 |是 |查询 |无 |要在 Box 中创建的文件的名称 |
| body |字符串（二进制） |是 |body |无 |要上传到 Box 的文件的内容 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

### <a name="when-a-file-is-created"></a>创建文件时
在 Box 文件夹中新建文件时，触发流。  
```GET: /datasets/default/triggers/onnewfile```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| folderId |字符串 |是 |查询 |无 |Box 中的文件夹的唯一标识符 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

### <a name="copy-file"></a>复制文件
将文件复制到 Box。  
```POST: /datasets/default/copyFile```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| 源 |字符串 |是 |查询 |无 |源文件的 URL |
| 目标 |字符串 |是 |查询 |无 |Box 中的目标文件路径，包括目标文件名 |
| overwrite |布尔值 |否 |查询 |无 |如果设置为“true”，覆盖目标文件 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

### <a name="delete-file"></a>删除文件
从 Box 中删除文件。  
```DELETE: /datasets/default/files/{id}```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| id |字符串 |是 |路径 |无 |要从 Box 中删除的文件的唯一标识符 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

### <a name="extract-archive-to-folder"></a>将存档提取到文件夹
将存档文件提取到 Box 中的文件夹（示例：.zip）。  
```POST: /datasets/default/extractFolderV2```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| 源 |字符串 |是 |查询 | |存档文件的路径 |
| 目标 |字符串 |是 |查询 | |要提取存档内容的 Box 中的路径 |
| overwrite |布尔值 |否 |查询 | |如果设置为“true”，覆盖目标文件 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

### <a name="get-file-content-using-id"></a>使用 ID 获取文件内容
使用 ID 从 Box 中检索文件内容。  
```GET: /datasets/default/files/{id}/content```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| id |字符串 |是 |路径 |无 |Box 中的文件的唯一标识符 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

### <a name="get-file-content-using-path"></a>使用路径获取文件内容
使用路径从 Box 中检索文件内容。  
```GET: /datasets/default/GetFileContentByPath```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| 路径 |字符串 |是 |查询 |无 |Box 中的文件的唯一路径 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

### <a name="get-file-metadata-using-id"></a>使用 ID 获取文件元数据
使用文件 ID 从 Box 中检索文件元数据。  
```GET: /datasets/default/files/{id}```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| id |字符串 |是 |路径 |无 |Box 中的文件的唯一标识符 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

### <a name="get-file-metadata-using-path"></a>使用路径获取文件元数据
使用路径从 Box 中检索文件元数据。  
```GET: /datasets/default/GetFileByPath```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| 路径 |字符串 |是 |查询 |无 |Box 中的文件的唯一路径 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

### <a name="update-file"></a>更新文件
更新 Box 中的文件。  
```PUT: /datasets/default/files/{id}```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| id |字符串 |是 |路径 |无 |要在 Box 中更新的文件的唯一标识符 |
| body |字符串（二进制） |是 |body |无 |要在 Box 中更新的文件的内容 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

### <a name="when-a-file-is-modified"></a>修改文件时
在 Box 文件夹中修改文件时，触发流。  
```GET: /datasets/default/triggers/onupdatedfile```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| folderId |字符串 |是 |查询 |无 |Box 中的文件夹的唯一标识符 |

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

## <a name="next-steps"></a>后续步骤
[创建逻辑应用](../app-service-logic/app-service-logic-create-a-logic-app.md)。




<!--HONumber=Nov16_HO3-->


