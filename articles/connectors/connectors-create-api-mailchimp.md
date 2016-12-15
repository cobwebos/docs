---
title: MailChimp | Microsoft Docs
description: "使用 Azure App Service 创建逻辑应用。 MailChimp 是 SaaS 服务，允许企业管理和自动化电子邮件市场营销活动，包括发送市场营销电子邮件、自动化消息和有针对性的市场活动。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: 36559de2-94f0-4355-b492-2926dfc56486
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 3df3dfa25bc438cfe4a340ba50868d2f2263b44a


---
# <a name="get-started-with-the-mailchimp-connector"></a>MailChimp 连接器入门
MailChimp 是 SaaS 服务，允许企业管理和自动化电子邮件市场营销活动，包括发送市场营销电子邮件、自动化消息和有针对性的市场活动。

> [!NOTE]
> 此文章版本适用于 2015 年 8 月 1 日预览版架构的逻辑应用。
> 
> 

若要立即开始创建逻辑应用，请参阅[创建逻辑应用](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## <a name="triggers-and-actions"></a>触发器和操作
MailChimp 连接器可用作操作；它具有触发器。 所有连接器都支持采用 JSON 和 XML 格式的数据。

 MailChimp 连接器具有以下可用操作和/或触发器：

### <a name="mailchimp-actions"></a>MailChimp 操作
可执行以下操作：

| 操作 | 说明 |
| --- | --- |
| [newcampaign](connectors-create-api-mailchimp.md#newcampaign) |根据市场活动类型、收件人列表和市场活动设置（主题行、标题、from_name 和 reply_to）创建新的市场活动。 |
| [newlist](connectors-create-api-mailchimp.md#newlist) |在 MailChimp 帐户中新建列表 |
| [addmember](connectors-create-api-mailchimp.md#addmember) |添加或更新列表成员 |
| [removemember](connectors-create-api-mailchimp.md#removemember) |从列表中删除成员。 |
| [updatemember](connectors-create-api-mailchimp.md#updatemember) |更新有关特定列表成员的信息 |

### <a name="mailchimp-triggers"></a>MailChimp 触发器
可侦听以下事件：

| 触发器 | 说明 |
| --- | --- |
| 成员已添加到列表时 |在新成员已添加到列表时，触发工作流 |
| 新建列表时 |新建列表时，触发工作流 |

## <a name="create-a-connection-to-mailchimp"></a>创建到 MailChimp 的连接
若要使用 MailChimp 创建逻辑应用，必须先创建**连接**，然后提供以下属性的详细信息：

| 属性 | 必选 | 说明 |
| --- | --- | --- |
| 令牌 |是 |提供 MailChimp 凭据 |

> [!INCLUDE [Steps to create a connection to MailChimp](../../includes/connectors-create-api-mailchimp.md)]
> 
> [!TIP]
> 可在其他逻辑应用中使用此连接。
> 
> 

## <a name="reference-for-mailchimp"></a>MailChimp 的参考
适用于版本：1.0

## <a name="newcampaign"></a>newcampaign
新建活动：根据市场活动类型、收件人列表和市场活动设置（主题行、标题、from_name 和 reply_to）创建新的市场活动

```POST: /campaigns```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| newCampaignRequest | |是 |body |无 |要在正文中发送的具有新市场活动请求参数的 JSON 对象 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| 400 |错误的请求 |
| 401 |未授权 |
| 403 |禁止 |
| 404 |未找到 |
| 500 |内部服务器错误。 发生未知错误 |
| default |操作失败。 |

## <a name="newlist"></a>newlist
新建列表：在 MailChimp 帐户中新建列表

```POST: /lists```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| newListRequest | |是 |body |无 |要在正文中发送的具有新市场活动请求参数的 JSON 对象 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| 400 |错误的请求 |
| 401 |未授权 |
| 403 |禁止 |
| 404 |未找到 |
| 500 |内部服务器错误。 发生未知错误 |
| default |操作失败。 |

## <a name="addmember"></a>addmember
将成员添加到列表：添加或更新列表成员

```POST: /lists/{list_id}/members```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| list_id |字符串 |是 |路径 |无 |列表的唯一 ID |
| newMemberInList | |是 |body |无 |要在正文中发送的具有新成员信息的 JSON 对象 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| 400 |错误的请求 |
| 401 |未授权 |
| 403 |禁止 |
| 404 |未找到 |
| 500 |内部服务器错误。 发生未知错误 |
| default |操作失败。 |

## <a name="removemember"></a>removemember
从列表中删除成员：从列表中删除成员。

```DELETE: /lists/replacemailwithhash/{list_id}/members/{member_email}```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| list_id |字符串 |是 |路径 |无 |列表的唯一 ID |
| member_email |字符串 |是 |路径 |无 |要删除的成员的电子邮件地址 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| 400 |错误的请求 |
| 401 |未授权 |
| 403 |禁止 |
| 404 |未找到 |
| 500 |内部服务器错误。 发生未知错误 |
| default |操作失败。 |

## <a name="updatemember"></a>updatemember
更新成员信息：更新有关特定列表成员的信息

```PATCH: /lists/replacemailwithhash/{list_id}/members/{member_email}```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| list_id |字符串 |是 |路径 |无 |列表的唯一 ID |
| member_email |字符串 |是 |路径 |无 |要更新的成员的唯一电子邮件地址 |
| updateMemberInListRequest | |是 |body |无 |要在正文中发送的具有已更新成员信息的 JSON 对象 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| 400 |错误的请求 |
| 401 |未授权 |
| 403 |禁止 |
| 404 |未找到 |
| 500 |内部服务器错误。 发生未知错误 |
| default |操作失败。 |

## <a name="onmembersubscribed"></a>OnMemberSubscribed
成员已添加到列表时：在新成员已添加到列表时，触发工作流

```GET: /trigger/lists/{list_id}/members```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| list_id |字符串 |是 |路径 |无 |列表的唯一 ID |

#### <a name="response"></a>响应
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

## <a name="oncreatelist"></a>OnCreateList
新建列表时：新建列表时，触发工作流

```GET: /trigger/lists```

此调用没有任何参数

#### <a name="response"></a>响应
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

## <a name="object-definitions"></a>对象定义
### <a name="newcampaignrequest"></a>NewCampaignRequest
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| type |字符串 |是 |
| recipients |未定义 |是 |
| 设置 |未定义 |是 |
| variate_settings |未定义 |否 |
| tracking |未定义 |否 |
| rss_opts |未定义 |否 |
| social_card |未定义 |否 |

### <a name="recipient"></a>Recipient
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| list_id |字符串 |是 |
| segment_opts |未定义 |否 |

### <a name="settings"></a>设置
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| subject_line |字符串 |是 |
| title |字符串 |否 |
| from_name |字符串 |是 |
| reply_to |字符串 |是 |
| use_conversation |布尔值 |否 |
| to_name |字符串 |否 |
| folder_id |integer |否 |
| authenticate |布尔值 |否 |
| auto_footer |布尔值 |否 |
| inline_css |布尔值 |否 |
| auto_tweet |布尔值 |否 |
| auto_fb_post |数组 |否 |
| fb_comments |布尔值 |否 |

### <a name="variatesettings"></a>Variate_Settings
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| winner_criteria |字符串 |否 |
| wait_time |integer |否 |
| test_size |integer |否 |
| subject_lines |数组 |否 |
| send_times |数组 |否 |
| from_names |数组 |否 |
| reply_to_addresses |数组 |否 |

### <a name="tracking"></a>跟踪
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| opens |布尔值 |否 |
| html_clicks |布尔值 |否 |
| text_clicks |布尔值 |否 |
| goal_tracking |布尔值 |否 |
| ecomm360 |布尔值 |否 |
| google_analytics |字符串 |否 |
| clicktale |字符串 |否 |
| salesforce |未定义 |否 |
| highrise |未定义 |否 |
| capsule |未定义 |否 |

### <a name="rssopts"></a>RSS_Opts
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| feed_url |字符串 |否 |
| frequency |字符串 |否 |
| constrain_rss_img |字符串 |否 |
| schedule |未定义 |否 |

### <a name="socialcard"></a>Social_Card
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| image_url |字符串 |否 |
| description |字符串 |否 |
| title |字符串 |否 |

### <a name="segmentopts"></a>Segment_Opts
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| saved_segment_id |integer |否 |
| match |字符串 |否 |

### <a name="salesforce"></a>Salesforce
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| campaign |布尔值 |否 |
| 说明 |布尔值 |否 |

### <a name="highrise"></a>Highrise
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| campaign |布尔值 |否 |
| 说明 |布尔值 |否 |

### <a name="capsule"></a>Capsule
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| 说明 |布尔值 |否 |

### <a name="schedule"></a>计划
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| hour |integer |否 |
| daily_send |未定义 |否 |
| weekly_send_day |字符串 |否 |
| monthly_send_date |数字 |否 |

### <a name="dailysend"></a>Daily_Send
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| sunday |布尔值 |否 |
| monday |布尔值 |否 |
| tuesday |布尔值 |否 |
| wednesday |布尔值 |否 |
| thursday |布尔值 |否 |
| friday |布尔值 |否 |
| saturday |布尔值 |否 |

### <a name="campaignresponsemodel"></a>CampaignResponseModel
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| id |字符串 |否 |
| type |字符串 |否 |
| create_time |字符串 |否 |
| archive_url |字符串 |否 |
| status |字符串 |否 |
| emails_sent |integer |否 |
| send_time |字符串 |否 |
| content_type |字符串 |否 |
| recipient |数组 |否 |
| 设置 |未定义 |否 |
| variate_settings |未定义 |否 |
| tracking |未定义 |否 |
| rss_opts |未定义 |否 |
| ab_split_opts |未定义 |否 |
| social_card |未定义 |否 |
| report_summary |未定义 |否 |
| delivery_status |未定义 |否 |
| _links |数组 |否 |

### <a name="absplitopts"></a>AB_Split_Opts
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| split_test |字符串 |否 |
| pick_winner |字符串 |否 |
| wait_units |字符串 |否 |
| wait_time |integer |否 |
| split_size |integer |否 |
| from_name_a |字符串 |否 |
| from_name_b |字符串 |否 |
| reply_email_a |字符串 |否 |
| reply_email_b |字符串 |否 |
| subject_a |字符串 |否 |
| subject_b |字符串 |否 |
| send_time_a |字符串 |否 |
| send_time_b |字符串 |否 |
| send_time_winner |字符串 |否 |

### <a name="reportsummary"></a>Report_Summary
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| opens |integer |否 |
| unique_opens |integer |否 |
| open_rate |数字 |否 |
| clicks |integer |否 |
| subscriber_clicks |数字 |否 |
| click_rate |数字 |否 |

### <a name="deliverystatus"></a>Delivery_Status
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| 已启用 |布尔值 |否 |
| can_cancel |布尔值 |否 |
| status |字符串 |否 |
| emails_sent |integer |否 |
| emails_canceled |integer |否 |

### <a name="link"></a>链接
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| rel |字符串 |否 |
| href |字符串 |否 |
| method |字符串 |否 |
| targetSchema |字符串 |否 |
| schema |字符串 |否 |

### <a name="newlistrequest"></a>NewListRequest
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| 名称 |字符串 |是 |
| contact |未定义 |是 |
| permission_reminder |字符串 |是 |
| use_archive_bar |布尔值 |否 |
| campaign_defaults |未定义 |是 |
| notify_on_subscribe |字符串 |否 |
| notify_on_unsubscribe |字符串 |否 |
| email_type_option |布尔值 |是 |
| visibility |字符串 |否 |

### <a name="contact"></a>联系人
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| company |字符串 |是 |
| address1 |字符串 |是 |
| address2 |字符串 |否 |
| city |字符串 |是 |
| state |字符串 |是 |
| zip |字符串 |是 |
| country |字符串 |是 |
| phone |字符串 |是 |

### <a name="campaigndefaults"></a>Campaign_Defaults
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| from_name |字符串 |是 |
| from_email |字符串 |是 |
| subject |字符串 |否 |
| 语言 |字符串 |是 |

### <a name="createnewlistresponsemodel"></a>CreateNewListResponseModel
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| id |字符串 |是 |
| 名称 |字符串 |是 |
| contact |未定义 |是 |
| permission_reminder |字符串 |是 |
| use_archive_bar |布尔值 |否 |
| campaign_defaults |未定义 |是 |
| notify_on_subscribe |字符串 |否 |
| notify_on_unsubscribe |字符串 |否 |
| date_created |字符串 |否 |
| list_rating |integer |否 |
| email_type_option |布尔值 |是 |
| subscribe_url_short |字符串 |否 |
| subscribe_url_long |字符串 |否 |
| beamer_address |字符串 |否 |
| visibility |字符串 |否 |
| modules |数组 |否 |
| stats |未定义 |否 |
| _links |数组 |否 |

### <a name="stats"></a>统计信息
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| member_count |integer |否 |
| unsubscribe_count |integer |否 |
| cleaned_count |integer |否 |
| member_count_since_send |integer |否 |
| unsubscribe_count_since_send |integer |否 |
| cleaned_count_since_send |integer |否 |
| campaign_count |integer |否 |
| campaign_last_sent |integer |否 |
| merge_field_count |integer |否 |
| avg_sub_rate |数字 |否 |
| avg_unsub_rate |数字 |否 |
| target_sub_rate |数字 |否 |
| open_rate |数字 |否 |
| click_rate |数字 |否 |
| last_sub_date |字符串 |否 |
| last_unsub_date |字符串 |否 |

### <a name="getlistsresponsemodel"></a>GetListsResponseModel
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| lists |数组 |否 |
| total_items |integer |否 |

### <a name="newmemberinlistrequest"></a>NewMemberInListRequest
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| email_type |字符串 |否 |
| status |字符串 |是 |
| merge_fields |未定义 |否 |
| interests |字符串 |否 |
| 语言 |字符串 |否 |
| vip |布尔值 |否 |
| location |未定义 |否 |
| email_address |字符串 |是 |

### <a name="firstandlastname"></a>FirstAndLastName
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| FNAME |字符串 |否 |
| LNAME |字符串 |否 |

### <a name="location"></a>位置
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| latitude |数字 |否 |
| longitude |数字 |否 |

### <a name="memberresponsemodel"></a>MemberResponseModel
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| id |字符串 |否 |
| email_address |字符串 |否 |
| unique_email_id |字符串 |否 |
| email_type |字符串 |否 |
| status |字符串 |否 |
| merge_fields |未定义 |否 |
| interests |字符串 |否 |
| stats |未定义 |否 |
| ip_signup |字符串 |否 |
| timestamp_signup |字符串 |否 |
| ip_opt |字符串 |否 |
| timestamp_opt |字符串 |否 |
| member_rating |integer |否 |
| last_changed |字符串 |否 |
| 语言 |字符串 |否 |
| vip |布尔值 |否 |
| email_client |字符串 |否 |
| location |未定义 |否 |
| last_note |未定义 |否 |
| list_id |字符串 |否 |
| _links |数组 |否 |

### <a name="lastnote"></a>Last_Note
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| note_id |integer |否 |
| created_at |字符串 |否 |
| created_by |字符串 |否 |
| note |字符串 |否 |

### <a name="getallmembersresponsemodel"></a>GetAllMembersResponseModel
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| members |数组 |否 |
| list_id |字符串 |否 |
| total_items |integer |否 |

### <a name="object"></a>对象
### <a name="updatememberinlistrequest"></a>UpdateMemberInListRequest
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| email_address |字符串 |否 |
| email_type |字符串 |否 |
| status |字符串 |是 |
| merge_fields |未定义 |否 |
| interests |字符串 |否 |
| 语言 |字符串 |否 |
| vip |布尔值 |否 |
| location |未定义 |否 |

### <a name="getmembersresponsemodel"></a>GetMembersResponseModel
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| members |数组 |否 |
| list_id |字符串 |否 |
| total_items |integer |否 |

### <a name="adduserresponsemodel"></a>AddUserResponseModel
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| id |字符串 |是 |
| email_address |字符串 |是 |
| unique_email_id |字符串 |否 |
| email_type |字符串 |否 |
| status |字符串 |否 |
| merge_fields |未定义 |是 |
| interests |字符串 |否 |
| stats |未定义 |否 |
| ip_signup |字符串 |否 |
| timestamp_signup |字符串 |否 |
| ip_opt |字符串 |否 |
| timestamp_opt |字符串 |否 |
| member_rating |integer |否 |
| last_changed |字符串 |否 |
| 语言 |字符串 |否 |
| vip |布尔值 |否 |
| email_client |字符串 |否 |
| location |未定义 |否 |
| last_note |未定义 |否 |
| list_id |字符串 |否 |
| _links |数组 |否 |

## <a name="next-steps"></a>后续步骤
[创建逻辑应用](../app-service-logic/app-service-logic-create-a-logic-app.md)




<!--HONumber=Nov16_HO3-->


