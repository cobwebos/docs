---
title: 连接到 Outlook.com
description: 使用 Azure 逻辑应用自动执行管理 Outlook.com 中的电子邮件、日历和联系人的任务和工作流
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/18/2016
tags: connectors
ms.openlocfilehash: 8d3b180b6f1e9dc4ec4b09dd81786cc81e8588da
ms.sourcegitcommit: f2149861c41eba7558649807bd662669574e9ce3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/07/2020
ms.locfileid: "75707180"
---
# <a name="manage-email-calendars-and-contacts-in-outlookcom-by-using-azure-logic-apps"></a>使用 Azure 逻辑应用管理 Outlook.com 中的电子邮件、日历和联系人

通过[Azure 逻辑应用](../logic-apps/logic-apps-overview.md)和[Outlook.com 连接器](/connectors/outlook/)，你可以创建自动化任务和工作流，通过构建逻辑应用来管理 @outlook.com 或 @hotmail.com 帐户。 例如，你可以自动执行以下任务：

* 获取、发送和回复电子邮件。
* 在日历上安排会议。
* 添加和编辑联系人。

您可以使用任何触发器来启动工作流，例如，在新电子邮件到达时、在更新日历项时或在差异服务中发生事件时。 您可以使用响应触发器事件的操作，例如，发送电子邮件或创建新的日历事件。

> [!NOTE]
> 若要为 Microsoft 工作帐户（如 @fabrikam.onmicrosoft.com）自动执行任务，请使用[Office 365 Outlook connector](../connectors/connectors-create-api-office365-outlook.md)。

## <a name="prerequisites"></a>必备组件

* 一个 [Outlook.com 帐户](https://outlook.live.com/owa/)

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。 

* 你要在其中访问 Outlook.com 帐户的逻辑应用。 若要使用 Outlook.com 触发器启动工作流，需要具有[空白逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 若要将 Outlook.com 操作添加到工作流，你的逻辑应用需要具有触发器。

## <a name="add-a-trigger"></a>添加触发器

[触发器](../logic-apps/logic-apps-overview.md#logic-app-concepts)是在逻辑应用中启动工作流的事件。 此示例逻辑应用使用 "轮询" 触发器，该触发器根据指定的时间间隔和频率检查电子邮件帐户中是否有任何新电子邮件。

1. 在[Azure 门户](https://portal.azure.com)中，打开逻辑应用设计器中的空白逻辑应用。

1. 在搜索框中，输入“outlook.com”作为筛选器。 对于本示例，请选择 "**新电子邮件到达时**"。

1. 如果系统提示你登录，请提供你的 Outlook.com 凭据，以便逻辑应用可以连接到你的帐户。 否则，如果您的连接已存在，则提供触发器属性的信息：

1. 在触发器中，设置 "**频率**" 和 "**间隔**" 值。

   例如，如果希望触发器每15分钟轮询一次，请将**频率**设置为 "**分钟**"，并将 "**间隔**" 设置为**15**。

1. 在设计器工具栏上，选择 "**保存**"，保存逻辑应用。

若要响应触发器，请添加其他操作。 例如，你可以添加 Twilio**发送消息**操作，该操作将在电子邮件到达时发送文本。

## <a name="add-an-action"></a>添加操作

[操作](../logic-apps/logic-apps-overview.md#logic-app-concepts)是指逻辑应用中工作流运行的操作。 此示例逻辑应用从你的 Outlook.com 帐户发送一封电子邮件。 您可以使用另一个触发器的输出来填充操作。 例如，假设逻辑应用**在创建对象时**使用 SalesForce。 可以添加 Outlook.com "**发送电子邮件**" 操作，并在电子邮件中使用 SalesForce 触发器的输出。

1. 在 [Azure 门户](https://portal.azure.com)的逻辑应用设计器中打开逻辑应用。

1. 若要添加操作作为工作流的最后一步，请选择 "**新建步骤**"。 

   若要在步骤之间添加操作，请将指针移到这些步骤之间的箭头上。 选择出现的加号（ **+** ），然后选择 "**添加操作**"。

1. 在搜索框中，输入 "outlook.com" 作为筛选器。 对于本示例，请选择 "**发送电子邮件**"。 

1. 如果系统提示你登录，请提供你的 Outlook.com 凭据，以便逻辑应用可以连接到你的帐户。 否则，如果连接已存在，请提供操作属性的信息。

1. 在设计器工具栏上，选择 "**保存**"，保存逻辑应用。

## <a name="connector-reference"></a>连接器参考

如需技术详细信息（例如触发器、操作和限制，如连接器的 Swagger 文件所述），请查看[连接器的参考页](/connectors/outlook/)。 

## <a name="next-steps"></a>后续步骤

* 了解其他[逻辑应用连接器](../connectors/apis-list.md)
