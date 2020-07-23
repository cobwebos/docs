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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "75707180"
---
# <a name="manage-email-calendars-and-contacts-in-outlookcom-by-using-azure-logic-apps"></a>使用 Azure 逻辑应用管理 Outlook.com 中的电子邮件、日历和联系人

使用 [Azure 逻辑应用](../logic-apps/logic-apps-overview.md)和 [Outlook.com 连接器](/connectors/outlook/)，可以通过构建逻辑应用来创建用于管理 @outlook.com 或 @hotmail.com 帐户的自动化任务和工作流。 例如，可以自动执行以下任务：

* 获取、发送和回复电子邮件。
* 在日历上安排会议。
* 添加和编辑联系人。

可以使用任何触发器来启动工作流，例如，当新电子邮件到达时、当日历项更新时或其他服务中发生某个事件时。 可以使用对触发器事件做出响应的操作，例如，发送电子邮件或创建新的日历事件。

> [!NOTE]
> 若要针对 Microsoft 工作帐户（例如 @fabrikam.onmicrosoft.com）自动执行任务，请使用 [Office 365 Outlook 连接器](../connectors/connectors-create-api-office365-outlook.md)。

## <a name="prerequisites"></a>必备条件

* 一个 [Outlook.com 帐户](https://outlook.live.com/owa/)

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。 

* 你要在其中访问 Outlook.com 帐户的逻辑应用。 若要通过 Outlook.com 触发器启动工作流，你需要有一个[空白逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 若要向工作流中添加 Outlook.com 操作，逻辑应用需要已有一个触发器。

## <a name="add-a-trigger"></a>添加触发器

[触发器](../logic-apps/logic-apps-overview.md#logic-app-concepts)是一个事件，用于启动逻辑应用中的工作流。 此示例逻辑应用使用一个“轮询”触发器，该触发器根据指定的时间间隔和频率检查电子邮件帐户中是否有任何新电子邮件。

1. 在 [Azure 门户](https://portal.azure.com)中，在逻辑应用设计器中打开你的空白逻辑应用。

1. 在搜索框中，输入“outlook.com”作为筛选器。 对于此示例，选择“新电子邮件到达时”  。

1. 如果提示你登录，请提供你的 Outlook.com 凭据，以便逻辑应用可以连接到你的帐户。 或者，如果连接已存在，请提供触发器属性的信息：

1. 在触发器中，设置“频率”  和“间隔”  值。

   例如，如果希望触发器每 15 分钟轮询一次，请将“频率”  设置为“分钟”  ，将“间隔”  设置为“15”  。

1. 在设计器工具栏上，选择“保存”  ，这将保存你的逻辑应用。

若要响应触发器，请添加另一操作。 例如，你可以添加 Twilio **发送消息**操作，该操作将在电子邮件到达时发送一个文本。

## <a name="add-an-action"></a>添加操作

[操作](../logic-apps/logic-apps-overview.md#logic-app-concepts)是指由逻辑应用中的工作流运行的操作。 此示例逻辑应用从你的 Outlook.com 帐户发送一封电子邮件。 可以使用其他触发器的输出来填充操作。 例如，假设你的逻辑应用使用了 SalesForce“创建对象时”  触发器。 你可以添加 Outlook.com“发送电子邮件”  操作，并在电子邮件中使用该 SalesForce 触发器的输出。

1. 在 [Azure 门户](https://portal.azure.com)的逻辑应用设计器中打开逻辑应用。

1. 若要将某个操作添加为工作流的最后一步，请选择“新建步骤”  。 

   若要在步骤之间添加操作，请将鼠标指针移到这些步骤之间的箭头上。 选择出现的加号 ( **+** )，然后选择“添加操作”。 

1. 在搜索框中，输入 "outlook.com" 作为筛选器。 对于此示例，请选择“发送电子邮件”  。 

1. 如果提示你登录，请提供你的 Outlook.com 凭据，以便逻辑应用可以连接到你的帐户。 或者，如果连接已存在，请提供操作属性的信息。

1. 在设计器工具栏上，选择“保存”  ，这将保存你的逻辑应用。

## <a name="connector-reference"></a>连接器参考

如需技术详细信息（例如触发器、操作和限制，如连接器的 Swagger 文件所述），请查看[连接器的参考页](/connectors/outlook/)。 

## <a name="next-steps"></a>后续步骤

* 了解其他[逻辑应用连接器](../connectors/apis-list.md)
