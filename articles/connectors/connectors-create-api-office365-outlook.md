---
title: 连接到 Office 365 Outlook
description: 使用 Azure 逻辑应用自动执行管理 Office 365 Outlook 中的电子邮件、联系人和日历的任务和工作流
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/08/2020
tags: connectors
ms.openlocfilehash: b0f2b8b9c369fdb42c7e0e7f77fc090424ae3729
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75732653"
---
# <a name="manage-email-contacts-and-calendars-in-office-365-outlook-by-using-azure-logic-apps"></a>使用 Azure 逻辑应用管理 Office 365 Outlook 中的电子邮件、联系人和日历

使用[Azure 逻辑应用](../logic-apps/logic-apps-overview.md)和[Office 365 Outlook 连接器](/connectors/office365connector/)，可以通过构建逻辑应用创建管理 Office 365 帐户的自动化任务和工作流。 例如，您可以自动执行以下任务：

* 获取、发送和回复电子邮件。 
* 在日历上安排会议。
* 添加和编辑联系人。 

您可以使用任何触发器启动工作流，例如，当新电子邮件到达时、日历项更新时或在差异服务（如 Salesforce）中发生事件时。 您可以使用响应触发器事件的操作，例如发送电子邮件或创建新的日历事件。 

> [!NOTE]
> 要自动执行 或@outlook.com@hotmail.com帐户的任务，请使用[Outlook.com连接器](../connectors/connectors-create-api-outlook.md)。

## <a name="prerequisites"></a>先决条件

* [办公室 365 帐户](https://www.office.com/)

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。 

* 要访问 Office 365 Outlook 帐户的逻辑应用。 要使用 Office 365 Outlook 触发器启动工作流，需要有一个[空白的逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 要向工作流添加 Office 365 Outlook 操作，逻辑应用需要已具有触发器。

## <a name="add-a-trigger"></a>添加触发器

[触发器](../logic-apps/logic-apps-overview.md#logic-app-concepts)是在逻辑应用中启动工作流的事件。 此示例逻辑应用使用"轮询"触发器，该触发器根据指定的间隔和频率检查电子邮件帐户中的任何更新日历事件。

1. 在[Azure 门户](https://portal.azure.com)中，在逻辑应用设计器中打开空白逻辑应用。

1. 在搜索框中，输入 `office 365 outlook` 作为筛选器。 此示例选择**即将启动的事件。**
   
   ![选择触发器以启动逻辑应用](./media/connectors-create-api-office365-outlook/office365-trigger.png)

1. 如果系统提示您登录，请提供 Office 365 凭据，以便逻辑应用可以连接到您的帐户。 否则，如果连接已存在，请提供触发器属性的信息。

   此示例选择触发器检查的日历，例如：

   ![配置触发器的属性](./media/connectors-create-api-office365-outlook/select-calendar.png)

1. 在触发器中，设置**频率**和**间隔**值。 要添加其他可用的触发器属性（如**时区**），请从 **"添加新参数**"列表中选择这些属性。

   例如，如果希望触发器每 15 分钟检查一次日历，请将 **"频率**"设置为 **"分钟**"，`15`并将**间隔**设置为 。 

   ![设置触发器的频率和间隔](./media/connectors-create-api-office365-outlook/calendar-settings.png)

1. 在设计器工具栏上，选择“保存”****。

现在添加在触发触发器后运行的操作。 例如，您可以添加 Twilio**发送消息**操作，该操作在日历事件在 15 分钟后启动时发送文本。

## <a name="add-an-action"></a>添加操作

[操作](../logic-apps/logic-apps-overview.md#logic-app-concepts)是由逻辑应用中的工作流运行的操作。 此示例逻辑应用在 Office 365 Outlook 中创建新联系人。 您可以使用来自另一个触发器或操作的输出来创建联系人。 例如，假设您的逻辑应用使用 Dynamics 365 触发器，**创建记录时**。 您可以添加 Office 365 Outlook**创建联系人**操作，并使用 SalesForce 触发器的输出创建新联系人。

1. 在 [Azure 门户](https://portal.azure.com)的逻辑应用设计器中打开逻辑应用。

1. 要将操作添加为工作流中的最后一步，请选择 **"新建步骤**"。 

   若要在步骤之间添加操作，请将鼠标指针移到这些步骤之间的箭头上。 选择显示的加号**+**（），然后选择 **"添加操作**"。

1. 在搜索框中，输入 `office 365 outlook` 作为筛选器。 此示例选择 **"创建联系人**"。

   ![选择要在逻辑应用中运行的操作](./media/connectors-create-api-office365-outlook/office365-actions.png) 

1. 如果系统提示您登录，请提供 Office 365 凭据，以便逻辑应用可以连接到您的帐户。 否则，如果连接已存在，请提供操作属性的信息。

   本示例选择操作创建新联系人的联系人文件夹，例如：

   ![配置操作的属性](./media/connectors-create-api-office365-outlook/select-contacts-folder.png)

   要添加其他可用操作属性，请从 **"添加新参数**"列表中选择这些属性。

1. 在设计器工具栏上，选择“保存”****。

## <a name="connector-specific-details"></a>特定于连接器的详细信息

有关连接器 Swagger 文件中所述的触发器、操作和限制的技术详细信息，请参阅[连接器的参考页](/connectors/office365connector/)。 

## <a name="next-steps"></a>后续步骤

* 了解其他[逻辑应用连接器](../connectors/apis-list.md)
