---
title: "在逻辑应用中添加 OneDrive 连接器 | Microsoft Docs"
description: "使用 REST API 参数的 OneDrive 连接器概述"
services: logic-apps
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 47a8582a-1b1a-4fc3-beb5-97c60c4306fe
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: 66fc8f7e1da55dbe6bb1dd8b8d6a535c498c1cf7
ms.openlocfilehash: 60c1d307ede5d66f8e92b048581148d6c7112dac


---
# <a name="get-started-with-the-onedrive-connector"></a>OneDrive 连接器入门
连接到 OneDrive，管理你的文件（包括上传、获取、删除文件）等。 

使用 OneDrive，你可以： 

* 通过在 OneDrive 中存储文件生成你的工作流，或更新 OneDrive 中的现有文件。 
* 在 OneDrive 内创建或更新文件时，使用触发器启动工作流。
* 使用创建文件、删除文件等操作。 例如，收到带有附件的新 Office 365 电子邮件时（触发器），在 OneDrive 中新建文件（操作）。

本主题演示了如何在逻辑应用中使用 OneDrive 连接器，还列出了触发器和操作。

> [!NOTE]
> 此文章版本适用于逻辑应用通用版本 (GA)。 
> 
> 

若要了解有关逻辑应用的详细信息，请参阅[什么是逻辑应用](../logic-apps/logic-apps-what-are-logic-apps.md)和[创建逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)。

## <a name="connect-to-onedrive"></a>连接到 OneDrive
需要先创建到任何服务的*连接*，然后逻辑应用才能访问该服务。 连接提供逻辑应用和其他服务之间的连接性。 例如，若要连接到 OneDrive，首先需要 OneDrive *连接*。 若要创建连接，请输入通常用于访问要连接到的服务的凭据。 因此，在 OneDrive 中，将凭据输入到 OneDrive 帐户以创建连接。

### <a name="create-the-connection"></a>创建连接
> [!INCLUDE [Steps to create a connection to OneDrive](../../includes/connectors-create-api-onedrive.md)]
> 
> 

## <a name="use-a-trigger"></a>使用触发器
触发器是用于启动在逻辑应用中定义的工作流的事件。 触发器以你希望的间隔和频率“轮询”服务。 [了解有关触发器的详细信息](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)。

1. 在逻辑应用中，键入“onedrive”获取触发器列表：  
   
    ![](./media/connectors-create-api-onedrive/onedrive-1.png)
