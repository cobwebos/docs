---
title: " 在逻辑应用中使用 Slack 连接器 | Microsoft 文档s"
description: "开始在逻辑应用中使用 Slack 连接器"
services: 
documentationcenter: 
author: msftman
manager: anneta
editor: 
tags: connectors
ms.assetid: 234cad64-b13d-4494-ae78-18b17119ba24
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 9c74b25a2ac5e2088a841d97920035376b7f3f11
ms.openlocfilehash: 404776283ee10aa9410d002597e054757516bcab


---
# <a name="get-started-with-the-slack-connector"></a>Slack 连接器入门
Slack 是一款团队通信工具，可将所有团队通信归于一处，可即时搜索，并且随时随地可用。

> [!NOTE]
> 此文章版本适用于 2015 年 8 月 1 日预览版架构的逻辑应用。
> 
> 

借助 Slack 连接器，可以：

* 使用它生成逻辑应用

若要在逻辑应用中添加操作，请参阅[创建逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)。

## <a name="lets-talk-about-triggers-and-actions"></a>让我们谈谈触发器和操作
Slack 连接器可用作操作；没有触发器。 所有连接器都支持采用 JSON 和 XML 格式的数据。 

 Slack 连接器具有以下可用操作和/或触发器：

### <a name="slack-actions"></a>Slack 操作
可执行以下操作：

| 操作 | 说明 |
| --- | --- |
| PostMessage |将消息发布到指定通道。 |

## <a name="create-a-connection-to-slack"></a>创建到 Slack 的连接
若要使用 Slack 连接，首先创建**连接**，然后为以下属性提供详细信息： 

| 属性 | 必选 | 说明 |
| --- | --- | --- |
| 令牌 |是 |提供 Slack 凭据 |

按照以下步骤登录 Slack 并在逻辑应用中的 Slack“连接”中完成配置：

1. 选择“重复周期”
2. 选择“频率”并输入“间隔”
3. 选择“添加操作”  
   ![配置 Slack][1]  
4. 在搜索框中输入“Slack”，然后等待搜索返回在名称中带有 Slack 的所有项
5. 选择“Slack - 发布消息”
6. 选择“登录到 Slack”：  
   ![配置 Slack][2]
7. 提供用于登录的 Slack 凭据以向应用程序授权    
   ![配置 Slack][3]  
8. 你将重定向到组织的登录页面。 **授权** Slack 与逻辑应用交互：      
   ![配置 Slack][5] 
9. 授权完成后，你将重定向到逻辑应用，通过配置“Slack - 获取所有消息”部分完成它。 添加所需的其他触发器和操作。  
   ![配置 Slack][6]
10. 通过选择上方菜单栏中的“保存” 保存工作。

> [!TIP]
> 可在其他逻辑应用中使用此连接。
> 
> 

## <a name="slack-rest-api-reference"></a>Slack REST API 参考
#### <a name="this-documentation-is-for-version-10"></a>此文档用于版本：1.0
### <a name="post-a-message-to-a-specified-channel"></a>将消息发布到指定通道。
**```POST: /chat.postMessage```** 

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| channel |字符串 |是 |查询 |无 |要向其发送消息的通道、专用组或 IM 通道。 可以是一个名称（例如：#general）或编码 ID。 |
| text |字符串 |是 |查询 |无 |要发送的消息文本。 有关格式设置选项，请参阅 https://api.slack.com/docs/formatting。 |
| username |字符串 |否 |查询 |无 |自动程序名称 |
| as_user |布尔值 |否 |查询 |无 |传递 true 以经过身份验证的用户身份（而不是以自动程序身份）发布消息 |
| parse |字符串 |否 |查询 |无 |更改处理消息的方式。 有关详细信息，请参阅 https://api.slack.com/docs/formatting。 |
| link_names |integer |否 |查询 |无 |查找并链接通道名称和用户名。 |
| unfurl_links |布尔值 |否 |查询 |无 |传递 true 启用主要基于文本的内容的展开。 |
| unfurl_media |布尔值 |否 |查询 |无 |传递 false 禁用媒体内容的展开。 |
| icon_url |字符串 |否 |查询 |无 |要在此消息中用作图标的图像的 URL |
| icon_emoji |字符串 |否 |查询 |无 |要在此消息中用作图标的表情符号 |

