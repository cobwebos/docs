---
title: "在逻辑应用中添加 Office 365 Outlook 连接器 | Microsoft Docs"
description: "使用 Office 365 连接器创建逻辑应用，启用与 Office 365 的交互。 例如：创建、编辑和更新联系人和日历项。"
services: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: b2f6cc2c-bba2-493a-b0ba-841785462a80
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: mandia; ladocs
ms.openlocfilehash: 51b8e3de639b5cce954547adb77ff13b79ad6747
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2018
---
# <a name="get-started-with-the-office-365-outlook-connector"></a>Office 365 Outlook 连接器入门
Office 365 Outlook 连接器在 Office 365 中启用与 Outlook 的交互。 使用此连接器创建、编辑和更新联系人和日历项，还可以获取、发送和回复电子邮件。

通过 Office 365 Outlook，可以：

* 使用 Office 365 内的电子邮件和日历功能生成工作流。 
* 在存在新电子邮件时、更新日历项时等情况下，使用触发器启动工作流。
* 使用发送电子邮件、创建新日历事件等操作。 例如，当 Salesforce 中存在新对象时（触发器），向 Office 365 Outlook 发送一封电子邮件（操作）。 

本主题演示了如何在逻辑应用中使用 Office 365 Outlook 连接器，还列出了触发器和操作。

> [!NOTE]
> 此文章版本适用于逻辑应用正式版 (GA)。
> 
> 

若要了解有关逻辑应用的详细信息，请参阅[什么是逻辑应用](../logic-apps/logic-apps-overview.md)和[创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="connect-to-office-365"></a>连接到 Office 365
在逻辑应用能够访问任何服务前，需要先创建到该服务的*连接*。 连接提供逻辑应用和其他服务之间的连接性。 例如，若要连接到 Office 365 Outlook，首先需要 Office 365 *连接*。 若要创建连接，请输入通常用于访问要连接到的服务的凭据。 因此，在 Office 365 Outlook 中，输入 Office 365 帐户的凭据以创建连接。

## <a name="create-the-connection"></a>创建连接
> [!INCLUDE [Steps to create a connection to Office 365](../../includes/connectors-create-api-office365-outlook.md)]
> 
> 

## <a name="use-a-trigger"></a>使用触发器
触发器是用于启动在逻辑应用中定义的工作流的事件。 触发器以希望的间隔和频率“轮询”服务。 [了解有关触发器的详细信息](../logic-apps/logic-apps-overview.md#logic-app-concepts)。

1. 在逻辑应用中，键入“office 365”获取触发器列表：  
   
    ![](./media/connectors-create-api-office365-outlook/office365-trigger.png)
2. 选择“Office 365 Outlook - 即将启动将要发生的事件时”。 如果连接已存在，从下拉列表中选择日历。
   
    ![](./media/connectors-create-api-office365-outlook/sample-calendar.png)
   
    如果提示登录，则输入登录详细信息以创建连接。 本主题中的[创建连接](connectors-create-api-office365-outlook.md#create-the-connection)列出了相关步骤。 
   
   > [!NOTE]
   > 在此示例中，逻辑应用在更新日历事件时运行。 要查看此触发器的结果，请添加另一个向你发送短信的操作。 例如，添加 Twilio“发送消息”操作，如果日历事件将在 15 分钟后启动，该操作将向你发送短信。 
   > 
   > 
3. 选择“编辑”按钮并设置“频率”和“间隔”值。 例如，如果希望触发器每 15 分钟轮询一次，将“频率”设置为“分钟”，将“间隔”设置为“15”。 
   
    ![](./media/connectors-create-api-office365-outlook/calendar-settings.png)
4. **保存**更改（工具栏的左上角）。 逻辑应用将保存，并且可能自动启用。

## <a name="use-an-action"></a>使用操作
操作是指在逻辑应用中定义的工作流所执行的操作。 [了解有关操作的详细信息](../logic-apps/logic-apps-overview.md#logic-app-concepts)。

1. 选择加号。 可看到多个选项：“添加操作”、“添加条件”或“更多”选项之一。
   
    ![](./media/connectors-create-api-office365-outlook/add-action.png)
2. 选择“添加操作”。
3. 在文本框中，键入“office 365”获取所有可用操作的列表。
   
    ![](./media/connectors-create-api-office365-outlook/office365-actions.png) 
4. 在我们的示例中，选择“Office 365 Outlook - 创建联系人”。 如果连接已存在，依次选择“文件夹 ID”、“名称”和其他属性：  
   
    ![](./media/connectors-create-api-office365-outlook/office365-sampleaction.png)
   
    如果提示提供连接信息，则输入详细信息以创建连接。 本主题中的[创建连接](connectors-create-api-office365-outlook.md#create-the-connection)介绍了这些属性。 
   
   > [!NOTE]
   > 在此示例中，我们在 Office 365 Outlook 中新建联系人。 可使用来自其他触发器的输出创建联系人。 例如，添加 SalesForce“创建对象时”触发器。 然后添加 Office 365 Outlook“创建联系人”操作，可使用 SalesForce 字段在 Office 365 中新建联系人。 
   > 
   > 
5. **保存**更改（工具栏的左上角）。 逻辑应用将保存，并且可能自动启用。

## <a name="connector-specific-details"></a>特定于连接器的详细信息

在[连接器详细信息](/connectors/office365connector/)中查看在 Swagger 中定义的触发器和操作，并查看限制。 

## <a name="next-steps"></a>后续步骤
[创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 在我们的 [API 列表](apis-list.md)中了解逻辑应用中的其他可用连接器。

