---
title: Dropbox | Microsoft Docs
description: "使用 Azure App Service 创建逻辑应用。 连接到 Dropbox 管理你的文件。 你可以在 Dropbox 中执行各种操作，例如上传、更新、获取和删除文件。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: cb0ae033-aba7-4ac9-beaa-be561a0f0cac
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/15/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 9c74b25a2ac5e2088a841d97920035376b7f3f11
ms.openlocfilehash: a8ecad3552f35c02c07fe33aa8531fb462b27002


---
# <a name="get-started-with-the-dropbox-connector"></a>Dropbox 连接器入门
连接到 Dropbox 管理你的文件。 你可以在 Dropbox 中执行各种操作，例如上传、更新、获取和删除文件。

若要使用“任何连接器”[](apis-list.md)，首先需要创建逻辑应用。 可通过“立即创建逻辑应用”[](../logic-apps/logic-apps-create-a-logic-app.md)开始操作。

## <a name="connect-to-dropbox"></a>连接到 Dropbox
需要先创建到任何服务的*连接*，然后逻辑应用才能访问该服务。 连接提供逻辑应用和其他服务之间的连接性。 例如，若要连接到 Dropbox，首先需要 Dropbox *连接*。 若要创建连接，需要提供通常用于访问要连接到的服务的凭据。 因此，在 Dropbox 示例中，需要 Dropbox 帐户的凭据，才能创建到 Dropbox 的连接。 [了解有关连接的详细信息]()

### <a name="create-a-connection-to-dropbox"></a>创建到 Dropbox 的连接
> [!INCLUDE [Steps to create a connection to Dropbox](../../includes/connectors-create-api-dropbox.md)]
> 
> 

## <a name="use-a-dropbox-trigger"></a>使用 Dropbox 触发器
触发器是用于启动在逻辑应用中定义的工作流的事件。 [了解有关触发器的详细信息](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)。

在此示例中，我们将使用“创建文件时”触发器。 发生此触发器时，我们将调用“使用路径获取文件内容”Dropbox 操作。 

1. 在逻辑应用设计器上的搜索框中输入 *dropbox*，然后选择“Dropbox - 创建文件时”触发器。      
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-trigger.PNG)  
2. 选择想要跟踪文件创建的文件夹。 选择“...”（已在红色框中标识），并浏览到要为其选择触发器输入的文件夹。  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-trigger-2.PNG)  

## <a name="use-a-dropbox-action"></a>使用 Dropbox 操作
操作是指在逻辑应用中定义的由工作流执行的操作。 [了解有关操作的详细信息](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)。

现已添加触发器，按照以下步骤添加将获取新文件内容的操作。

1. 选择“+ 新步骤”，添加要在创建新文件时采取的操作。  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action.PNG)
2. 选择“添加操作”。 这将打开可搜索要采取的任何操作的搜索框。  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-2.PNG)
3. 输入 *dropbox*，搜索与 Dropbox 相关的操作。  
4. 选择“Dropbox - 使用路径获取文件内容”作为要在所选 Dropbox 文件夹中新建文件时采取的操作。 操作控制块打开。 系统将提示你授权逻辑应用访问你的 Dropbox 帐户（如果之前尚未这样做）。  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-3.PNG)  
5. 选择“...”（位于“文件路径”控件的右侧）并浏览到要使用的文件路径。 或者，使用“文件路径”令牌加快逻辑应用创建速度。  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-4.PNG)  
6. 保存所做工作，并在 Dropbox 中新建文件以激活工作流。  

## <a name="technical-details"></a>技术详细信息
下面详细介绍了此连接支持的触发器、操作和响应：

## <a name="dropbox-triggers"></a>Dropbox 触发器
Dropbox 连接器具有以下触发器：  

