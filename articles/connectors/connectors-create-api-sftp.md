---
title: "了解如何在逻辑应用中使用 SFTP 连接器 | Microsoft Docs"
description: "使用 Azure App Service 创建逻辑应用。 连接到 SFTP API 以发送和接收文件。 你可以执行各种操作，如创建、更新、获取或删除文件。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: 697eb8b0-4a66-40c7-be7b-6aa6b131c7ad
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/20/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 93b77197d69d8c6261ea786aecb653c69129dc9d


---
# <a name="get-started-with-the-sftp-connector"></a>SFTP 连接器入门
使用 SFTP 连接器访问 SFTP 帐户以发送和接收文件。 你可以执行各种操作，如创建、更新、获取或删除文件。  

若要使用“任何连接器”[](apis-list.md)，首先需要创建逻辑应用。 可通过“立即创建逻辑应用”[](../app-service-logic/app-service-logic-create-a-logic-app.md)开始操作。

## <a name="connect-to-sftp"></a>连接到 SFTP
需要先创建到任何服务的*连接*，然后逻辑应用才能访问该服务。 [连接](connectors-overview.md)提供逻辑应用和其他服务之间的连接性。  

### <a name="create-a-connection-to-sftp"></a>创建到 SFTP 的连接
> [!INCLUDE [Steps to create a connection to SFTP](../../includes/connectors-create-api-sftp.md)]
> 
> 

## <a name="use-an-sftp-trigger"></a>使用 SFTP 触发器
触发器是用于启动在逻辑应用中定义的工作流的事件。 [了解有关触发器的详细信息](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts)。  

在此示例中，我将演示如何使用“SFTP - 添加或修改文件时”触发器，在 SFTP 服务器上添加或修改文件时启动逻辑应用工作流。 在示例中，你还将了解如何添加用于检查新的或已修改文件内容的条件，并在其内容指示应在使用该内容前提取文件时作出提取文件的决策。 最后，你将了解如何添加提取文件内容并将提取的内容放置在 SFTP 服务器上某个文件夹中的操作。 

在企业示例中，你可以使用此触发器监视 SFTP 文件夹中表示客户订单的新文件。  然后可以使用 SFTP 连接器操作（如**获取文件内容**）获取订单内容进行进一步处理并存储在订单数据库中。

> [!INCLUDE [Steps to create an SFTP trigger](../../includes/connectors-create-api-sftp-trigger.md)]
> 
> 

## <a name="add-a-condition"></a>添加条件
> [!INCLUDE [Steps to add a condition](../../includes/connectors-create-api-sftp-condition.md)]
> 
> 

## <a name="use-an-sftp-action"></a>使用 SFTP 操作
操作是指在逻辑应用中定义的由工作流执行的操作。 [了解有关操作的详细信息](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts)。  

> [!INCLUDE [Steps to create an SFTP action](../../includes/connectors-create-api-sftp-action.md)]
> 
> 

## <a name="technical-details"></a>技术详细信息
下面详细介绍了此连接支持的触发器、操作和响应：

## <a name="sftp-triggers"></a>SFTP 触发器
SFTP 具有以下触发器：  

| 触发器 | 说明 |
| --- | --- |
| [添加或修改文件时](connectors-create-api-sftp.md#when-a-file-is-added-or-modified) |此操作可在文件夹中添加或修改文件时触发流。 |

## <a name="sftp-actions"></a>SFTP 操作
FTP 具有以下操作：

| 操作 | 说明 |
| --- | --- |
| [获取文件元数据](connectors-create-api-sftp.md#get-file-metadata) |此操作使用文件 ID 获取文件元数据。 |
| [更新文件](connectors-create-api-sftp.md#update-file) |此操作更新文件内容。 |
| [删除文件](connectors-create-api-sftp.md#delete-file) |此操作可删除文件。 |
| [使用路径获取文件元数据](connectors-create-api-sftp.md#get-file-metadata-using-path) |此操作使用文件路径获取文件元数据。 |
| [使用路径获取文件内容](connectors-create-api-sftp.md#get-file-content-using-path) |此操作使用文件路径获取文件内容。 |
| [获取文件内容](connectors-create-api-sftp.md#get-file-content) |此操作使用文件 ID 获取文件内容。 |
| [创建文件](connectors-create-api-sftp.md#create-file) |此操作将文件上传到 SFTP 服务器。 |
| [复制文件](connectors-create-api-sftp.md#copy-file) |此操作将文件复制到 SFTP 服务器。 |
| [列出文件夹中的文件](connectors-create-api-sftp.md#list-files-in-folder) |此操作获取包含在文件夹中的文件。 |
| [列出根文件夹中的文件](connectors-create-api-sftp.md#list-files-in-root-folder) |此操作获取根文件夹中的文件。 |
| [提取文件夹](connectors-create-api-sftp.md#extract-folder) |此操作将存档文件提取到文件夹中（示例：.zip）。 |

### <a name="action-details"></a>操作详细信息
下面详细介绍了此连接器的操作和触发器及其响应：

### <a name="get-file-metadata"></a>获取文件元数据
此操作使用文件 ID 获取文件元数据。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| ID* |文件 |指定文件 |

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
| ID* |文件 |指定文件 |
| body* |文件内容 |要更新的文件内容 |

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
| ID* |文件 |指定文件 |

* 指示属性是必需的

### <a name="get-file-metadata-using-path"></a>使用路径获取文件元数据
此操作使用文件路径获取文件元数据。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| path* |文件路径 |文件的唯一路径 |

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
| path* |文件路径 |文件的唯一路径 |

* 指示属性是必需的

### <a name="get-file-content"></a>获取文件内容
此操作使用文件 ID 获取文件内容。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| ID* |文件 |指定文件 |

* 指示属性是必需的

### <a name="create-file"></a>创建文件
此操作将文件上传到 SFTP 服务器。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| folderPath* |文件夹路径 |文件夹的唯一路径 |
| name* |文件名 |文件的名称 |
| body* |文件内容 |要创建的文件内容 |

* 指示属性是必需的

#### <a name="output-details"></a>输出详细信息
BlobMetadata

|  | 属性名称 | 数据类型 |
| --- | --- | --- |
| ID |字符串 | |
| Name |字符串 | |
| DisplayName |字符串 | |
| 路径 |字符串 | |
| LastModified |字符串 | |
| 大小 |integer | |
| MediaType |字符串 | |
| IsFolder |布尔值 | |
| ETag |字符串 | |
| FileLocator |字符串 | |

### <a name="copy-file"></a>复制文件
此操作将文件复制到 SFTP 服务器。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| source* |源文件路径 |源文件的路径 |
| destination* |目标文件路径 |目标文件的路径，包括文件名 |
| overwrite |覆盖？ |如果设置为“true”，覆盖目标文件 |

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

### <a name="when-a-file-is-added-or-modified"></a>添加或修改文件时
此操作可在文件夹中添加或修改文件时触发流。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| folderId* |文件夹 |指定文件夹 |

* 指示属性是必需的

### <a name="list-files-in-folder"></a>列出文件夹中的文件
此操作获取包含在文件夹中的文件。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| ID* |文件夹 |指定文件夹 |

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

### <a name="list-files-in-root-folder"></a>列出根文件夹中的文件
此操作获取根文件夹中的文件。 

此调用没有任何参数

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
此操作将存档文件提取到文件夹中（示例：.zip）。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| source* |源存档文件路径 |存档文件的路径 |
| destination* |目标文件夹路径 |目标文件夹的路径 |
| overwrite |覆盖？ |如果设置为“true”，覆盖目标文件 |

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


