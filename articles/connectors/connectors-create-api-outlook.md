---
title: Outlook.com | Microsoft Docs
description: "使用 Azure App Service 创建逻辑应用。 Outlook.com 连接器允许你管理邮件、日历和联系人。 你可以执行各种操作，例如发送邮件、安排会议、添加联系人等。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: 87113c85-d158-4dd5-9ed5-5748130003d6
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 13d0a71762d3cba05c3dccb647d0890862307327
ms.openlocfilehash: 9593804ba5baa4ffe4cf49e21b5698c5fd3d157e


---
# <a name="get-started-with-the-outlookcom-connector"></a>Outlook.com 连接器入门
Outlook.com 连接器允许你管理邮件、日历和联系人。 你可以执行各种操作，例如发送邮件、安排会议、添加联系人等。

> [!NOTE]
> 此文章版本适用于 2015 年 8 月 1 日预览版架构的逻辑应用。
>
>

若要立即开始创建逻辑应用，请参阅[创建逻辑应用](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## <a name="triggers-and-actions"></a>触发器和操作
Outlook.com 连接器可用作操作；它具有触发器。 所有连接器都支持采用 JSON 和 XML 格式的数据。

 Outlook.com 连接器具有以下可用操作和/或触发器：

### <a name="outlookcom-actions"></a>Outlook.com 操作
可执行以下操作：

| 操作 | 说明 |
| --- | --- |
| [GetEmails](connectors-create-api-outlook.md#getemails) |从文件夹中检索电子邮件 |
| [SendEmail](connectors-create-api-outlook.md#sendemail) |发送电子邮件 |
| [DeleteEmail](connectors-create-api-outlook.md#deleteemail) |按 ID 删除电子邮件 |
| [MarkAsRead](connectors-create-api-outlook.md#markasread) |将电子邮件标记为已读 |
| [ReplyTo](connectors-create-api-outlook.md#replyto) |回复电子邮件 |
| [GetAttachment](connectors-create-api-outlook.md#getattachment) |按 ID 检索电子邮件附件 |
| [SendMailWithOptions](connectors-create-api-outlook.md#sendmailwithoptions) |发送带有多个选项的电子邮件，并等待收件人使用选项之一进行回复 |
| [SendApprovalMail](connectors-create-api-outlook.md#sendapprovalmail) |发送批准电子邮件并等待收件人回复 |
| [CalendarGetTables](connectors-create-api-outlook.md#calendargettables) |检索日历 |
| [CalendarGetItems](connectors-create-api-outlook.md#calendargetitems) |从日历中检索项 |
| [CalendarPostItem](connectors-create-api-outlook.md#calendarpostitem) |新建事件 |
| [CalendarGetItem](connectors-create-api-outlook.md#calendargetitem) |从日历中检索特定项 |
| [CalendarDeleteItem](connectors-create-api-outlook.md#calendardeleteitem) |删除日历项 |
| [CalendarPatchItem](connectors-create-api-outlook.md#calendarpatchitem) |部分更新日历项 |
| [ContactGetTables](connectors-create-api-outlook.md#contactgettables) |检索联系人文件夹 |
| [ContactGetItems](connectors-create-api-outlook.md#contactgetitems) |从联系人文件夹中检索联系人 |
| [ContactPostItem](connectors-create-api-outlook.md#contactpostitem) |新建联系人 |
| [ContactGetItem](connectors-create-api-outlook.md#contactgetitem) |从联系人文件夹中检索特定联系人 |
| [ContactDeleteItem](connectors-create-api-outlook.md#contactdeleteitem) |删除联系人 |
| [ContactPatchItem](connectors-create-api-outlook.md#contactpatchitem) |部分更新联系人 |

### <a name="outlookcom-triggers"></a>Outlook.com 触发器
可侦听以下事件：

| 触发器 | 说明 |
| --- | --- |
| 即将启动事件时 |在启动将要发生的日历事件时，触发流 |
| 新电子邮件到达时 |在新电子邮件到达时，触发流 |
| 新建项时 |已在新建日历项时触发 |
| 更新项时 |已在修改日历项时触发 |

## <a name="create-a-connection-to-outlookcom"></a>创建到 Outlook.com 的连接
若要使用 Outlook.com 创建逻辑应用，必须先创建**连接**，然后提供以下属性的详细信息：

| 属性 | 必选 | 说明 |
| --- | --- | --- |
| 令牌 |是 |提供 Outlook.com 凭据 |

创建连接后，可使用它执行操作并侦听触发器，如本文所述。

> [!INCLUDE [Steps to create a connection to Outlook.com](../../includes/connectors-create-api-outlook.md)]
>
> [!TIP]
> 可在其他逻辑应用中使用此连接。  
>
>

## <a name="reference-for-outlookcom"></a>Outlook.com 的参考
适用于版本：1.0

## <a name="onupcomingevents"></a>OnUpcomingEvents
即将启动事件时：在启动将要发生的日历事件时，触发流

```GET: /Events/OnUpcomingEvents```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| 表 |字符串 |是 |查询 |无 |日历的唯一标识符 |
| lookAheadTimeInMinutes |integer |否 |查询 |15 |将要发生的事件的预计时间（以分钟为单位） |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |操作成功 |
| 202 |操作成功 |
| 400 |BadRequest |
| 401 |未授权 |
| 403 |禁止 |
| 500 |内部服务器错误 |
| default |操作失败。 |

## <a name="getemails"></a>GetEmails
获取电子邮件：从文件夹中检索电子邮件

```GET: /Mail```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| folderPath |字符串 |否 |查询 |Inbox |要检索电子邮件的文件夹的路径（默认值：“Inbox”） |
| top |integer |否 |查询 |10 |要检索的电子邮件数（默认值：10） |
| fetchOnlyUnread |布尔值 |否 |查询 |是 |仅检索未读电子邮件吗？ |
| includeAttachments |布尔值 |否 |查询 |false |如果设置为 true，附件也将与电子邮件一起检索 |
| searchQuery |字符串 |否 |查询 |无 |搜索查询，筛选电子邮件 |
| skip |integer |否 |查询 |0 |要跳过的电子邮件数（默认值：0） |
| skipToken |字符串 |否 |查询 |无 |跳过令牌，提取新页面 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |操作成功 |
| 400 |BadRequest |
| 401 |未授权 |
| 403 |禁止 |
| 500 |内部服务器错误 |
| default |操作失败。 |

## <a name="sendemail"></a>SendEmail
发送电子邮件：发送电子邮件

```POST: /Mail```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| emailMessage | |是 |body |无 |电子邮件 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |操作成功 |
| 400 |BadRequest |
| 401 |未授权 |
| 403 |禁止 |
| 500 |内部服务器错误 |
| default |操作失败。 |

## <a name="deleteemail"></a>DeleteEmail
删除电子邮件：按 ID 删除电子邮件

```DELETE: /Mail/{messageId}```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| messageId |字符串 |是 |路径 |无 |要删除的电子邮件的 ID |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |操作成功 |
| 400 |BadRequest |
| 401 |未授权 |
| 403 |禁止 |
| 500 |内部服务器错误 |
| default |操作失败。 |

## <a name="markasread"></a>MarkAsRead
标记为已读：将电子邮件标记为已读

```POST: /Mail/MarkAsRead/{messageId}```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| messageId |字符串 |是 |路径 |无 |要标记为已读的电子邮件的 ID |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |操作成功 |
| 400 |BadRequest |
| 401 |未授权 |
| 403 |禁止 |
| 500 |内部服务器错误 |
| default |操作失败。 |

## <a name="replyto"></a>ReplyTo
回复电子邮件：回复电子邮件

```POST: /Mail/ReplyTo/{messageId}```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| messageId |字符串 |是 |路径 |无 |要回复的电子邮件的 ID |
| comment |字符串 |是 |查询 |无 |回复注释 |
| replyAll |布尔值 |否 |查询 |false |回复所有收件人 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |操作成功 |
| 400 |BadRequest |
| 401 |未授权 |
| 403 |禁止 |
| 500 |内部服务器错误 |
| default |操作失败。 |

## <a name="getattachment"></a>GetAttachment
获取附件：按 ID 检索电子邮件附件

```GET: /Mail/{messageId}/Attachments/{attachmentId}```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| messageId |字符串 |是 |路径 |无 |电子邮件的 ID |
| attachmentId |字符串 |是 |路径 |无 |要下载的附件的 ID |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |操作成功 |
| 400 |BadRequest |
| 401 |未授权 |
| 403 |禁止 |
| 500 |内部服务器错误 |
| default |操作失败。 |

## <a name="onnewemail"></a>OnNewEmail
新电子邮件到达时：在新电子邮件到达时，触发流

```GET: /Mail/OnNewEmail```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| folderPath |字符串 |否 |查询 |Inbox |要检索的电子邮件文件夹（默认值：Inbox） |
| to |字符串 |否 |查询 |无 |收件人电子邮件地址 |
| from |字符串 |否 |查询 |无 |发件人地址 |
| importance |字符串 |否 |查询 |一般 |电子邮件的重要性（高、正常、低）（默认值：正常） |
| fetchOnlyWithAttachment |布尔值 |否 |查询 |false |仅检索带有附件的电子邮件 |
| includeAttachments |布尔值 |否 |查询 |false |包括附件 |
| subjectFilter |字符串 |否 |查询 |无 |要在主题中查找的字符串 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |操作成功 |
| 202 |已接受 |
| 400 |BadRequest |
| 401 |未授权 |
| 403 |禁止 |
| 500 |内部服务器错误 |
| default |操作失败。 |

## <a name="sendmailwithoptions"></a>SendMailWithOptions
发送带有选项的电子邮件：发送带有多个选项的电子邮件，并等待收件人使用选项之一进行回复

```POST: /mailwithoptions/$subscriptions```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| optionsEmailSubscription | |是 |body |无 |选项电子邮件的订阅请求 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| 201 |订阅已创建 |
| 400 |BadRequest |
| 401 |未授权 |
| 403 |禁止 |
| 500 |内部服务器错误 |
| default |操作失败。 |

## <a name="sendapprovalmail"></a>SendApprovalMail
发送批准电子邮件：发送批准电子邮件并等待收件人回复

```POST: /approvalmail/$subscriptions```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| approvalEmailSubscription | |是 |body |无 |批准电子邮件的订阅请求 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| 201 |订阅已创建 |
| 400 |BadRequest |
| 401 |未授权 |
| 403 |禁止 |
| 500 |内部服务器错误 |
| default |操作失败。 |

## <a name="calendargettables"></a>CalendarGetTables
获取日历：检索日历

```GET: /datasets/calendars/tables```

此调用没有任何参数

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

## <a name="calendargetitems"></a>CalendarGetItems
获取事件：从日历中检索项

```GET: /datasets/calendars/tables/{table}/items```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| 表 |字符串 |是 |路径 |无 |要检索的日历的唯一标识符 |
| $filter |字符串 |否 |查询 |无 |要限制项数的 ODATA 筛选查询 |
| $orderby |字符串 |否 |查询 |无 |用于指定项顺序的 ODATA orderBy 查询 |
| $skip |integer |否 |查询 |无 |要跳过的项数（默认值 = 0） |
| $top |integer |否 |查询 |无 |要检索的最大项数（默认值 = 256） |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

## <a name="calendarpostitem"></a>CalendarPostItem
创建事件：新建事件

```POST: /datasets/calendars/tables/{table}/items```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| 表 |字符串 |是 |路径 |无 |日历的唯一标识符 |
| item | |是 |body |无 |要创建的日历项 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

## <a name="calendargetitem"></a>CalendarGetItem
获取事件：从日历中检索特定项

```GET: /datasets/calendars/tables/{table}/items/{id}```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| 表 |字符串 |是 |路径 |无 |日历的唯一标识符 |
| id |字符串 |是 |路径 |无 |要检索的日历项的唯一标识符 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

## <a name="calendardeleteitem"></a>CalendarDeleteItem
删除事件：删除日历项

```DELETE: /datasets/calendars/tables/{table}/items/{id}```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| 表 |字符串 |是 |路径 |无 |日历的唯一标识符 |
| id |字符串 |是 |路径 |无 |要删除的日历项的唯一标识符 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

## <a name="calendarpatchitem"></a>CalendarPatchItem
更新事件：部分更新日历项

```PATCH: /datasets/calendars/tables/{table}/items/{id}```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| 表 |字符串 |是 |路径 |无 |日历的唯一标识符 |
| id |字符串 |是 |路径 |无 |要更新的日历项的唯一标识符 |
| item | |是 |body |无 |要更新的日历项 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

## <a name="calendargetonnewitems"></a>CalendarGetOnNewItems
新建项时：已在新建日历项时触发

```GET: /datasets/calendars/tables/{table}/onnewitems```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| 表 |字符串 |是 |路径 |无 |日历的唯一标识符 |
| $filter |字符串 |否 |查询 |无 |要限制项数的 ODATA 筛选查询 |
| $orderby |字符串 |否 |查询 |无 |用于指定项顺序的 ODATA orderBy 查询 |
| $skip |integer |否 |查询 |无 |要跳过的项数（默认值 = 0） |
| $top |integer |否 |查询 |无 |要检索的最大项数（默认值 = 256） |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

## <a name="calendargetonupdateditems"></a>CalendarGetOnUpdatedItems
更新项时：已在修改日历项时触发

```GET: /datasets/calendars/tables/{table}/onupdateditems```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| 表 |字符串 |是 |路径 |无 |日历的唯一标识符 |
| $filter |字符串 |否 |查询 |无 |要限制项数的 ODATA 筛选查询 |
| $orderby |字符串 |否 |查询 |无 |用于指定项顺序的 ODATA orderBy 查询 |
| $skip |integer |否 |查询 |无 |要跳过的项数（默认值 = 0） |
| $top |integer |否 |查询 |无 |要检索的最大项数（默认值 = 256） |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

## <a name="contactgettables"></a>ContactGetTables
获取联系人文件夹：检索联系人文件夹

```GET: /datasets/contacts/tables```

此调用没有任何参数

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

## <a name="contactgetitems"></a>ContactGetItems
获取联系人：从联系人文件夹中检索联系人

```GET: /datasets/contacts/tables/{table}/items```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| 表 |字符串 |是 |路径 |无 |要检索的联系人文件夹的唯一标识符 |
| $filter |字符串 |否 |查询 |无 |要限制项数的 ODATA 筛选查询 |
| $orderby |字符串 |否 |查询 |无 |用于指定项顺序的 ODATA orderBy 查询 |
| $skip |integer |否 |查询 |无 |要跳过的项数（默认值 = 0） |
| $top |integer |否 |查询 |无 |要检索的最大项数（默认值 = 256） |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

## <a name="contactpostitem"></a>ContactPostItem
创建联系人：新建联系人

```POST: /datasets/contacts/tables/{table}/items```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| 表 |字符串 |是 |路径 |无 |联系人文件夹的唯一标识符 |
| item | |是 |body |无 |要创建的联系人 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

## <a name="contactgetitem"></a>ContactGetItem
获取联系人：从联系人文件夹中检索特定联系人

```GET: /datasets/contacts/tables/{table}/items/{id}```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| 表 |字符串 |是 |路径 |无 |联系人文件夹的唯一标识符 |
| id |字符串 |是 |路径 |无 |要检索的联系人的唯一标识符 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

## <a name="contactdeleteitem"></a>ContactDeleteItem
删除联系人：删除联系人

```DELETE: /datasets/contacts/tables/{table}/items/{id}```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| 表 |字符串 |是 |路径 |无 |联系人文件夹的唯一标识符 |
| id |字符串 |是 |路径 |无 |要删除的联系人的唯一标识符 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

## <a name="contactpatchitem"></a>ContactPatchItem
更新联系人：部分更新联系人

```PATCH: /datasets/contacts/tables/{table}/items/{id}```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| 表 |字符串 |是 |路径 |无 |联系人文件夹的唯一标识符 |
| id |字符串 |是 |路径 |无 |要更新的联系人的唯一标识符 |
| item | |是 |body |无 |要更新的联系人项 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

## <a name="object-definitions"></a>对象定义
### <a name="triggerbatchresponseidictionarystringobject"></a>TriggerBatchResponse[IDictionary[String,Object]]
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| value |数组 |否 |

### <a name="object"></a>对象
### <a name="sendmessage"></a>SendMessage
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| Attachments |数组 |否 |
| 从 |字符串 |否 |
| Cc |字符串 |否 |
| Bcc |字符串 |否 |
| 使用者 |字符串 |是 |
| 正文 |字符串 |是 |
| 重要性 |字符串 |否 |
| IsHtml |布尔值 |否 |
| 如果 |字符串 |是 |

### <a name="sendattachment"></a>SendAttachment
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| @odata.type |字符串 |否 |
| Name |字符串 |是 |
| ContentBytes |字符串 |是 |

### <a name="receivemessage"></a>ReceiveMessage
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| ID |字符串 |否 |
| IsRead |布尔值 |否 |
| HasAttachment |布尔值 |否 |
| DateTimeReceived |字符串 |否 |
| Attachments |数组 |否 |
| 从 |字符串 |否 |
| Cc |字符串 |否 |
| Bcc |字符串 |否 |
| 使用者 |字符串 |是 |
| 正文 |字符串 |是 |
| 重要性 |字符串 |否 |
| IsHtml |布尔值 |否 |
| 如果 |字符串 |是 |

### <a name="receiveattachment"></a>ReceiveAttachment
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| ID |字符串 |是 |
| ContentType |字符串 |是 |
| @odata.type |字符串 |否 |
| Name |字符串 |是 |
| ContentBytes |字符串 |是 |

### <a name="digestmessage"></a>DigestMessage
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| 使用者 |字符串 |是 |
| 正文 |字符串 |否 |
| 重要性 |字符串 |否 |
| Digest |数组 |是 |
| Attachments |数组 |否 |
| 如果 |字符串 |是 |

### <a name="triggerbatchresponsereceivemessage"></a>TriggerBatchResponse[ReceiveMessage]
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| value |数组 |否 |

### <a name="datasetsmetadata"></a>DataSetsMetadata
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| tabular |未定义 |否 |
| Blob |未定义 |否 |

### <a name="tabulardatasetsmetadata"></a>TabularDataSetsMetadata
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| 源 |字符串 |否 |
| displayName |字符串 |否 |
| urlEncoding |字符串 |否 |
| tableDisplayName |字符串 |否 |
| tablePluralName |字符串 |否 |

### <a name="blobdatasetsmetadata"></a>BlobDataSetsMetadata
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| 源 |字符串 |否 |
| displayName |字符串 |否 |
| urlEncoding |字符串 |否 |

### <a name="tablemetadata"></a>TableMetadata
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| 名称 |字符串 |否 |
| title |字符串 |否 |
| x-ms-permission |字符串 |否 |
| x-ms-capabilities |未定义 |否 |
| schema |未定义 |否 |

### <a name="tablecapabilitiesmetadata"></a>TableCapabilitiesMetadata
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| sortRestrictions |未定义 |否 |
| filterRestrictions |未定义 |否 |
| filterFunctions |数组 |否 |

### <a name="tablesortrestrictionsmetadata"></a>TableSortRestrictionsMetadata
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| sortable |布尔值 |否 |
| unsortableProperties |数组 |否 |
| ascendingOnlyProperties |数组 |否 |

### <a name="tablefilterrestrictionsmetadata"></a>TableFilterRestrictionsMetadata
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| filterable |布尔值 |否 |
| nonFilterableProperties |数组 |否 |
| requiredProperties |数组 |否 |

### <a name="optionsemailsubscription"></a>OptionsEmailSubscription
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| NotificationUrl |字符串 |否 |
| 消息 |未定义 |否 |

### <a name="messagewithoptions"></a>MessageWithOptions
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| 使用者 |字符串 |是 |
| 选项 |字符串 |是 |
| 正文 |字符串 |否 |
| 重要性 |字符串 |否 |
| Attachments |数组 |否 |
| 如果 |字符串 |是 |

### <a name="subscriptionresponse"></a>SubscriptionResponse
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| id |字符串 |否 |
| resource |字符串 |否 |
| notificationType |字符串 |否 |
| notificationUrl |字符串 |否 |

### <a name="approvalemailsubscription"></a>ApprovalEmailSubscription
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| NotificationUrl |字符串 |否 |
| 消息 |未定义 |否 |

### <a name="approvalmessage"></a>ApprovalMessage
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| 使用者 |字符串 |是 |
| 选项 |字符串 |是 |
| 正文 |字符串 |否 |
| 重要性 |字符串 |否 |
| Attachments |数组 |否 |
| 如果 |字符串 |是 |

### <a name="approvalemailresponse"></a>ApprovalEmailResponse
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| SelectedOption |字符串 |否 |

### <a name="tableslist"></a>TablesList
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| value |数组 |否 |

### <a name="table"></a>表
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| Name |字符串 |否 |
| DisplayName |字符串 |否 |

### <a name="item"></a>项目
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| ItemInternalId |字符串 |否 |

### <a name="calendaritemslist"></a>CalendarItemsList
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| value |数组 |否 |

### <a name="calendaritem"></a>CalendarItem
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| ItemInternalId |字符串 |否 |

### <a name="contactitemslist"></a>ContactItemsList
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| value |数组 |否 |

### <a name="contactitem"></a>ContactItem
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| ItemInternalId |字符串 |否 |

### <a name="datasetslist"></a>DataSetsList
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| value |数组 |否 |

### <a name="dataset"></a>DataSet
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| Name |字符串 |否 |
| DisplayName |字符串 |否 |

## <a name="next-steps"></a>后续步骤
[创建逻辑应用](../app-service-logic/app-service-logic-create-a-logic-app.md)



<!--HONumber=Nov16_HO3-->


