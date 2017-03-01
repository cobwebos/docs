---
title: "了解如何在逻辑应用中使用 FTP 连接器 | Microsoft Docs"
description: "使用 Azure App Service 创建逻辑应用。 连接到 FTP 服务器管理你的文件。 你可以在 FTP 服务器中执行各种操作，例如上传、更新、获取和删除文件。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: d83c55fe-eb59-4b7b-a5ec-afac5c772616
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/22/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 66fc8f7e1da55dbe6bb1dd8b8d6a535c498c1cf7
ms.openlocfilehash: 2634d0101c1cd4535922e8844f0beb6b523e2dea


---
# <a name="get-started-with-the-ftp-connector"></a>FTP 连接器入门
使用 FTP 连接器在 FTP 服务器上监视、管理和创建文件。 

若要使用[“任何连接器”](apis-list.md)，首先需要创建逻辑应用。 可通过[“立即创建逻辑应用”](../logic-apps/logic-apps-create-a-logic-app.md)开始操作。

## <a name="connect-to-ftp"></a>连接到 FTP
需要先创建到任何服务的*连接*，然后逻辑应用才能访问该服务。 [连接](connectors-overview.md)提供逻辑应用和其他服务之间的连接性。  

### <a name="create-a-connection-to-ftp"></a>创建到 FTP 的连接
> [!INCLUDE [Steps to create a connection to FTP](../../includes/connectors-create-api-ftp.md)]
> 
> 

## <a name="use-a-ftp-trigger"></a>使用 FTP 触发器
触发器是用于启动在逻辑应用中定义的工作流的事件。 [了解有关触发器的详细信息](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)。  

> [!IMPORTANT]
> FTP 连接器需要可从 Internet 访问并配置为在 PASSIVE 模式下运行的 FTP 服务器。 此外，FTP 连接器**与隐式 FTPS (FTP over SSL) 不兼容**。 FTP 连接器仅支持显式 FTPS (FTP over SSL)。  
> 
> 

在此示例中，我将演示如何使用“FTP - 添加或修改文件时”触发器，在 FTP 服务器上添加或修改文件时启动逻辑应用工作流。 在企业示例中，你可以使用此触发器监视 TFP 文件夹中表示客户订单的新文件。  然后可以使用 FTP 连接器操作（如**获取文件内容**）获取订单内容进行进一步处理并存储在订单数据库中。

1. 在逻辑应用设计器上的搜索框中输入 *ftp*，然后选择“FTP - 添加或修改文件时”触发器。   
   ![FTP 触发器图 1](./media/connectors-create-api-ftp/ftp-trigger-1.png)  
   “添加或修改文件时”控件打开  
   ![FTP 触发器图 2](./media/connectors-create-api-ftp/ftp-trigger-2.png)  
2. 选择位于控件右侧的“...”。 这将打开文件夹选取器控件  
   ![FTP 触发器图 3](./media/connectors-create-api-ftp/ftp-trigger-3.png)  
3. 选择“>”（向右箭头），并通过浏览查找要监视新文件或已修改文件的文件夹。 选择该文件夹，注意文件夹现在显示在“文件夹”控件中。  
   ![FTP 触发器图 4](./media/connectors-create-api-ftp/ftp-trigger-4.png)   

在这种情况下，使用将在特定 FTP 文件夹中修改或创建文件时，开始运行工作流中的其他触发器和操作的触发器配置了逻辑应用。 

> [!NOTE]
> 若要使逻辑应用正常工作，必须包含至少一个触发器和一个操作。 按照下一部分中的步骤添加操作。  
> 
> 

## <a name="use-a-ftp-action"></a>使用 FTP 操作
操作是指在逻辑应用中定义的由工作流执行的操作。 [了解有关操作的详细信息](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)。  

现已添加触发器，按照以下步骤添加将获取由该触发器找到的新文件或已修改文件内容的操作。    

1. 选择“+ 新步骤”添加在 FTP 服务器上获取文件内容的操作。  
2. 选择**添加操作**链接。  
   ![FTP 操作图 1](./media/connectors-create-api-ftp/ftp-action-1.png)  
