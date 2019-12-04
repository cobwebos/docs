---
title: 从 Azure 逻辑应用连接到时差
description: 使用 Azure 逻辑应用自动执行任务和工作流，用以监视文件以及管理你的 Slack 帐户中的通道、组和消息
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 5f61009ee7b43be618e37acb4a783a54dbf11e55
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789165"
---
# <a name="monitor-and-manage-slack-with-azure-logic-apps"></a>使用 Azure 逻辑应用监视和管理 Slack

使用 Azure 逻辑应用和 Slack 连接器，可以创建用于监视 Slack 文件以及管理 Slack 通道、消息和组等等的自动化任务和工作流，例如：

* 监视何时创建了新文件。
* 创建、列出和加入通道 
* 发布消息。
* 创建组并设置“请勿打扰”。

可以使用触发器从 Slack 帐户获取响应，并使输出可用于其他操作。 可以使用通过 Slack 帐户执行任务的操作。 还可以让其他操作使用来自 Slack 操作的输出。 例如，当创建了新文件时，你可以使用 Office 365 Outlook 连接器发送电子邮件。 如果不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用？](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>必备组件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。 

* [Slack](https://slack.com/) 帐户和用户凭据

  你的凭据授权逻辑应用创建连接并访问你的 Slack 帐户。

* 有关[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知识

* 要在其中访问 Slack 帐户的逻辑应用。 若要使用 Slack 触发器启动，请[创建空白的逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 若要使用 Slack 操作，请使用一个触发器启动逻辑应用，例如 Slack 触发器或其他个触发器（例如**定期**触发器）。

## <a name="connect-to-slack"></a>连接到 Slack

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. 登录 [Azure门户](https://portal.azure.com)，然后在逻辑应用设计器中打开逻辑应用（如果尚未打开）。

1. 对于空白逻辑应用，请在搜索框中输入“slack”作为筛选器。 在触发器列表下，选择所需的触发器。 

   -或-

   对于现有逻辑应用，请在要添加操作的最后一个步骤下，选择“新建步骤”。 
   在搜索框中，输入“slack”作为筛选器。 
   在操作列表下，选择所需的操作。

   若要在步骤之间添加操作，请将鼠标指针移到步骤之间的箭头上。 
   选择出现的加号 ( **+** )，然后选择“添加操作”。

1. 如果提示你登录到 Slack，请登录到你的 Slack 工作区。 

   ![登录到 Slack 工作区](./media/connectors-create-api-slack/slack-sign-in-workspace.png)

1. 授权你的逻辑应用进行访问。

   ![授权访问 Slack](./media/connectors-create-api-slack/slack-authorize-access.png)

1. 为所选触发器或操作提供必要的详细信息。 若要继续构建逻辑应用的工作流，请添加更多操作。

## <a name="connector-reference"></a>连接器参考

有关触发器、操作和限制（请参阅连接器的 OpenAPI（以前称为 Swagger）说明）的技术详细信息，请查看连接器的[参考页](/connectors/slack/)。

## <a name="get-support"></a>获取支持

* 有关问题，请访问 [Azure 逻辑应用论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。
* 若要提交功能建议或对功能建议进行投票，请访问[逻辑应用用户反馈网站](https://aka.ms/logicapps-wish)。

## <a name="next-steps"></a>后续步骤

* 了解其他[逻辑应用连接器](../connectors/apis-list.md)