### <a name="here-are-the-possible-responses"></a>下面是可能的响应：
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| 400 |错误的请求 |
| 408 |请求超时 |
| 429 |请求过多 |
| 500 |内部服务器错误。 发生未知错误 |
| 503 |Slack 服务不可用 |
| 504 |网关超时 |
| default |操作失败。 |

- - -
## <a name="object-definitions"></a>对象定义：
 **消息**：Slack 消息

Message 的必需属性：

没有一个属性是必需属性。 

**所有属性**： 

| Name | 数据类型 |
| --- | --- |
| id |integer |
| content_excerpt |字符串 |
| sender_id |integer |
| replied_to_id |integer |
| created_at |字符串 |
| network_id |integer |
| message_type |字符串 |
| sender_type |字符串 |
| url |字符串 |
| web_url |字符串 |
| group_id |integer |
| body |未定义 |
| thread_id |integer |
| direct_message |布尔值 |
| client_type |字符串 |
| client_url |字符串 |
| 语言 |字符串 |
| notified_user_ids |数组 |
| privacy |字符串 |
| liked_by |未定义 |
| system_message |布尔值 |

 **PostOperationRequest**：表示使 Slack 连接器向 Slack 进行发布的发布请求

PostOperationRequest 的必需属性：

body

**所有属性**： 

| Name | 数据类型 |
| --- | --- |
| body |字符串 |
| group_id |integer |
| replied_to_id |integer |
| direct_to_id |integer |
| broadcast |布尔值 |
| topic1 |字符串 |
| topic2 |字符串 |
| topic3 |字符串 |
| topic4 |字符串 |
| topic5 |字符串 |
| topic6 |字符串 |
| topic7 |字符串 |
| topic8 |字符串 |
| topic9 |字符串 |
| topic10 |字符串 |
| topic11 |字符串 |
| topic12 |字符串 |
| topic13 |字符串 |
| topic14 |字符串 |
| topic15 |字符串 |
| topic16 |字符串 |
| topic17 |字符串 |
| topic18 |字符串 |
| topic19 |字符串 |
| topic20 |字符串 |

 **MessageList**：消息列表

MessageList 的必需属性：

没有一个属性是必需属性。 

**所有属性**： 

| Name | 数据类型 |
| --- | --- |
| messages |数组 |

 **MessageBody**：消息正文

MessageBody 的必需属性：

没有一个属性是必需属性。 

**所有属性**： 

| Name | 数据类型 |
| --- | --- |
| parsed |字符串 |
| plain |字符串 |
| rich |字符串 |

 **LikedBy**：赞者

LikedBy 的必需属性：

没有一个属性是必需属性。 

**所有属性**： 

| Name | 数据类型 |
| --- | --- |
| count |integer |
| names |数组 |

 **YammmerEntity**：赞者

YammmerEntity 的必需属性：

没有一个属性是必需属性。 

**所有属性**： 

| Name | 数据类型 |
| --- | --- |
| type |字符串 |
| id |integer |
| full_name |字符串 |

## <a name="next-steps"></a>后续步骤
[创建逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)

## <a name="object-definitions"></a>对象定义：
 **WebResultModel**：必应 Web 搜索结果

WebResultModel 的必需属性：

没有一个属性是必需属性。 

**所有属性**： 

| Name | 数据类型 |
| --- | --- |
| 标题 |字符串 |
| 说明 |字符串 |
| DisplayUrl |字符串 |
| ID |字符串 |
| FullUrl |字符串 |

 **VideoResultModel**：必应视频搜索结果