2. 选择“修改文件时”。 如果连接已存在，选择“显示选取器”按钮选择文件夹。
   
    ![](./media/connectors-create-api-onedrive/sample-folder.png)
   
    如果提示你登录，则输入登录详细信息以创建连接。 本主题中的[创建连接](connectors-create-api-onedrive.md#create-the-connection)列出了相关步骤。 
   
   > [!NOTE]
   > 在此示例中，逻辑应用在更新文件夹中的所选文件时运行。 若要查看此触发器的结果，请添加另一个向你发送电子邮件的操作。 例如，添加在更新文件时向你发送电子邮件的 Office 365 Outlook“发送电子邮件”操作。 
   > 
   > 
3. 选择“编辑”按钮并设置“频率”和“间隔”值。 例如，如果你希望触发器每 15 分钟轮询一次，将“频率”设置为“分钟”，将“间隔”设置为“15”。 
   
    ![](./media/connectors-create-api-onedrive/trigger-properties.png)
4. **保存**更改（工具栏的左上角）。 你的逻辑应用将保存，并且可能自动启用。

## <a name="use-an-action"></a>使用操作
操作是指在逻辑应用中定义的由工作流执行的操作。 [了解有关操作的详细信息](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)。

1. 选择加号。 可看到多个选项：“添加操作”、“添加条件”或“更多”选项之一。
   
    ![](./media/connectors-create-api-onedrive/add-action.png)
2. 选择“添加操作”。
3. 在文本框中，键入“onedrive”获取所有可用操作的列表。
   
    ![](./media/connectors-create-api-onedrive/onedrive-actions.png) 
4. 在我们的示例中，选择“OneDrive - 创建文件”。 如果连接已存在，选择“文件夹路径”放入文件、输入“文件名”，然后选择所需的“文件内容”：  
   
    ![](./media/connectors-create-api-onedrive/sample-action.png)
   
    如果提示你提供连接信息，则输入详细信息以创建连接。 本主题中的[创建连接](connectors-create-api-onedrive.md#create-the-connection)介绍了这些属性。 
   
   > [!NOTE]
   > 在此示例中，我们在 OneDrive 文件夹中新建文件。 可使用来自其他触发器的输出创建 OneDrive 文件。 例如，添加 Office 365 Outlook“新电子邮件到达时”触发器。 然后添加可在 ForEach 内使用附件和内容类型字段的 OneDrive“创建文件”操作，在 OneDrive 中新建文件。 
   > 
   > ![](./media/connectors-create-api-onedrive/foreach-action.png)
   > 
   > 
5. **保存**更改（工具栏的左上角）。 你的逻辑应用将保存，并且可能自动启用。

## <a name="technical-details"></a>技术详细信息
## <a name="triggers"></a>触发器
| 触发器 | 说明 |
| --- | --- |
| [创建文件时](connectors-create-api-onedrive.md#when-a-file-is-created) |此操作可在文件夹中新建文件时触发流。 |
| [修改文件时](connectors-create-api-onedrive.md#when-a-file-is-modified) |此操作可在文件夹中修改文件时触发流。 |

## <a name="actions"></a>操作
| 操作 | 说明 |
| --- | --- |
| [获取文件元数据](connectors-create-api-onedrive.md#get-file-metadata) |此操作可获取文件的元数据。 |
| [更新文件](connectors-create-api-onedrive.md#update-file) |此操作可更新文件。 |
| [删除文件](connectors-create-api-onedrive.md#delete-file) |此操作可删除文件。 |
| [使用路径获取文件元数据](connectors-create-api-onedrive.md#get-file-metadata-using-path) |此操作可使用路径获取文件的元数据。 |
| [使用路径获取文件内容](connectors-create-api-onedrive.md#get-file-content-using-path) |此操作可使用路径获取文件的内容。 |
| [获取文件内容](connectors-create-api-onedrive.md#get-file-content) |此操作可获取文件的内容。 |
| [创建文件](connectors-create-api-onedrive.md#create-file) |此操作可创建文件。 |
| [复制文件](connectors-create-api-onedrive.md#copy-file) |此操作可将文件复制到 OneDrive。 |
| [列出文件夹中的文件](connectors-create-api-onedrive.md#list-files-in-folder) |此操作可获取文件夹中文件和子文件夹的列表。 |
| [列出根文件夹中的文件](connectors-create-api-onedrive.md#list-files-in-root-folder) |此操作可获取根文件夹中文件和子文件夹的列表。 |
| [将存档提取到文件夹](connectors-create-api-onedrive.md#extract-archive-to-folder) |此操作将存档文件提取到文件夹中（示例：.zip）。 |

### <a name="action-details"></a>操作详细信息
在此部分中，查看有关每项操作的具体详细信息，包括任何必需或可选的输入属性以及与连接器相关联的任何相应输出。

#### <a name="get-file-metadata"></a>获取文件元数据
此操作可获取文件的元数据。 

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
| body* |文件内容 |文件的内容 |

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
此操作可使用路径获取文件的元数据。 

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
此操作可使用路径获取文件的内容。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| path* |文件路径 |选择文件 |

星号 (*) 表示该属性是必需的。

##### <a name="output-details"></a>输出详细信息
无。

#### <a name="get-file-content"></a>获取文件内容
此操作可获取文件的内容。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| ID* |文件 |选择文件 |

星号 (*) 表示该属性是必需的。

##### <a name="output-details"></a>输出详细信息
无。

#### <a name="create-file"></a>创建文件
此操作可创建文件。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| folderPath* |文件夹路径 |选择文件夹 |
| name* |文件名 |文件的名称 |
| body* |文件内容 |文件的内容 |

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
此操作可将文件复制到 OneDrive。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| source* |源 URL |源文件的 URL |
| destination* |目标文件路径 |目标文件路径，包括目标文件名 |
| overwrite |覆盖？ |如果设置为“true”，覆盖目标文件 |

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

#### <a name="when-a-file-is-created"></a>创建文件时
此操作可在文件夹中新建文件时触发流。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| folderId* |文件夹 |选择文件夹 |

星号 (*) 表示该属性是必需的。

##### <a name="output-details"></a>输出详细信息
无。

#### <a name="when-a-file-is-modified"></a>修改文件时
此操作可在文件夹中修改文件时触发流。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| folderId* |文件夹 |选择文件夹 |

星号 (*) 表示该属性是必需的。

##### <a name="output-details"></a>输出详细信息
无。

#### <a name="list-files-in-folder"></a>列出文件夹中的文件
此操作可获取文件夹中文件和子文件夹的列表。

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| ID* |文件夹 |选择文件夹 |

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

#### <a name="list-files-in-root-folder"></a>列出根文件夹中的文件
此操作可获取根文件夹中文件和子文件夹的列表。 

此调用没有任何参数。

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
| source* |源存档文件路径 |存档文件的路径 |
| destination* |目标文件夹路径 |要提取存档内容的路径 |
| overwrite |覆盖？ |如果设置为“true”，覆盖目标文件 |

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
下表概述了对操作和触发器的响应以及响应说明：  

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
[创建逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)。 在我们的 [API 列表](apis-list.md)中了解逻辑应用中的其他可用连接器。




<!--HONumber=Jan17_HO3-->


