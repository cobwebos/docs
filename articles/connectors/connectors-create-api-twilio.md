---
title: 从 Azure 逻辑应用连接到 Twilio | Microsoft Docs
description: 使用 Azure 逻辑应用自动执行任务和工作流，用以通过 Twili 帐户管理全球短信、彩信和 IP 消息
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: 43116187-4a2f-42e5-9852-a0d62f08c5fc
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: db7677042737ea1377af54cc02ee1c82c05435c8
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2018
ms.locfileid: "43047568"
---
# <a name="manage-messages-in-twilio-with-azure-logic-apps"></a>使用 Azure 逻辑应用管理 Twilio 中的消息

使用 Azure 逻辑应用和 Twilio 连接器，可以创建用于获取、发送和列出 Twilio 中的消息（这包括全球短信、彩信和 IP 消息）的自动化任务和工作流。 可以使用这些操作通过 Twilio 帐户执行任务。 还可以让其他操作使用来自 Twilio 操作的输出。 例如，当有新消息到达时，你可以使用 Slack 连接器发送消息内容。 如果不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用？](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请<a href="https://azure.microsoft.com/free/" target="_blank">注册一个免费 Azure 帐户</a>。 

* 从 [Twilio](https://www.twilio.com/) 中： 

  * 你的 Twilio 帐户 ID 和[身份验证令牌](https://support.twilio.com/hc/en-us/articles/223136027-Auth-Tokens-and-How-to-Change-Them)，这可以在 Twilio 仪表板上找到

    你的凭据授权逻辑应用创建连接并访问你的 Twilio 帐户。 
    如果使用的是 Twilio 试用帐户，则只能向“已验证”的手机号码发送短信。

  * 已验证可以发送短信的 Twilio 手机号码

  * 已验证可以接收短信的 Twilio 手机号码

* 有关[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知识

* 要在其中访问 Twilio 帐户的逻辑应用。 若要使用 Twilio 操作，请使用其他触发器（例如**定期**触发器）启动逻辑应用。

## <a name="connect-to-twilio"></a>连接到 Twilio

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. 登录到 [Azure门户](https://portal.azure.com)，然后在逻辑应用设计器中打开你的逻辑应用（如果尚未打开）。

1. 选择一个路径： 

     * 在要添加操作的最后一个步骤下，选择“新建步骤”。 

       -或-

     * 在要在其中添加操作的两个步骤之间，将鼠标指针移到步骤之间的箭头上。 
     选择出现的加号 (**+**)，然后选择“添加操作”。
     
       在搜索框中，输入“twilio”作为筛选器。 
       在操作列表下，选择所需的操作。

1. 为你的连接提供必要的详细信息，然后选择“创建”：

   * 要为你的连接使用的名称
   * 你的 Twilio 帐户 ID 
   * 你的 Twilio 访问（身份验证）令牌

1. 为所选操作提供必要的详细信息，并继续构建逻辑应用的工作流。

## <a name="connector-reference"></a>连接器参考

有关触发器、操作和限制（请参阅连接器的 OpenAPI（以前称为 Swagger）说明）的技术详细信息，请查看连接器的[参考页](/connectors/twilio/)。

## <a name="get-support"></a>获取支持

* 有关问题，请访问 [Azure 逻辑应用论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。
* 若要提交功能建议或对功能建议进行投票，请访问[逻辑应用用户反馈网站](http://aka.ms/logicapps-wish)。

## <a name="next-steps"></a>后续步骤

* 了解其他[逻辑应用连接器](../connectors/apis-list.md)