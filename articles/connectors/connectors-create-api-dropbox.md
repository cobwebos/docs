---
title: "在 Azure 逻辑应用中使用 Dropbox 连接器 | Microsoft Docs"
description: "使用 Azure 应用服务创建逻辑应用。 连接到 Dropbox 管理你的文件。 你可以在 Dropbox 中执行各种操作，例如上传、更新、获取和删除文件。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: cb0ae033-aba7-4ac9-beaa-be561a0f0cac
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/15/2016
ms.author: mandia; ladocs
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 0d09580c60fd620811b539147439d0922839fe7e
ms.contentlocale: zh-cn
ms.lasthandoff: 05/26/2017


---
# <a name="get-started-with-the-dropbox-connector"></a>Dropbox 连接器入门
连接到 Dropbox 管理你的文件。 你可以在 Dropbox 中执行各种操作，例如上传、更新、获取和删除文件。

若要使用 [任何连接器](apis-list.md) ，首先需要创建逻辑应用。 可通过 [立即创建逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md) 开始操作。

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

## <a name="connector-specific-details"></a>特定于连接器的详细信息

在[连接器详细信息](/connectors/dropbox/)中查看在 Swagger 中定义的触发器和操作，并查看限制。

## <a name="more-connectors"></a>更多连接器
返回到 [API 列表](apis-list.md)。
