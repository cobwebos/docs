---
title: 从 Azure 逻辑应用连接到 SendGrid | Microsoft Docs
description: 使用 Azure 逻辑应用自动执行任务和工作流，用以在 SendGrid 中发送电子邮件以及管理邮件列表
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: bc4f1fc2-824c-4ed7-8de8-e82baff3b746
ms.topic: article
tags: connectors
ms.date: 08/24/2018
ms.openlocfilehash: 7eecd3908883b195b52755d03e70872afe9180bb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "62105728"
---
# <a name="send-emails-and-manage-mailing-lists-in-sendgrid-by-using-azure-logic-apps"></a>使用 Azure 逻辑应用在 SendGrid 中发送电子邮件以及管理邮件列表

使用 Azure 逻辑应用和 SendGrid 连接器，可以创建用于发送电子邮件以及管理收件人列表的自动化任务和工作流，例如：

* 发送电子邮件。
* 将收件人添加到列表。
* 获取、添加和管理全局禁止显示。

可以在逻辑应用中使用 SendGrid 操作来执行这些任务。 还可以让其他操作使用来自 SendGrid 操作的输出。 

此连接器仅提供操作，因此，若要启动逻辑应用，请使用单独的触发器，例如**定期**触发器。 例如，如果你定期将收件人添加到你的列表中，则可以使用 Office 365 Outlook 连接器或 Outlook.com 连接器发送关于收件人和列表的电子邮件。
如果你不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用？](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>必备组件

* Azure 订阅。 如果没有 Azure 订阅，请<a href="https://azure.microsoft.com/free/" target="_blank">注册一个免费 Azure 帐户</a>。 

* 一个 [SendGrid 帐户](https://www.sendgrid.com/)和一个 [SendGrid API 密钥](https://sendgrid.com/docs/ui/account-and-settings/api-keys/)

   你的 API 密钥授权逻辑应用创建连接并访问你的 SendGrid 帐户。

* 有关[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知识

* 要在其中访问 SendGrid 帐户的逻辑应用。 若要使用 SendGrid 操作，请使用其他触发器（例如**定期**触发器）启动逻辑应用。

## <a name="connect-to-sendgrid"></a>连接到 SendGrid

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. 登录到 [Azure门户](https://portal.azure.com)，然后在逻辑应用设计器中打开你的逻辑应用（如果尚未打开）。

1. 选择一个路径： 

   * 在要添加操作的最后一个步骤下，选择“新建步骤”。  

     -或-

   * 在要在其中添加操作的两个步骤之间，将鼠标指针移到步骤之间的箭头上。 
   选择出现的加号 ( **+** )，然后选择“添加操作”。 

1. 在搜索框中，输入“sendgrid”作为筛选器。 在操作列表下，选择所需的操作。

1. 提供连接的名称。 

1. 输入你的 SendGrid API 密钥，然后选择“创建”  。

1. 为所选操作提供必要的详细信息，并继续构建逻辑应用的工作流。

## <a name="connector-reference"></a>连接器参考

有关触发器、操作和限制（请参阅连接器的 OpenAPI（以前称为 Swagger）说明）的技术详细信息，请查看连接器的[参考页](/connectors/sendgrid/)。

## <a name="get-support"></a>获取支持

* 有关问题，请访问 [Azure 逻辑应用论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。
* 若要提交功能建议或对功能建议进行投票，请访问[逻辑应用用户反馈网站](https://aka.ms/logicapps-wish)。

## <a name="next-steps"></a>后续步骤

* 了解其他[逻辑应用连接器](../connectors/apis-list.md)