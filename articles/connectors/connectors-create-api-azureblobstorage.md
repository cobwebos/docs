---
title: "在逻辑应用中添加 Azure Blob 存储连接器 | Microsoft Docs"
description: "使用 REST API 参数的 Azure Blob 存储连接器概述"
services: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: b5dc3f75-6bea-420b-b250-183668d2848d
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 9a739af2c9f6adaec58bfd7b339c370fae113641


---
# <a name="get-started-with-the-azure-blob-storage-connector"></a>Azure Blob 存储连接器入门
Azure Blob 存储是一项用于存储大量非结构化数据的服务。 在 Azure Blob 存储中执行各种操作，例如上传、更新、获取和删除 Blob。 

通过 Azure Blob 存储，你可以：

* 通过上传新项目或获取最近更新的文件生成你的工作流。
* 使用获取文件元数据、删除文件、复制文件等各项操作。 例如，在 Azure 网站中更新某个工具时（触发器），更新 Blob 存储中的文件（操作）。 

本主题演示了如何在逻辑应用中使用 Blob 存储连接器，还列出了相关操作。

> [!NOTE]
> 此文章版本适用于逻辑应用通用版本 (GA)。 
> 
> 

若要了解有关逻辑应用的详细信息，请参阅[什么是逻辑应用](../app-service-logic/app-service-logic-what-are-logic-apps.md)和[创建逻辑应用](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## <a name="connect-to-azure-blob-storage"></a>连接到 Azure Blob 存储
需要先创建到任何服务的*连接*，然后逻辑应用才能访问该服务。 连接提供逻辑应用和其他服务之间的连接性。 例如，若要连接到存储帐户，请首先创建 Blob 存储*连接*。 若要创建连接，请输入通常用于访问要连接到的服务的凭据。 因此，在 Azure 存储中，将凭据输入到存储帐户以创建连接。 

#### <a name="create-the-connection"></a>创建连接
> [!INCLUDE [Create a connection to Azure blob storage](../../includes/connectors-create-api-azureblobstorage.md)]
> 
> 

## <a name="use-a-trigger"></a>使用触发器
此连接器没有任何触发器。 使用其他触发器启动逻辑应用，例如重复触发器、HTTP Webhook 触发器、可用于其他连接器的触发器等。 [创建逻辑应用](../app-service-logic/app-service-logic-create-a-logic-app.md)提供了一个示例。

## <a name="use-an-action"></a>使用操作
操作是指在逻辑应用中定义的由工作流执行的操作。

1. 选择加号。 可看到多个选项：“添加操作”、“添加条件”或“更多”选项之一。
   
    ![](./media/connectors-create-api-azureblobstorage/add-action.png)
2. 选择“添加操作”。
3. 在文本框中，键入“Blob”获取所有可用操作的列表。
   
    ![](./media/connectors-create-api-azureblobstorage/actions.png) 
4. 在我们的示例中，选择“AzureBlob - 使用路径获取文件元数据”。 如果连接已存在，则选择“...”（显示选取器）按钮选择一个文件。
   
    ![](./media/connectors-create-api-azureblobstorage/sample-file.png)
   
    如果提示你提供连接信息，则输入详细信息以创建连接。 本主题中的[创建连接](connectors-create-api-azureblobstorage.md#create-the-connection)介绍了这些属性。 
   
   > [!NOTE]
   > 在此示例中，我们将获取文件的元数据。 若要查看元数据，请使用其他连接器添加可新建文件的其他操作。 例如根据元数据添加可新建“test”文件的 OneDrive 操作。 
   > 
   > 
5. **保存**更改（工具栏的左上角）。 你的逻辑应用将保存，并且可能自动启用。

> [!TIP]
> [存储资源管理器](http://storageexplorer.com/)是用于管理多个存储帐户的出色工具。
> 
> 

## <a name="technical-details"></a>技术详细信息
## <a name="storage-blob-actions"></a>存储 Blob 操作
| 操作 | 说明 |
| --- | --- |
| [获取文件元数据](connectors-create-api-azureblobstorage.md#get-file-metadata) |此操作可使用文件 ID 获取文件元数据。 |
| [更新文件](connectors-create-api-azureblobstorage.md#update-file) |此操作可更新文件。 |
| [删除文件](connectors-create-api-azureblobstorage.md#delete-file) |此操作可删除文件。 |
| [使用路径获取文件元数据](connectors-create-api-azureblobstorage.md#get-file-metadata-using-path) |此操作可使用路径获取文件元数据。 |
| [使用路径获取文件内容](connectors-create-api-azureblobstorage.md#get-file-content-using-path) |此操作可使用路径获取文件内容。 |
| [获取文件内容](connectors-create-api-azureblobstorage.md#get-file-content) |此操作可使用 ID 获取文件内容。 |
| [创建文件](connectors-create-api-azureblobstorage.md#create-file) |此操作可上传文件。 |
| [复制文件](connectors-create-api-azureblobstorage.md#copy-file) |此操作可将文件复制到 Azure Blob 存储。 |
| [将存档提取到文件夹](connectors-create-api-azureblobstorage.md#extract-archive-to-folder) |此操作将存档文件提取到文件夹中（示例：.zip）。 |

### <a name="action-details"></a>操作详细信息
在此部分中，查看有关每项操作的具体详细信息，包括任何必需或可选的输入属性以及与连接器相关联的任何相应输出。

#### <a name="get-file-metadata"></a>获取文件元数据
此操作可使用文件 ID 获取文件元数据。  

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| ID* |文件 |选择文件 |

星号 (*) 表示该属性是必需的。

##### <a name="output-details"></a>输出详细信息
BlobMetadata

| 属性名称 | 数据类型 |
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

#### <a name="update-file"></a>更新文件
此操作可更新文件。  

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| ID* |文件 |选择文件 |
| body* |文件内容 |要更新的文件内容 |

星号 (*) 表示该属性是必需的。

##### <a name="output-details"></a>输出详细信息
BlobMetadata

| 属性名称 | 数据类型 |
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

#### <a name="delete-file"></a>删除文件
此操作可删除文件。  

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| ID* |文件 |选择文件 |

星号 (*) 表示该属性是必需的。

##### <a name="output-details"></a>输出详细信息
无。

#### <a name="get-file-metadata-using-path"></a>使用路径获取文件元数据
此操作可使用路径获取文件元数据。  

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| path* |文件路径 |选择文件 |

星号 (*) 表示该属性是必需的。

##### <a name="output-details"></a>输出详细信息
BlobMetadata

| 属性名称 | 数据类型 |
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

#### <a name="get-file-content-using-path"></a>使用路径获取文件内容
此操作可使用路径获取文件内容。  

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| path* |文件路径 |选择文件 |

星号 (*) 表示该属性是必需的。

##### <a name="output-details"></a>输出详细信息
无。

#### <a name="get-file-content"></a>获取文件内容
此操作可使用 ID 获取文件内容。  

| 属性名称 | 数据类型 | 说明 |
| --- | --- | --- |
| ID* |字符串 |选择文件 |

星号 (*) 表示该属性是必需的。

##### <a name="output-details"></a>输出详细信息
无。

#### <a name="create-file"></a>创建文件
此操作可上传文件。  

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| folderPath* |文件夹路径 |选择文件夹 |
| name* |文件名 |要上传的文件的名称 |
| body* |文件内容 |要上传的文件内容 |

星号 (*) 表示该属性是必需的。

##### <a name="output-details"></a>输出详细信息
BlobMetadata

| 属性名称 | 数据类型 |
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

#### <a name="copy-file"></a>复制文件
此操作可将文件复制到 Azure Blob 存储。  

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| source* |源 URL |指定源文件的 URL |
| destination* |目标文件路径 |指定目标文件路径，包括目标文件名 |
| overwrite |覆盖？ |是否应覆盖现有目标文件 (true/false)？ |

星号 (*) 表示该属性是必需的。

##### <a name="output-details"></a>输出详细信息
BlobMetadata

| 属性名称 | 数据类型 |
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

#### <a name="extract-archive-to-folder"></a>将存档提取到文件夹
此操作将存档文件提取到文件夹中（示例：.zip）。  

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| source* |源存档文件路径 |选择存档文件 |
| destination* |目标文件夹路径 |选择要提取的内容 |
| overwrite |覆盖？ |是否应覆盖现有目标文件 (true/false)？ |

星号 (*) 表示该属性是必需的。

##### <a name="output-details"></a>输出详细信息
BlobMetadata

| 属性名称 | 数据类型 |
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

## <a name="http-responses"></a>HTTP 响应
调用不同的操作时，可能会收到特定响应。 下表概述了这些响应及其说明：  

| Name | 说明 |
| --- | --- |
| 200 |确定 |
| 202 |已接受 |
| 400 |错误的请求 |
| 401 |未授权 |
| 403 |禁止 |
| 404 |未找到 |
| 500 |内部服务器错误。 发生未知错误 |
| default |操作失败。 |

## <a name="next-steps"></a>后续步骤
[创建逻辑应用](../app-service-logic/app-service-logic-create-a-logic-app.md)。 在我们的 [API 列表](apis-list.md)中了解逻辑应用中的其他可用连接器。




<!--HONumber=Nov16_HO3-->


