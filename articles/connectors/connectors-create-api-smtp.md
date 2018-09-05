---
title: 从 Azure 逻辑应用连接到 SMTP | Microsoft Docs
description: 使用 Azure 逻辑应用自动执行任务和工作流，用以通过 SMTP（简单邮件传输协议）帐户发送电子邮件
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: d4141c08-88d7-4e59-a757-c06d0dc74300
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: 90af33574093cfbe529093c7091ee6988f043aa6
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2018
ms.locfileid: "43052016"
---
# <a name="send-email-from-your-smtp-account-with-azure-logic-apps"></a>使用 Azure 逻辑应用从 SMTP 帐户发送电子邮件

使用 Azure 逻辑应用和简单邮件传输协议 (SMTP) 连接器，可以创建从 SMTP 帐户发送电子邮件的自动化任务和工作流。 还可以让其他操作使用来自 SMTP 操作的输出。 例如，在 SMTP 发送电子邮件后，你可以通过 Slack 连接器通知你在 Slack 中的团队。 如果不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用？](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请<a href="https://azure.microsoft.com/free/" target="_blank">注册一个免费 Azure 帐户</a>。 

* SMTP 帐户和用户凭据

  你的凭据授权逻辑应用创建连接并访问你的 SMTP 帐户。

* 有关[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知识

* 要在其中访问 SMTP 帐户的逻辑应用。 若要使用 SMTP 操作，请使用触发器启动逻辑应用，例如，如果你有 Salesforce 帐户，则可使用 Salesforce 触发器。

  例如，可以使用 Salesforce 触发器**当创建了记录时**来启动逻辑应用。 
  每当在 Salesforce 中创建了新记录（例如潜在顾客）时，此触发器都会触发。 
  在此触发器后，可以跟随执行 SMTP **发送电子邮件**操作。 这样一来，当创建了新记录时，逻辑应用都会从你的 SMTP 帐户发送一封关于新记录的电子邮件。

## <a name="connect-to-smtp"></a>连接到 SMTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. 登录到 [Azure门户](https://portal.azure.com)，然后在逻辑应用设计器中打开你的逻辑应用（如果尚未打开）。

1. 在要添加 SMTP 操作的最后一个步骤下，选择“新建步骤”。 

   若要在步骤之间添加操作，请将鼠标指针移到步骤之间的箭头上。 
   选择出现的加号 (**+**)，然后选择“添加操作”。

1. 在搜索框中，输入“smtp”作为筛选器。 在操作列表下，选择所需的操作。

1. 出现提示时，提供此连接信息：

   | 属性 | 必需 | 说明 |
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

有关触发器、操作和限制（请参阅连接器的 OpenAPI（以前称为 Swagger）说明）的技术详细信息，请查看连接器的[参考页](/connectors/smtpconnector/)。

## <a name="get-support"></a>获取支持

* 有关问题，请访问 [Azure 逻辑应用论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。
* 若要提交功能建议或对功能建议进行投票，请访问[逻辑应用用户反馈网站](http://aka.ms/logicapps-wish)。

## <a name="next-steps"></a>后续步骤

* 了解其他[逻辑应用连接器](../connectors/apis-list.md)