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
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 3d2ce46a66079ef2737b7ca6fbe46ff988e2bc84


---
# <a name="get-started-with-the-office-365-outlook-connector"></a>Office 365 Outlook 连接器入门
Office 365 Outlook 连接器在 Office 365 中启用与 Outlook 的交互。 使用此连接器创建、编辑和更新联系人和日历项，还可以获取、发送和回复电子邮件。

通过 Office 365 Outlook，你可以：

* 使用 Office 365 内的电子邮件和日历功能生成你的工作流。 
* 在存在新电子邮件时、更新日历项时等情况下，使用触发器启动工作流。
* 使用发送电子邮件、创建新日历事件等操作。 例如，当 Salesforce 中存在新对象时（触发器），向 Office 365 Outlook 发送一封电子邮件（操作）。 

本主题演示了如何在逻辑应用中使用 Office 365 Outlook 连接器，还列出了触发器和操作。

> [!NOTE]
> 此文章版本适用于逻辑应用通用版本 (GA)。
> 
> 

若要了解有关逻辑应用的详细信息，请参阅[什么是逻辑应用](../app-service-logic/app-service-logic-what-are-logic-apps.md)和[创建逻辑应用](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## <a name="connect-to-office-365"></a>连接到 Office 365
需要先创建到任何服务的*连接*，然后逻辑应用才能访问该服务。 连接提供逻辑应用和其他服务之间的连接性。 例如，若要连接到 Office 365 Outlook，首先需要 Office 365 *连接*。 若要创建连接，请输入通常用于访问要连接到的服务的凭据。 因此，在 Office 365 Outlook 中，将凭据输入到 Office 365 帐户以创建连接。

## <a name="create-the-connection"></a>创建连接
> [!INCLUDE [Steps to create a connection to Office 365](../../includes/connectors-create-api-office365-outlook.md)]
> 
> 

## <a name="use-a-trigger"></a>使用触发器
触发器是用于启动在逻辑应用中定义的工作流的事件。 触发器以你希望的间隔和频率“轮询”服务。 [了解有关触发器的详细信息](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts)。

1. 在逻辑应用中，键入“office 365”获取触发器列表：  
   
    ![](./media/connectors-create-api-office365-outlook/office365-trigger.png)
2. 选择“Office 365 Outlook - 即将启动将要发生的事件时”。 如果连接已存在，从下拉列表中选择日历。
   
    ![](./media/connectors-create-api-office365-outlook/sample-calendar.png)
   
    如果提示你登录，则输入登录详细信息以创建连接。 本主题中的[创建连接](connectors-create-api-office365-outlook.md#create-the-connection)列出了相关步骤。 
   
   > [!NOTE]
   > 在此示例中，逻辑应用在更新日历事件时运行。 若要查看此触发器的结果，请添加另一个向你发送短信的操作。 例如，在 15 分钟内启动日历事件时，添加可向你发送短信的 Twilio“发送消息”操作。 
   > 
   > 
3. 选择“编辑”按钮并设置“频率”和“间隔”值。 例如，如果你希望触发器每 15 分钟轮询一次，将“频率”设置为“分钟”，将“间隔”设置为“15”。 
   
    ![](./media/connectors-create-api-office365-outlook/calendar-settings.png)
4. **保存**更改（工具栏的左上角）。 你的逻辑应用将保存，并且可能自动启用。

## <a name="use-an-action"></a>使用操作
操作是指在逻辑应用中定义的由工作流执行的操作。 [了解有关操作的详细信息](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts)。

1. 选择加号。 可看到多个选项：“添加操作”、“添加条件”或“更多”选项之一。
   
    ![](./media/connectors-create-api-office365-outlook/add-action.png)
2. 选择“添加操作”。
3. 在文本框中，键入“office 365”获取所有可用操作的列表。
   
    ![](./media/connectors-create-api-office365-outlook/office365-actions.png) 
4. 在我们的示例中，选择“Office 365 Outlook - 创建联系人”。 如果连接已存在，依次选择“文件夹 ID”、“给定名称”和其他属性：  
   
    ![](./media/connectors-create-api-office365-outlook/office365-sampleaction.png)
   
    如果提示你提供连接信息，则输入详细信息以创建连接。 本主题中的[创建连接](connectors-create-api-office365-outlook.md#create-the-connection)介绍了这些属性。 
   
   > [!NOTE]
   > 在此示例中，我们在 Office 365 Outlook 中新建联系人。 可使用来自其他触发器的输出创建联系人。 例如，添加 SalesForce“创建对象时”触发器。 然后添加 Office 365 Outlook“创建联系人”操作，可使用 SalesForce 字段在 Office 365 中新建联系人。 
   > 
   > 
5. **保存**更改（工具栏的左上角）。 你的逻辑应用将保存，并且可能自动启用。

## <a name="technical-details"></a>技术详细信息
下面详细介绍了此连接支持的触发器、操作和响应：

## <a name="office-365-triggers"></a>Office 365 触发器
| 触发器 | 说明 |
| --- | --- |
| [即将启动将要发生的事件时](connectors-create-api-office365-outlook.md#when-an-upcoming-event-is-starting-soon) |此操作可在启动将要发生的日历事件时触发流。 |
| [新电子邮件到达时](connectors-create-api-office365-outlook.md#when-a-new-email-arrives) |此操作可在新电子邮件到达时触发流 |
| [新建事件时](connectors-create-api-office365-outlook.md#when-a-new-event-is-created) |此操作可在日历中新建事件时触发流。 |
| [修改事件时](connectors-create-api-office365-outlook.md#when-an-event-is-modified) |此操作可在日历中修改事件时触发流。 |

## <a name="office-365-actions"></a>Office 365 操作
| 操作 | 说明 |
| --- | --- |
| [获取电子邮件](connectors-create-api-office365-outlook.md#get-emails) |此操作可从文件夹中获取电子邮件。 |
| [发送电子邮件](connectors-create-api-office365-outlook.md#send-an-email) |此操作可发送电子邮件。 |
| [删除电子邮件](connectors-create-api-office365-outlook.md#delete-email) |此操作可按 ID 删除电子邮件。 |
| [标记为已读](connectors-create-api-office365-outlook.md#mark-as-read) |此操作可将电子邮件标记为已读。 |
| [回复电子邮件](connectors-create-api-office365-outlook.md#reply-to-email) |此操作可回复电子邮件。 |
| [获取附件](connectors-create-api-office365-outlook.md#get-attachment) |此操作可按 ID 获取电子邮件附件。 |
| [发送带有选项的电子邮件](connectors-create-api-office365-outlook.md#send-email-with-options) |此操作可发送带有多个选项的电子邮件，并等待收件人使用选项之一进行回复。 |
| [发送批准电子邮件](connectors-create-api-office365-outlook.md#send-approval-email) |此操作可发送批准电子邮件并等待收件人回复。 |
| [获取日历](connectors-create-api-office365-outlook.md#get-calendars) |此操作可列出可用日历。 |
| [获取事件](connectors-create-api-office365-outlook.md#get-events) |此操作可从日历中获取事件。 |
| [创建事件](connectors-create-api-office365-outlook.md#create-event) |此操作可在日历中新建事件。 |
| [获取事件](connectors-create-api-office365-outlook.md#get-event) |此操作可从日历中获取特定事件。 |
| [删除事件](connectors-create-api-office365-outlook.md#delete-event) |此操作可在日历中删除事件。 |
| [更新事件](connectors-create-api-office365-outlook.md#update-event) |此操作可在日历中更新事件。 |
| [获取联系人文件夹](connectors-create-api-office365-outlook.md#get-contact-folders) |此操作可列出可用的联系人文件夹。 |
| [获取联系人](connectors-create-api-office365-outlook.md#get-contacts) |此操作可从联系人文件夹中获取联系人。 |
| [创建联系人](connectors-create-api-office365-outlook.md#create-contact) |此操作可在联系人文件夹中新建联系人。 |
| [获取联系人](connectors-create-api-office365-outlook.md#get-contact) |此操作可从联系人文件夹中获取特定联系人。 |
| [删除联系人](connectors-create-api-office365-outlook.md#delete-contact) |此操作可从联系人文件夹中删除联系人。 |
| [更新联系人](connectors-create-api-office365-outlook.md#update-contact) |此操作可在联系人文件夹中更新联系人。 |

### <a name="trigger-and-action-details"></a>触发器和操作详细信息
在此部分中，查看有关每个触发器和每项操作的具体详细信息，包括任何必需或可选的输入属性以及与连接器相关联的任何相应输出。

#### <a name="when-an-upcoming-event-is-starting-soon"></a>即将启动将要发生的事件时
此操作可在启动将要发生的日历事件时触发流。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| table* |日历 ID |日历的唯一标识符 |
| lookAheadTimeInMinutes |预计时间 |将要发生的事件的预计时间（以分钟为单位） |

星号 (*) 表示该属性是必需的。

##### <a name="output-details"></a>输出详细信息
CalendarItemsList：日历项的列表

| 属性名称 | 数据类型 | 说明 |
| --- | --- | --- |
| value |数组 |日历项的列表 |

#### <a name="get-emails"></a>获取电子邮件
此操作可从文件夹中获取电子邮件。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| folderPath |文件夹路径 |要检索电子邮件的文件夹的路径（默认值：“Inbox”） |
| top |上限 |要检索的电子邮件数（默认值：10） |
| fetchOnlyUnread |仅提取未读邮件 |仅检索未读电子邮件吗？ |
| includeAttachments |包括附件 |如果设置为 true，附件也将与电子邮件一起检索 |
| searchQuery |搜索查询 |搜索查询，筛选电子邮件 |
| skip |Skip |要跳过的电子邮件数（默认值：0） |
| skipToken |跳过令牌 |跳过令牌，提取新页面 |

星号 (*) 表示该属性是必需的。

##### <a name="output-details"></a>输出详细信息
ReceiveMessage：接收电子邮件

| 属性名称 | 数据类型 | 说明 |
| --- | --- | --- |
| 从 |字符串 |从 |
| 如果 |字符串 |如果 |
| 使用者 |字符串 |使用者 |
| 正文 |字符串 |正文 |
| 重要性 |字符串 |重要性 |
| HasAttachment |布尔值 |带有附件 |
| ID |字符串 |邮件 ID |
| IsRead |布尔值 |已读 |
| DateTimeReceived |字符串 |接收的日期时间 |
| Attachments |数组 |附件 |
| Cc |字符串 |指定由分号分隔的电子邮件地址，如 someone@contoso.com |
| Bcc |字符串 |指定由分号分隔的电子邮件地址，如 someone@contoso.com |
| IsHtml |布尔值 |属于 HTML |

#### <a name="send-an-email"></a>发送电子邮件
此操作可发送电子邮件。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| emailMessage* |电子邮件 |电子邮件 |

星号 (*) 表示该属性是必需的。

##### <a name="output-details"></a>输出详细信息
无。

#### <a name="delete-email"></a>删除电子邮件
此操作可按 ID 删除电子邮件。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| messageId* |邮件 ID |要删除的电子邮件的 ID |

星号 (*) 表示该属性是必需的。

##### <a name="output-details"></a>输出详细信息
无。

#### <a name="mark-as-read"></a>标记为已读
此操作可将电子邮件标记为已读。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| messageId* |邮件 ID |要标记为已读的电子邮件的 ID |

星号 (*) 表示该属性是必需的。

##### <a name="output-details"></a>输出详细信息
无。

#### <a name="reply-to-email"></a>回复电子邮件
此操作可回复电子邮件。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| messageId* |邮件 ID |要回复的电子邮件的 ID |
| comment* |注释 |回复注释 |
| replyAll |全部回复 |回复所有收件人 |

星号 (*) 表示该属性是必需的。

##### <a name="output-details"></a>输出详细信息
无。

#### <a name="get-attachment"></a>获取附件
此操作可按 ID 获取电子邮件附件。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| messageId* |邮件 ID |电子邮件的 ID |
| attachmentId* |附件 ID |要下载的附件的 ID |

星号 (*) 表示该属性是必需的。

##### <a name="output-details"></a>输出详细信息
无。

#### <a name="when-a-new-email-arrives"></a>新电子邮件到达时
此操作可在新电子邮件到达时触发流。

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| folderPath |文件夹路径 |要检索的电子邮件文件夹（默认值：Inbox） |
| to |如果 |收件人电子邮件地址 |
| from |从 |发件人地址 |
| importance |重要性 |电子邮件的重要性（高、正常、低）（默认值：正常） |
| fetchOnlyWithAttachment |带有附件 |仅检索带有附件的电子邮件 |
| includeAttachments |包括附件 |包括附件 |
| subjectFilter |主题筛选器 |要在主题中查找的字符串 |

星号 (*) 表示该属性是必需的。

##### <a name="output-details"></a>输出详细信息
TriggerBatchResponse[ReceiveMessage]

| 属性名称 | 数据类型 |
| --- | --- |
| value |数组 |

#### <a name="send-email-with-options"></a>发送带有选项的电子邮件
此操作可发送带有多个选项的电子邮件，并等待收件人使用选项之一进行回复。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| optionsEmailSubscription* |选项电子邮件的订阅请求 |选项电子邮件的订阅请求 |

星号 (*) 表示该属性是必需的。

##### <a name="output-details"></a>输出详细信息
SubscriptionResponse：批准电子邮件订阅的模型

| 属性名称 | 数据类型 | 说明 |
| --- | --- | --- |
| id |字符串 |订阅的 ID |
| resource |字符串 |订阅请求的资源 |
| notificationType |字符串 |通知类型 |
| notificationUrl |字符串 |通知 URL |

#### <a name="send-approval-email"></a>发送批准电子邮件
此操作可发送批准电子邮件并等待收件人回复。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| approvalEmailSubscription* |批准电子邮件的订阅请求 |批准电子邮件的订阅请求 |

星号 (*) 表示该属性是必需的。

##### <a name="output-details"></a>输出详细信息
SubscriptionResponse：批准电子邮件订阅的模型

| 属性名称 | 数据类型 | 说明 |
| --- | --- | --- |
| id |字符串 |订阅的 ID |
| resource |字符串 |订阅请求的资源 |
| notificationType |字符串 |通知类型 |
| notificationUrl |字符串 |通知 URL |

#### <a name="get-calendars"></a>获取日历
此操作可列出可用日历。 

此调用没有任何参数。

##### <a name="output-details"></a>输出详细信息
TablesList

| 属性名称 | 数据类型 |
| --- | --- |
| value |数组 |

#### <a name="get-events"></a>获取事件
此操作可从日历中获取事件。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| table* |日历 ID |选择日历 |
| $filter |筛选查询 |要限制已返回项的 ODATA 筛选查询 |
| $orderby |排序依据 |用于指定项顺序的 ODATA orderBy 查询 |
| $skip |跳过计数 |要跳过的项数（默认值 = 0） |
| $top |最大获取计数 |要检索的最大项数（默认值 = 256） |

星号 (*) 表示该属性是必需的。

##### <a name="output-details"></a>输出详细信息
CalendarEventList：日历项的列表

| 属性名称 | 数据类型 | 说明 |
| --- | --- | --- |
| value |数组 |日历项的列表 |

#### <a name="create-event"></a>创建事件
此操作可在日历中新建事件。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| table* |日历 ID |选择日历 |
| item* |项目 |要创建的事件 |

星号 (*) 表示该属性是必需的。

##### <a name="output-details"></a>输出详细信息
CalendarEvent：连接器特定的日历事件模型类。

| 属性名称 | 数据类型 | 说明 |
| --- | --- | --- |
| ID |字符串 |事件的唯一标识符。 |
| Attendees |数组 |事件的与会者列表。 |
| 正文 |未定义 |与事件相关联的邮件正文。 |
| BodyPreview |字符串 |与事件相关联的邮件预览。 |
| Categories |数组 |与事件相关联的类别。 |
| ChangeKey |字符串 |标识事件对象的版本。 每次更改事件时，ChangeKey 也会更改。 |
| DateTimeCreated |字符串 |创建事件的日期和时间。 |
| DateTimeLastModified |字符串 |上次修改事件的日期和时间。 |
| 结束 |字符串 |事件的结束时间。 |
| EndTimeZone |字符串 |指定会议结束时间的时区。 此值必须如 Windows 中所定义（示例：“太平洋标准时间”）。 |
| HasAttachments |布尔值 |如果事件带有附件，设置为 true。 |
| 重要性 |字符串 |事件的重要性：低、正常或高。 |
| IsAllDay |布尔值 |如果事件持续一整天，设置为 true。 |
| IsCancelled |布尔值 |如果事件已取消，设置为 true。 |
| IsOrganizer |布尔值 |如果邮件发件人也是组织者，设置为 true。 |
| 位置 |未定义 |事件的位置。 |
| Organizer |未定义 |事件的组织者。 |
| 定期 |未定义 |事件的定期模式。 |
| Reminder |integer |事件启动之前的提醒时间。 |
| ResponseRequested |布尔值 |如果发件人要在事件接受或拒绝时收到回复，设置为 true。 |
| ResponseStatus |未定义 |指示事件邮件回复中发送的回复类型。 |
| SeriesMasterId |字符串 |Series Master 事件类型的唯一标识符。 |
| ShowAs |字符串 |显示为空闲或繁忙。 |
| 开始 |字符串 |事件的开始时间。 |
| StartTimeZone |字符串 |指定会议开始时间的时区。 此值必须如 Windows 中所定义（示例：“太平洋标准时间”）。 |
| 使用者 |字符串 |事件主题。 |
| 类型 |字符串 |事件类型：Single Instance、Occurrence、Exception 或 Series Master。 |
| WebLink |字符串 |与事件相关联的邮件预览。 |

#### <a name="get-event"></a>获取事件
此操作可从日历中获取特定事件。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| table* |日历 ID |选择日历 |
| ID* |项目 ID |选择事件 |

星号 (*) 表示该属性是必需的。

##### <a name="output-details"></a>输出详细信息
CalendarEvent：连接器特定的日历事件模型类。

| 属性名称 | 数据类型 | 说明 |
| --- | --- | --- |
| ID |字符串 |事件的唯一标识符。 |
| Attendees |数组 |事件的与会者列表。 |
| 正文 |未定义 |与事件相关联的邮件正文。 |
| BodyPreview |字符串 |与事件相关联的邮件预览。 |
| Categories |数组 |与事件相关联的类别。 |
| ChangeKey |字符串 |标识事件对象的版本。 每次更改事件时，ChangeKey 也会更改。 |
| DateTimeCreated |字符串 |创建事件的日期和时间。 |
| DateTimeLastModified |字符串 |上次修改事件的日期和时间。 |
| 结束 |字符串 |事件的结束时间。 |
| EndTimeZone |字符串 |指定会议结束时间的时区。 此值必须如 Windows 中所定义（示例：“太平洋标准时间”）。 |
| HasAttachments |布尔值 |如果事件带有附件，设置为 true。 |
| 重要性 |字符串 |事件的重要性：低、正常或高。 |
| IsAllDay |布尔值 |如果事件持续一整天，设置为 true。 |
| IsCancelled |布尔值 |如果事件已取消，设置为 true。 |
| IsOrganizer |布尔值 |如果邮件发件人也是组织者，设置为 true。 |
| 位置 |未定义 |事件的位置。 |
| Organizer |未定义 |事件的组织者。 |
| 定期 |未定义 |事件的定期模式。 |
| Reminder |integer |事件启动之前的提醒时间。 |
| ResponseRequested |布尔值 |如果发件人要在事件接受或拒绝时收到回复，设置为 true。 |
| ResponseStatus |未定义 |指示事件邮件回复中发送的回复类型。 |
| SeriesMasterId |字符串 |Series Master 事件类型的唯一标识符。 |
| ShowAs |字符串 |显示为空闲或繁忙。 |
| 开始 |字符串 |事件的开始时间。 |
| StartTimeZone |字符串 |指定会议开始时间的时区。 此值必须如 Windows 中所定义（示例：“太平洋标准时间”）。 |
| 使用者 |字符串 |事件主题。 |
| 类型 |字符串 |事件类型：Single Instance、Occurrence、Exception 或 Series Master。 |
| WebLink |字符串 |与事件相关联的邮件预览。 |

#### <a name="delete-event"></a>删除事件
此操作可在日历中删除事件。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| table* |日历 ID |选择日历 |
| ID* |ID |选择事件 |

星号 (*) 表示该属性是必需的。

##### <a name="output-details"></a>输出详细信息
无。

#### <a name="update-event"></a>更新事件
此操作可在日历中更新事件。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| table* |日历 ID |选择日历 |
| ID* |ID |选择事件 |
| item* |项目 |要更新的事件 |

星号 (*) 表示该属性是必需的。

##### <a name="output-details"></a>输出详细信息
CalendarEvent：连接器特定的日历事件模型类。

| 属性名称 | 数据类型 | 说明 |
| --- | --- | --- |
| ID |字符串 |事件的唯一标识符。 |
| Attendees |数组 |事件的与会者列表。 |
| 正文 |未定义 |与事件相关联的邮件正文。 |
| BodyPreview |字符串 |与事件相关联的邮件预览。 |
| Categories |数组 |与事件相关联的类别。 |
| ChangeKey |字符串 |标识事件对象的版本。 每次更改事件时，ChangeKey 也会更改。 |
| DateTimeCreated |字符串 |创建事件的日期和时间。 |
| DateTimeLastModified |字符串 |上次修改事件的日期和时间。 |
| 结束 |字符串 |事件的结束时间。 |
| EndTimeZone |字符串 |指定会议结束时间的时区。 此值必须如 Windows 中所定义（示例：“太平洋标准时间”）。 |
| HasAttachments |布尔值 |如果事件带有附件，设置为 true。 |
| 重要性 |字符串 |事件的重要性：低、正常或高。 |
| IsAllDay |布尔值 |如果事件持续一整天，设置为 true。 |
| IsCancelled |布尔值 |如果事件已取消，设置为 true。 |
| IsOrganizer |布尔值 |如果邮件发件人也是组织者，设置为 true。 |
| 位置 |未定义 |事件的位置。 |
| Organizer |未定义 |事件的组织者。 |
| 定期 |未定义 |事件的定期模式。 |
| Reminder |integer |事件启动之前的提醒时间。 |
| ResponseRequested |布尔值 |如果发件人要在事件接受或拒绝时收到回复，设置为 true。 |
| ResponseStatus |未定义 |指示事件邮件回复中发送的回复类型。 |
| SeriesMasterId |字符串 |Series Master 事件类型的唯一标识符。 |
| ShowAs |字符串 |显示为空闲或繁忙。 |
| 开始 |字符串 |事件的开始时间。 |
| StartTimeZone |字符串 |指定会议开始时间的时区。 此值必须如 Windows 中所定义（示例：“太平洋标准时间”）。 |
| 使用者 |字符串 |事件主题。 |
| 类型 |字符串 |事件类型：Single Instance、Occurrence、Exception 或 Series Master。 |
| WebLink |字符串 |与事件相关联的邮件预览。 |

#### <a name="when-a-new-event-is-created"></a>新建事件时
此操作可在日历中新建事件时触发流。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| table* |日历 ID |选择日历 |
| $filter |筛选查询 |要限制已返回项的 ODATA 筛选查询 |
| $orderby |排序依据 |用于指定项顺序的 ODATA orderBy 查询 |
| $skip |跳过计数 |要跳过的项数（默认值 = 0） |
| $top |最大获取计数 |要检索的最大项数（默认值 = 256） |

星号 (*) 表示该属性是必需的。

##### <a name="output-details"></a>输出详细信息
CalendarItemsList：日历项的列表

| 属性名称 | 数据类型 | 说明 |
| --- | --- | --- |
| value |数组 |日历项的列表 |

#### <a name="when-an-event-is-modified"></a>修改事件时
此操作可在日历中修改事件时触发流。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| table* |日历 ID |选择日历 |
| $filter |筛选查询 |要限制已返回项的 ODATA 筛选查询 |
| $orderby |排序依据 |用于指定项顺序的 ODATA orderBy 查询 |
| $skip |跳过计数 |要跳过的项数（默认值 = 0） |
| $top |最大获取计数 |要检索的最大项数（默认值 = 256） |

星号 (*) 表示该属性是必需的。

##### <a name="output-details"></a>输出详细信息
CalendarItemsList：日历项的列表

| 属性名称 | 数据类型 | 说明 |
| --- | --- | --- |
| value |数组 |日历项的列表 |

#### <a name="get-contact-folders"></a>获取联系人文件夹
此操作可列出可用的联系人文件夹。 

此调用没有任何参数。

##### <a name="output-details"></a>输出详细信息
TablesList

| 属性名称 | 数据类型 |
| --- | --- |
| value |数组 |

#### <a name="get-contacts"></a>获取联系人
此操作可从联系人文件夹中获取联系人。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| table* |文件夹 ID |要检索的联系人文件夹的唯一标识符 |
| $filter |筛选查询 |要限制已返回项的 ODATA 筛选查询 |
| $orderby |排序依据 |用于指定项顺序的 ODATA orderBy 查询 |
| $skip |跳过计数 |要跳过的项数（默认值 = 0） |
| $top |最大获取计数 |要检索的最大项数（默认值 = 256） |

星号 (*) 表示该属性是必需的。

##### <a name="output-details"></a>输出详细信息
ContactList：联系人列表

| 属性名称 | 数据类型 | 说明 |
| --- | --- | --- |
| value |数组 |联系人列表 |

#### <a name="create-contact"></a>创建联系人
此操作可在联系人文件夹中新建联系人。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| table* |文件夹 ID |选择联系人文件夹 |
| item* |项目 |要创建的联系人 |

星号 (*) 表示该属性是必需的。

##### <a name="output-details"></a>输出详细信息
Contact：联系人

| 属性名称 | 数据类型 | 说明 |
| --- | --- | --- |
| ID |字符串 |联系人的唯一标识符。 |
| ParentFolderId |字符串 |联系人的父文件夹的 ID |
| Birthday |字符串 |联系人的生日。 |
| FileAs |字符串 |要存档的联系人的姓名。 |
| DisplayName |字符串 |联系人的显示名称。 |
| GivenName |字符串 |联系人的名字。 |
| Initials |字符串 |联系人的姓名缩写。 |
| MiddleName |字符串 |联系人的中间名。 |
| NickName |字符串 |联系人的别名。 |
| Surname |字符串 |联系人的姓氏。 |
| 标题 |字符串 |联系人的头衔。 |
| Generation |字符串 |联系人的世代。 |
| EmailAddresses |数组 |联系人的电子邮件地址。 |
| ImAddresses |数组 |联系人的即时消息 (IM) 地址。 |
| JobTitle |字符串 |联系人的职务。 |
| CompanyName |字符串 |联系人所在的公司名称。 |
| 系 |字符串 |联系人所在的部门。 |
| OfficeLocation |字符串 |联系人的办公室地址。 |
| Profession |字符串 |联系人的职业。 |
| BusinessHomePage |字符串 |联系人的业务主页。 |
| AssistantName |字符串 |联系人的助理姓名。 |
| Manager |字符串 |联系人的经理姓名。 |
| HomePhones |数组 |联系人的住宅电话号码。 |
| BusinessPhones |数组 |联系人的公司电话号码 |
| MobilePhone1 |字符串 |联系人的移动电话号码。 |
| HomeAddress |未定义 |联系人的家庭地址。 |
| BusinessAddress |未定义 |联系人的公司地址。 |
| OtherAddress |未定义 |联系人的其他地址。 |
| YomiCompanyName |字符串 |联系人的日语拼音公司名称。 |
| YomiGivenName |字符串 |联系人的日语拼音名字（名字）。 |
| YomiSurname |字符串 |联系人的日语拼音姓氏（姓氏） |
| Categories |数组 |与联系人相关联的类别。 |
| ChangeKey |字符串 |标识事件对象的版本 |
| DateTimeCreated |字符串 |联系人的创建时间。 |
| DateTimeLastModified |字符串 |联系人的修改时间。 |

#### <a name="get-contact"></a>获取联系人
此操作可从联系人文件夹中获取特定联系人。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| table* |文件夹 ID |选择联系人文件夹 |
| ID* |项目 ID |要检索的联系人的唯一标识符 |

星号 (*) 表示该属性是必需的。

##### <a name="output-details"></a>输出详细信息
Contact：联系人

| 属性名称 | 数据类型 | 说明 |
| --- | --- | --- |
| ID |字符串 |联系人的唯一标识符。 |
| ParentFolderId |字符串 |联系人的父文件夹的 ID |
| Birthday |字符串 |联系人的生日。 |
| FileAs |字符串 |要存档的联系人的姓名。 |
| DisplayName |字符串 |联系人的显示名称。 |
| GivenName |字符串 |联系人的名字。 |
| Initials |字符串 |联系人的姓名缩写。 |
| MiddleName |字符串 |联系人的中间名。 |
| NickName |字符串 |联系人的别名。 |
| Surname |字符串 |联系人的姓氏。 |
| 标题 |字符串 |联系人的头衔。 |
| Generation |字符串 |联系人的世代。 |
| EmailAddresses |数组 |联系人的电子邮件地址。 |
| ImAddresses |数组 |联系人的即时消息 (IM) 地址。 |
| JobTitle |字符串 |联系人的职务。 |
| CompanyName |字符串 |联系人所在的公司名称。 |
| 系 |字符串 |联系人所在的部门。 |
| OfficeLocation |字符串 |联系人的办公室地址。 |
| Profession |字符串 |联系人的职业。 |
| BusinessHomePage |字符串 |联系人的业务主页。 |
| AssistantName |字符串 |联系人的助理姓名。 |
| Manager |字符串 |联系人的经理姓名。 |
| HomePhones |数组 |联系人的住宅电话号码。 |
| BusinessPhones |数组 |联系人的公司电话号码 |
| MobilePhone1 |字符串 |联系人的移动电话号码。 |
| HomeAddress |未定义 |联系人的家庭地址。 |
| BusinessAddress |未定义 |联系人的公司地址。 |
| OtherAddress |未定义 |联系人的其他地址。 |
| YomiCompanyName |字符串 |联系人的日语拼音公司名称。 |
| YomiGivenName |字符串 |联系人的日语拼音名字（名字）。 |
| YomiSurname |字符串 |联系人的日语拼音姓氏（姓氏） |
| Categories |数组 |与联系人相关联的类别。 |
| ChangeKey |字符串 |标识事件对象的版本 |
| DateTimeCreated |字符串 |联系人的创建时间。 |
| DateTimeLastModified |字符串 |联系人的修改时间。 |

#### <a name="delete-contact"></a>删除联系人
此操作可从联系人文件夹中删除联系人。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| table* |文件夹 ID |选择联系人文件夹 |
| ID* |ID |要删除的联系人的唯一标识符 |

星号 (*) 表示该属性是必需的。

##### <a name="output-details"></a>输出详细信息
无。

#### <a name="update-contact"></a>更新联系人
此操作可在联系人文件夹中更新联系人。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| table* |文件夹 ID |选择联系人文件夹 |
| ID* |ID |要更新的联系人的唯一标识符 |
| item* |项目 |要更新的联系人项 |

星号 (*) 表示该属性是必需的。

##### <a name="output-details"></a>输出详细信息
Contact：联系人

| 属性名称 | 数据类型 | 说明 |
| --- | --- | --- |
| ID |字符串 |联系人的唯一标识符。 |
| ParentFolderId |字符串 |联系人的父文件夹的 ID |
| Birthday |字符串 |联系人的生日。 |
| FileAs |字符串 |要存档的联系人的姓名。 |
| DisplayName |字符串 |联系人的显示名称。 |
| GivenName |字符串 |联系人的名字。 |
| Initials |字符串 |联系人的姓名缩写。 |
| MiddleName |字符串 |联系人的中间名。 |
| NickName |字符串 |联系人的别名。 |
| Surname |字符串 |联系人的姓氏。 |
| 标题 |字符串 |联系人的头衔。 |
| Generation |字符串 |联系人的世代。 |
| EmailAddresses |数组 |联系人的电子邮件地址。 |
| ImAddresses |数组 |联系人的即时消息 (IM) 地址。 |
| JobTitle |字符串 |联系人的职务。 |
| CompanyName |字符串 |联系人所在的公司名称。 |
| 系 |字符串 |联系人所在的部门。 |
| OfficeLocation |字符串 |联系人的办公室地址。 |
| Profession |字符串 |联系人的职业。 |
| BusinessHomePage |字符串 |联系人的业务主页。 |
| AssistantName |字符串 |联系人的助理姓名。 |
| Manager |字符串 |联系人的经理姓名。 |
| HomePhones |数组 |联系人的住宅电话号码。 |
| BusinessPhones |数组 |联系人的公司电话号码 |
| MobilePhone1 |字符串 |联系人的移动电话号码。 |
| HomeAddress |未定义 |联系人的家庭地址。 |
| BusinessAddress |未定义 |联系人的公司地址。 |
| OtherAddress |未定义 |联系人的其他地址。 |
| YomiCompanyName |字符串 |联系人的日语拼音公司名称。 |
| YomiGivenName |字符串 |联系人的日语拼音名字（名字）。 |
| YomiSurname |字符串 |联系人的日语拼音姓氏（姓氏） |
| Categories |数组 |与联系人相关联的类别。 |
| ChangeKey |字符串 |标识事件对象的版本 |
| DateTimeCreated |字符串 |联系人的创建时间。 |
| DateTimeLastModified |字符串 |联系人的修改时间。 |

## <a name="http-responses"></a>HTTP 响应
上述操作和触发器可以返回以下一个或多个 HTTP 状态代码： 

| Name | 说明 |
| --- | --- |
| 200 |确定 |
| 202 |已接受 |
| 400 |错误的请求 |
| 401 |未授权 |
| 403 |禁止 |
| 404 |未找到 |
| 500 |内部服务器错误。 发生未知错误 |
| default |操作失败。 |

## <a name="next-steps"></a>后续步骤
[创建逻辑应用](../app-service-logic/app-service-logic-create-a-logic-app.md)。 在我们的 [API 列表](apis-list.md)中了解逻辑应用中的其他可用连接器。




<!--HONumber=Nov16_HO3-->


