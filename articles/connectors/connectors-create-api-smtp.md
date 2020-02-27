---
title: 从 Azure 逻辑应用连接到 SMTP
description: 使用 Azure 逻辑应用自动执行任务和工作流，用以通过 SMTP（简单邮件传输协议）帐户发送电子邮件
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 60acd128495176cd0a90418c61edf53bdcd88e5a
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77647575"
---
# <a name="send-email-from-your-smtp-account-with-azure-logic-apps"></a>使用 Azure 逻辑应用从 SMTP 帐户发送电子邮件

使用 Azure 逻辑应用和简单邮件传输协议 (SMTP) 连接器，可以创建从 SMTP 帐户发送电子邮件的自动化任务和工作流。 还可以让其他操作使用来自 SMTP 操作的输出。 例如，在 SMTP 发送电子邮件后，你可以通过 Slack 连接器通知你在 Slack 中的团队。 如果你不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用？](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>必备条件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。 

* SMTP 帐户和用户凭据

  你的凭据授权逻辑应用创建连接并访问你的 SMTP 帐户。

* 有关[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知识

* 要在其中访问 SMTP 帐户的逻辑应用。 若要使用 SMTP 操作，请使用触发器启动逻辑应用，例如，如果你有 Salesforce 帐户，则可使用 Salesforce 触发器。

  例如，可以使用“创建记录时”Salesforce 触发器来启动逻辑应用。 
  每当在 Salesforce 中创建新记录（例如潜在顾客）时，此触发器都会激发。 
  在此触发器后，可以跟随执行 SMTP **发送电子邮件**操作。 这样一来，当创建了新记录时，逻辑应用都会从你的 SMTP 帐户发送一封关于新记录的电子邮件。

## <a name="connect-to-smtp"></a>连接到 SMTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. 登录到 [Azure 门户](https://portal.azure.com)，在逻辑应用设计器中打开逻辑应用（如果尚未打开）。

1. 在要添加 SMTP 操作的最后一个步骤下，选择“新建步骤”。 

   若要在步骤之间添加操作，请将鼠标指针移到步骤之间的箭头上。 
   选择出现的加号 ( **+** )，然后选择“添加操作”。

1. 在搜索框中，输入“smtp”作为筛选器。 在操作列表下，选择所需的操作。

1. 出现提示时，提供此连接信息：

   | properties | 必选 | 说明 |
   |----------|----------|-------------|
   | **连接名称** | 是 | 到 SMTP 服务器的连接的名称 | 
   | **SMTP 服务器地址** | 是 | SMTP 服务器的地址 | 
   | **用户名** | 是 | SMTP 帐户的用户名 | 
   | **密码** | 是 | SMTP 帐户的密码 | 
   | **SMTP 服务器端口** | 否 | SMTP 服务器上你要使用的特定端口 | 
   | **启用 SSL?** | 否 | 启用或禁用 SSL 加密。 | 
   |||| 

1. 为所选操作提供必要的详细信息。 

1. 保存逻辑应用，或继续构建逻辑应用的工作流。

## <a name="connector-reference"></a>连接器参考

有关此连接器的更多技术详细信息，如连接器的 Swagger 文件所述的触发器、操作和限制，请参阅[连接器的参考页](https://docs.microsoft.com/connectors/smtpconnector/)。

> [!NOTE]
> 对于[integration service 环境（ISE）](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)中的逻辑应用，此连接器的 ise 标记版本会改用[ise 消息限制](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)。

## <a name="next-steps"></a>后续步骤

* 了解其他[逻辑应用连接器](../connectors/apis-list.md)