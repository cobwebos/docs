---
title: 使用 Azure 逻辑应用连接到任何 HTTP 终结点 | Microsoft Docs
description: 使用 Azure 逻辑应用自动完成与任何 HTTP 终结点通信的任务和工作流
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: e11c6b4d-65a5-4d2d-8e13-38150db09c0b
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: 95a039c990dc9b6c852a69518f0a71c51a4747b7
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2018
ms.locfileid: "44302616"
---
# <a name="call-http-or-https-endpoints-with-azure-logic-apps"></a>使用 Azure 逻辑应用调用 HTTP 或 HTTPS 终结点

使用 Azure 逻辑应用和超文本传输协议 (HTTP) 连接器，可以通过生成逻辑应用来自动完成与任何 HTTP 或 HTTPS 终结点通信的工作流。 例如，可以监视网站的服务终结点。 当该终结点上发生某个事件（例如，网站关闭）时，该事件会触发逻辑应用的工作流并运行指定的操作。 

可将 HTTP 触发器用作工作流中的第一个步骤，用于定期检查或轮询某个终结点。 每次检查时，该触发器会向该终结点发送调用或请求。 该终结点的响应确定了逻辑应用的工作流是否运行。 触发器将响应中的任何内容传递到逻辑应用中的操作。 

可将 HTTP 操作用作工作流中的另一个步骤，用于调用所需的终结点。 该终结点的响应确定了工作流剩余操作的运行方式。

如果你不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用？](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请<a href="https://azure.microsoft.com/free/" target="_blank">注册一个免费 Azure 帐户</a>。 

* 要调用的目标终结点的 URL 

* 有关[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知识

* 要从中调用目标终结点的逻辑应用。若要从 HTTP 触发器开始，请[创建一个空白逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 若要使用 HTTP 操作，请使用触发器启动逻辑应用。

## <a name="add-http-trigger"></a>添加 HTTP 触发器

1. 登录到 [Azure 门户](https://portal.azure.com)，然后在逻辑应用设计器中打开该空白逻辑应用（如果尚未打开）。

1. 在搜索框中，输入“http”作为筛选器。 在触发器列表下，选择“HTTP”触发器。 

   ![选择 HTTP 触发器](./media/connectors-native-http/select-http-trigger.png)

1. 提供要包含在目标终结点调用中的 [HTTP 触发器参数和值](../logic-apps/logic-apps-workflow-actions-triggers.md##http-trigger)。 设置重复周期，以确定触发器检查目标终结点的频率。

   ![输入 HTTP 触发器参数](./media/connectors-native-http/http-trigger-parameters.png)

   有关 HTTP 触发器、参数和值的详细信息，请参阅[触发器和操作类型参考](../logic-apps/logic-apps-workflow-actions-triggers.md##http-trigger)。

1. 继续使用触发器激发时运行的操作生成逻辑应用的工作流。

## <a name="add-http-action"></a>添加 HTTP 操作

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. 登录到 [Azure 门户](https://portal.azure.com)，在逻辑应用设计器中打开逻辑应用（如果尚未打开）。

1. 在要添加 HTTP 操作的最后一个步骤下，选择“新建步骤”。 

   在此示例中，逻辑应用是通过用作第一个步骤的 HTTP 触发器启动的。

1. 在搜索框中，输入“http”作为筛选器。 在操作列表下，选择“HTTP”操作。

   ![选择“HTTP”操作](./media/connectors-native-http/select-http-action.png)

   若要在步骤之间添加操作，请将鼠标指针移到步骤之间的箭头上。 
   选择出现的加号 (**+**)，然后选择“添加操作”。

1. 提供要包含在目标终结点调用中的 [HTTP 操作参数和值](../logic-apps/logic-apps-workflow-actions-triggers.md##http-action)。 

   ![输入 HTTP 操作参数](./media/connectors-native-http/http-action-parameters.png)

1. 完成后，请务必保存逻辑应用。 在设计器工具栏上，选择“保存”。 

## <a name="authentication"></a>身份验证

若要设置身份验证，请在操作或触发器中选择“显示高级选项”。 有关 HTTP 触发器和操作可用的身份验证类型的详细信息，请参阅[触发器和操作类型参考](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication)。

## <a name="get-support"></a>获取支持

* 有关问题，请访问 [Azure 逻辑应用论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。
* 若要提交功能建议或对功能建议进行投票，请访问[逻辑应用用户反馈网站](http://aka.ms/logicapps-wish)。

## <a name="next-steps"></a>后续步骤

* 了解其他[逻辑应用连接器](../connectors/apis-list.md)