3. 输入 *FTP* 搜索与 FTP 相关的所有操作。
4. 选择“FTP - 获取文件内容”作为要在 FTP 文件夹中找到新文件或已修改文件时采取的操作。      
   ![FTP 操作图 2](./media/connectors-create-api-ftp/ftp-action-2.png)  
   “获取文件内容”控件打开。 **注意：**系统将提示你授权逻辑应用访问你的 FTP 服务器帐户（如果之前尚未这样做）。  
   ![FTP 操作图 3](./media/connectors-create-api-ftp/ftp-action-3.png)   
5. 选择“文件”控件（**文件*** 下方的空格）。 可在此处使用来自在 FTP 服务器上找到的新文件或已修改文件的任一属性。  
6. 选择“文件内容”选项。  
   ![FTP 操作图 4](./media/connectors-create-api-ftp/ftp-action-4.png)   
7. 控件更新，指示“FTP - 获取文件内容”操作将获取 FTP 服务器上新文件或已修改文件的“文件内容”。      
   ![FTP 操作图 5](./media/connectors-create-api-ftp/ftp-action-5.png)     
8. 保存所做工作，然后将文件添加到 FTP 文件夹以测试你的工作流。    

在这种情况下，使用在 FTP 服务器上发现新文件或已修改文件时，监视 TFP 服务器上的文件夹并启动工作流的触发器配置了逻辑应用。 

还使用获取新文件或已修改文件的内容操作配置了逻辑应用。

现在可以添加另一个操作（[如“SQL Server - 插入行”](connectors-create-api-sqlazure.md#insert-row)操作），将新文件或已修改文件的内容插入到 SQL 数据库表中。  

## <a name="technical-details"></a>技术详细信息
下面详细介绍了此连接支持的触发器、操作和响应：

## <a name="ftp-triggers"></a>FTP 触发器
FTP 具有以下触发器：  

| 触发器 | 说明 |
| --- | --- |
| [添加或修改文件时](connectors-create-api-ftp.md#when-a-file-is-added-or-modified) |此操作可在文件夹中添加或修改文件时触发流。 |

## <a name="ftp-actions"></a>FTP 操作
FTP 具有以下操作：

| 操作 | 说明 |
| --- | --- |
| [获取文件元数据](connectors-create-api-ftp.md#get-file-metadata) |此操作可获取文件的元数据。 |
| [更新文件](connectors-create-api-ftp.md#update-file) |此操作可更新文件。 |
| [删除文件](connectors-create-api-ftp.md#delete-file) |此操作可删除文件。 |
| [使用路径获取文件元数据](connectors-create-api-ftp.md#get-file-metadata-using-path) |此操作可使用路径获取文件的元数据。 |
| [使用路径获取文件内容](connectors-create-api-ftp.md#get-file-content-using-path) |此操作可使用路径获取文件的内容。 |
| [获取文件内容](connectors-create-api-ftp.md#get-file-content) |此操作可获取文件的内容。 |
| [创建文件](connectors-create-api-ftp.md#create-file) |此操作可创建文件。 |
| [复制文件](connectors-create-api-ftp.md#copy-file) |此操作可将文件复制到 FTP 服务器。 |
| [列出文件夹中的文件](connectors-create-api-ftp.md#list-files-in-folder) |此操作可获取文件夹中文件和子文件夹的列表。 |
| [列出根文件夹中的文件](connectors-create-api-ftp.md#list-files-in-root-folder) |此操作可获取根文件夹中文件和子文件夹的列表。 |
| [提取文件夹](connectors-create-api-ftp.md#extract-folder) |此操作将存档文件提取到文件夹中（示例：.zip）。 |

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
此操作可更新文件。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| ID* |文件 |选择文件 |
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
此操作可将文件复制到 FTP 服务器。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| source* |源 URL |源文件的 URL |
| destination* |目标文件路径 |目标文件路径，包括目标文件名 |
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
此操作可获取根文件夹中文件和子文件夹的列表。 

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
[创建逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)




<!--HONumber=Jan17_HO3-->


