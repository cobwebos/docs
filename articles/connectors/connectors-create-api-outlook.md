---
title: 连接到 Outlook.com
description: 使用 Outlook.com REST API 和 Azure 逻辑应用管理电子邮件、日历和联系人
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/18/2016
tags: connectors
ms.openlocfilehash: 750efc2cb928bf127c4f3c68d5a58c5f52ca7d51
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789353"
---
# <a name="manage-email-calendars-and-contacts-in-outlookcom-with-azure-logic-apps"></a>使用 Azure 逻辑应用管理 Outlook.com 中的电子邮件、日历和联系人

本文介绍如何使用 Box 连接器在逻辑应用内创建和管理 Outlook.com 帐户。 这样，你就可以创建逻辑应用以自动执行 Outlook.com 帐户的任务和工作流程，例如：

* 发送电子邮件。 
* 安排会议。
* 添加联系人。 

如果不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用](../logic-apps/logic-apps-overview.md)。

## <a name="prerequisites"></a>必备组件

* 一个 [Outlook.com 帐户](https://outlook.live.com/owa/)

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。 

* 你要在其中访问 Outlook.com 帐户的逻辑应用。 若要通过 Outlook 触发器启动逻辑应用，需要一个[空白逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 

* 有关[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知识。

## <a name="connect-to-outlookcom"></a>连接到 Outlook.com

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Connect to Outlook.com](../../includes/connectors-create-api-outlook.md)]

## <a name="connector-reference"></a>连接器参考

如需技术详细信息（例如触发器、操作和限制，如连接器的 Swagger 文件所述），请查看[连接器的参考页](/connectors/outlook/)。 

## <a name="get-support"></a>获取支持

* 有关问题，请访问 [Azure 逻辑应用论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。
* 若要提交功能建议或对功能建议进行投票，请访问[逻辑应用用户反馈网站](https://aka.ms/logicapps-wish)。

## <a name="next-steps"></a>后续步骤

* 了解其他[逻辑应用连接器](../connectors/apis-list.md)