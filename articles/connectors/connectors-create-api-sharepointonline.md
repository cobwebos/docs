---
title: "了解如何在逻辑应用中使用 SharePoint Online 连接器 | Microsoft Docs"
description: "使用 SharePoint Online 连接器创建逻辑应用以管理 SharePoint 上的列表。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: e0ec3149-507a-409d-8e7b-d5fbded006ce
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/19/2016
ms.author: mandia; ladocs
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 96fc1347604c0c6cc2c2463a5dbd83b560183a16
ms.contentlocale: zh-cn
ms.lasthandoff: 05/26/2017


---
# <a name="get-started-with-the-sharepoint-online-connector"></a>SharePoint Online 连接器入门
使用 SharePoint Online 连接器管理 SharePoint 列表。  

若要使用 [任何连接器](apis-list.md) ，首先需要创建逻辑应用。 可通过 [立即创建逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md) 开始操作。

## <a name="connect-to-sharepoint-online"></a>连接到 SharePoint Online
需要先创建到任何服务的*连接*，然后逻辑应用才能访问该服务。 [连接](connectors-overview.md)提供逻辑应用和其他服务之间的连接性。  

### <a name="create-a-connection-to-sharepoint-online"></a>创建到 SharePoint Online 的连接
> [!INCLUDE [Steps to create a connection to SharePoint](../../includes/connectors-create-api-sharepointonline.md)]


## <a name="use-a-sharepoint-online-trigger"></a>使用 SharePoint Online 触发器
触发器是用于启动在逻辑应用中定义的工作流的事件。 [了解有关触发器的详细信息](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)。  

> [!INCLUDE [Steps to create a SharePoint Online trigger](../../includes/connectors-create-api-sharepointonline-trigger.md)]


## <a name="use-a-sharepoint-online-action"></a>使用 SharePoint Online 操作
操作是指在逻辑应用中定义的由工作流执行的操作。 [了解有关操作的详细信息](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)。  

> [!INCLUDE [Steps to create a SharePoint Online action](../../includes/connectors-create-api-sharepointonline-action.md)]


## <a name="connector-specific-details"></a>特定于连接器的详细信息

在[连接器详细信息](/connectors/sharepoint/)中查看在 Swagger 中定义的触发器和操作，并查看限制。

## <a name="next-steps"></a>后续步骤
[创建逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)


