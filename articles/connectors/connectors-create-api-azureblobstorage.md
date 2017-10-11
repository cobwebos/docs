---
title: "在逻辑应用中添加 Azure Blob 存储连接器 | Microsoft Docs"
description: "入门并在逻辑应用中配置 Azure Blob 存储连接器"
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
ms.date: 05/02/2017
ms.author: mandia; ladocs
ms.openlocfilehash: bc7908868828bd1628633cf9e57f8c44f8000827
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="use-the-azure-blob-storage-connector-in-a-logic-app"></a>在逻辑应用中使用 Azure Blob 存储连接器
使用 Azure Blob 存储连接器上传、更新、获取和删除存储帐户中的 Blob，一切操作皆在逻辑应用中完成。  

通过 Azure Blob 存储，可以：

* 通过上传新项目或获取最近更新的文件生成工作流。
* 使用获取文件元数据、删除文件、复制文件等各项操作。 例如，在 Azure 网站中更新某个工具时（触发器），更新 Blob 存储中的文件（操作）。 

本主题介绍如何在逻辑应用中使用 Blob 存储连接器。

若要了解有关逻辑应用的详细信息，请参阅[什么是逻辑应用](../logic-apps/logic-apps-what-are-logic-apps.md)和[创建逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)。

若要了解有关逻辑应用的详细信息，请参阅[什么是逻辑应用](../logic-apps/logic-apps-what-are-logic-apps.md)和[创建逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)。

## <a name="connect-to-azure-blob-storage"></a>连接到 Azure Blob 存储
在逻辑应用能够访问任何服务前，需要先创建到该服务的*连接*。 连接提供逻辑应用和其他服务之间的连接性。 例如，若要连接到存储帐户，请首先创建 Blob 存储*连接*。 若要创建连接，请输入通常用于访问要连接到的服务的凭据。 因此，在 Azure 存储中，将凭据输入到存储帐户以创建连接。 

#### <a name="create-the-connection"></a>创建连接
> [!INCLUDE [Create a connection to Azure blob storage](../../includes/connectors-create-api-azureblobstorage.md)]

## <a name="use-a-trigger"></a>使用触发器
此连接器没有任何触发器。 使用其他触发器启动逻辑应用，例如重复触发器、HTTP Webhook 触发器、可用于其他连接器的触发器等。 [创建逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)提供了一个示例。

## <a name="use-an-action"></a>使用操作
操作是指在逻辑应用中定义的由工作流执行的操作。

1. 选择加号。 可看到多个选项：“添加操作”、“添加条件”或“更多”选项之一。
   
    ![](./media/connectors-create-api-azureblobstorage/add-action.png)
2. 选择“添加操作”。
3. 在文本框中，键入“Blob”获取所有可用操作的列表。
   
    ![](./media/connectors-create-api-azureblobstorage/actions.png) 
4. 在我们的示例中，选择“AzureBlob - 使用路径获取文件元数据”。 如果连接已存在，则选择“...”（显示选取器）按钮选择一个文件。
   
    ![](./media/connectors-create-api-azureblobstorage/sample-file.png)
   
    如果提示提供连接信息，则输入详细信息以创建连接。 本主题中的[创建连接](connectors-create-api-azureblobstorage.md#create-the-connection)介绍了这些属性。 
   
   > [!NOTE]
   > 在此示例中，我们将获取文件的元数据。 若要查看元数据，请使用其他连接器添加可新建文件的其他操作。 例如根据元数据添加可新建“test”文件的 OneDrive 操作。 


5. **保存**更改（工具栏的左上角）。 逻辑应用将保存，并且可能自动启用。

> [!TIP]
> [存储资源管理器](http://storageexplorer.com/)是用于管理多个存储帐户的出色工具。

## <a name="connector-specific-details"></a>特定于连接器的详细信息

在[连接器详细信息](/connectors/azureblobconnector/)中查看在 Swagger 中定义的触发器和操作，并查看限制。 

## <a name="next-steps"></a>后续步骤
[创建逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)。 在我们的 [API 列表](apis-list.md)中了解逻辑应用中的其他可用连接器。