VideoResultModel 的必需属性：

没有一个属性是必需属性。 

**所有属性**： 

| Name | 数据类型 |
| --- | --- |
| 标题 |字符串 |
| DisplayUrl |字符串 |
| ID |字符串 |
| MediaUrl |字符串 |
| 运行时 |integer |
| 缩略图 |未定义 |

 **ThumbnailModel**：多媒体元素的缩略图属性

ThumbnailModel 的必需属性：

没有一个属性是必需属性。 

**所有属性**： 

| Name | 数据类型 |
| --- | --- |
| MediaUrl |字符串 |
| ContentType |字符串 |
| 宽度 |integer |
| 高度 |integer |
| FileSize |integer |

 **ImageResultModel**：必应图像搜索结果

ImageResultModel 的必需属性：

没有一个属性是必需属性。 

**所有属性**： 

| Name | 数据类型 |
| --- | --- |
| 标题 |字符串 |
| DisplayUrl |字符串 |
| ID |字符串 |
| MediaUrl |字符串 |
| SourceUrl |字符串 |
| 缩略图 |未定义 |

 **NewsResultModel**：必应资讯搜索结果

NewsResultModel 的必需属性：

没有一个属性是必需属性。 

**所有属性**： 

| Name | 数据类型 |
| --- | --- |
| 标题 |字符串 |
| 说明 |字符串 |
| DisplayUrl |字符串 |
| ID |字符串 |
| 源 |字符串 |
| 日期 |字符串 |

 **SpellResultModel**：必应拼写建议结果

SpellResultModel 的必需属性：

没有一个属性是必需属性。 

**所有属性**： 

| Name | 数据类型 |
| --- | --- |
| ID |字符串 |
| 值 |字符串 |

 **RelatedSearchResultModel**：必应相关搜索结果

RelatedSearchResultModel 的必需属性：

没有一个属性是必需属性。 

**所有属性**： 

| Name | 数据类型 |
| --- | --- |
| 标题 |字符串 |
| ID |字符串 |
| BingUrl |字符串 |

 **CompositeSearchResultModel**：必应复合搜索结果

CompositeSearchResultModel 的必需属性：

没有一个属性是必需属性。 

**所有属性**： 

| Name | 数据类型 |
| --- | --- |
| WebResultsTotal |integer |
| ImageResultsTotal |integer |
| VideoResultsTotal |integer |
| NewsResultsTotal |integer |
| SpellSuggestionsTotal |integer |
| WebResults |数组 |
| ImageResults |数组 |
| VideoResults |数组 |
| NewsResults |数组 |
| SpellSuggestionResults |数组 |
| RelatedSearchResults |数组 |

## <a name="object-definitions"></a>对象定义：
 **PostOperationResponse**：表示 Slack 连接器发布到 Slack 的发布操作的响应

PostOperationResponse 的必需属性：

没有一个属性是必需属性。 

**所有属性**： 

| Name | 数据类型 |
| --- | --- |
| 确定 |布尔值 |
| channel |字符串 |
| ts |字符串 |
| message |未定义 |
| error |字符串 |

 **MessageItem**：通道消息。

MessageItem 的必需属性：

没有一个属性是必需属性。 

**所有属性**： 

| Name | 数据类型 |
| --- | --- |
| text |字符串 |
| id |字符串 |
| user |字符串 |
| created |integer |
| is_user-deleted |布尔值 |

## <a name="next-steps"></a>后续步骤
[创建逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)

[1]: ./media/connectors-create-api-slack/connectionconfig1.png
[2]: ./media/connectors-create-api-slack/connectionconfig2.png 
[3]: ./media/connectors-create-api-slack/connectionconfig3.png
[4]: ./media/connectors-create-api-slack/connectionconfig4.png
[5]: ./media/connectors-create-api-slack/connectionconfig5.png
[6]: ./media/connectors-create-api-slack/connectionconfig6.png



<!--HONumber=Jan17_HO3-->