| 触发器 | 说明 |
| --- | --- |
| [创建文件时](connectors-create-api-dropbox.md#when-a-file-is-created) |此操作可在文件夹中新建文件时触发流。 |
| [修改文件时](connectors-create-api-dropbox.md#when-a-file-is-modified) |此操作可在文件夹中修改文件时触发流。 |

## <a name="dropbox-actions"></a>Dropbox 操作
Dropbox 连接器具有以下操作：

| 操作 | 说明 |
| --- | --- |
| [获取文件元数据](connectors-create-api-dropbox.md#get-file-metadata) |此操作可获取文件的元数据。 |
| [更新文件](connectors-create-api-dropbox.md#update-file) |此操作可更新文件。 |
| [删除文件](connectors-create-api-dropbox.md#delete-file) |此操作可删除文件。 |
| [使用路径获取文件元数据](connectors-create-api-dropbox.md#get-file-metadata-using-path) |此操作可使用路径获取文件的元数据。 |
| [使用路径获取文件内容](connectors-create-api-dropbox.md#get-file-content-using-path) |此操作可使用路径获取文件的内容。 |
| [获取文件内容](connectors-create-api-dropbox.md#get-file-content) |此操作可获取文件的内容。 |
| [创建文件](connectors-create-api-dropbox.md#create-file) |此操作可创建文件。 |
| [复制文件](connectors-create-api-dropbox.md#copy-file) |此操作可将文件复制到 Dropbox。 |
| [列出文件夹中的文件](connectors-create-api-dropbox.md#list-files-in-folder) |此操作可获取文件夹中文件和子文件夹的列表。 |
| [列出根文件夹中的文件](connectors-create-api-dropbox.md#list-files-in-root-folder) |此操作可获取根文件夹中文件和子文件夹的列表。 |
| [将存档提取到文件夹](connectors-create-api-dropbox.md#extract-archive-to-folder) |此操作将存档文件提取到文件夹中（示例：.zip）。 |

### <a name="action-details"></a>操作详细信息
下面详细介绍了此连接器的操作和触发器及其响应：

### <a name="get-file-metadata"></a>获取文件元数据
此操作可获取文件的元数据。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| ID* |文件 |选择文件 |

* 指示属性是必需的

#### <a name="output-details"></a>输出详细信息
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

### <a name="update-file"></a>更新文件
此操作可更新文件。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| ID* |文件 |选择文件 |
| body* |文件内容 |文件的内容 |

* 指示属性是必需的

#### <a name="output-details"></a>输出详细信息
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

### <a name="delete-file"></a>删除文件
此操作可删除文件。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| ID* |文件 |选择文件 |

* 指示属性是必需的

### <a name="get-file-metadata-using-path"></a>使用路径获取文件元数据
此操作可使用路径获取文件的元数据。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| path* |文件路径 |选择文件 |

* 指示属性是必需的

#### <a name="output-details"></a>输出详细信息
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

### <a name="get-file-content-using-path"></a>使用路径获取文件内容
此操作可使用路径获取文件的内容。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| path* |文件路径 |选择文件 |

* 指示属性是必需的

### <a name="get-file-content"></a>获取文件内容
此操作可获取文件的内容。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| ID* |文件 |选择文件 |

* 指示属性是必需的

### <a name="create-file"></a>创建文件
此操作可创建文件。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| folderPath* |文件夹路径 |选择文件夹 |
| name* |文件名 |文件的名称 |
| body* |文件内容 |文件的内容 |

* 指示属性是必需的

#### <a name="output-details"></a>输出详细信息
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

### <a name="copy-file"></a>复制文件
此操作可将文件复制到 Dropbox。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| source* |源 URL |源文件的 URL |
| destination* |目标文件路径 |目标文件路径，包括目标文件名 |
| overwrite |覆盖？ |如果设置为“true”，覆盖目标文件 |

* 指示属性是必需的

#### <a name="output-details"></a>输出详细信息
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

### <a name="when-a-file-is-created"></a>创建文件时
此操作可在文件夹中新建文件时触发流。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| folderId* |文件夹 |选择文件夹 |

* 指示属性是必需的

### <a name="when-a-file-is-modified"></a>修改文件时
此操作可在文件夹中修改文件时触发流。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| folderId* |文件夹 |选择文件夹 |

* 指示属性是必需的

### <a name="list-files-in-folder"></a>列出文件夹中的文件
此操作可获取文件夹中文件和子文件夹的列表。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| ID* |文件夹 |选择文件夹 |

* 指示属性是必需的

#### <a name="output-details"></a>输出详细信息
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

### <a name="list-files-in-root-folder"></a>列出根文件夹中的文件
此操作可获取根文件夹中文件和子文件夹的列表。 

此调用没有任何参数

#### <a name="output-details"></a>输出详细信息
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

### <a name="extract-archive-to-folder"></a>将存档提取到文件夹
此操作将存档文件提取到文件夹中（示例：.zip）。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| source* |源存档文件路径 |存档文件的路径 |
| destination* |目标文件夹路径 |要提取存档内容的路径 |
| overwrite |覆盖？ |如果设置为“true”，覆盖目标文件 |

* 指示属性是必需的

#### <a name="output-details"></a>输出详细信息
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
[创建逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)




<!--HONumber=Jan17_HO3-->


