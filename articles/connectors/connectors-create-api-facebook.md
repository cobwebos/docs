---
title: "在逻辑应用中添加 Facebook 连接器 | Microsoft Docs"
description: "使用 REST API 参数的 Facebook 连接器概述"
services: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: f4d6f0ed-c09b-488c-be1c-8cf2b5b1d4b8
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: b8a66308c4f4f1df610cdacd092ef133bd605665


---
# <a name="get-started-with-the-facebook-connector"></a>Facebook 连接器入门
连接到 Facebook，发布到时间线、获取页面源等。 

> [!NOTE]
> 此文章版本适用于 2015 年 8 月 1 日预览版架构的逻辑应用。
> 
> 

通过 Facebook，你可以：

* 根据从 Facebook 中获取的数据生成你的业务流。 
* 在收到新发布时使用触发器。
* 使用发布到时间线、获取页面源等操作。 这些操作可获得响应，然后使输出可用于其他操作。 例如，当时间线上有新发布时，你可以获取该发布并将其推送到 Twitter 源。 

若要在逻辑应用中添加操作，请参阅[创建逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)。

## <a name="triggers-and-actions"></a>触发器和操作
Facebook 连接器包括以下触发器和操作。 

| 触发器 | 操作 |
| --- | --- |
| <ul><li>当我的时间线上有新发布时</li></ul> |<ul><li>从我的时间线获取源</li><li>发布到我的时间线</li><li>当我的时间线上有新发布时</li><li>获取页面源</li><li>获取用户时间线</li><li>发送到页面</li></ul> |

所有连接器都支持采用 JSON 和 XML 格式的数据。

## <a name="create-a-connection-to-facebook"></a>创建到 Facebook 的连接
将此连接器添加到逻辑应用时，必须授权逻辑应用连接到你的 Facebook。

1. 登录到 Facebook 帐户
2. 选择“授权”，允许你的逻辑应用连接和使用 Facebook。 

> [!INCLUDE [Steps to create a connection to Facebook](../../includes/connectors-create-api-facebook.md)]
> 
> [!TIP]
> 可在其他逻辑应用中使用此相同 Facebook 连接。
> 
> 

## <a name="swagger-rest-api-reference"></a>Swagger REST API 参考
适用于版本：1.0。

### <a name="get-feed-from-my-timeline"></a>从我的时间线获取源
从已登录用户的时间线获取源。  
```GET: /me/feed```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| fields |字符串 |否 |查询 |无 |指定想要返回的字段。 示例（ID、名称、图片）。 |
| limit |integer |否 |查询 |无 |要检索的最大发布数 |
| 替换为 |字符串 |否 |查询 |无 |将发布列表限制到仅附加位置的发布。 |
| filter |字符串 |否 |查询 |无 |仅检索与特定流筛选器匹配的发布。 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| 400 |错误的请求 |
| 500 |内部服务器错误 |
| default |操作失败。 |

### <a name="post-to-my-timeline"></a>发布到我的时间线
将状态消息发布到已登录用户的时间线。  
```POST: /me/feed```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| post |字符串 |是 |body |无 |要发布的新消息 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| 400 |错误的请求 |
| 500 |内部服务器错误 |
| default |操作失败。 |

### <a name="when-there-is-a-new-post-on-my-timeline"></a>当我的时间线上有新发布时
当已登录用户的时间线上有新发布时，触发新流。  
```GET: /trigger/me/feed```

没有任何参数。 

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| 400 |错误的请求 |
| 500 |内部服务器错误 |
| default |操作失败。 |

### <a name="get-page-feed"></a>获取页面源
从指定页面的源获取发布。  
```GET: /{pageId}/feed```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| pageId |字符串 |是 |路径 |无 |要从中检索发布的页面的 ID。 |
| limit |integer |否 |查询 |无 |要检索的最大发布数 |
| include_hidden |布尔值 |否 |查询 |无 |是否要包括页面隐藏的任何发布 |
| fields |字符串 |否 |查询 |无 |指定想要返回的字段。 示例（ID、名称、图片）。 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| 400 |错误的请求 |
| 500 |内部服务器错误 |
| default |操作失败。 |

### <a name="get-user-timeline"></a>获取用户时间线
从用户的时间线获取发布。  
```GET: /{userId}/feed```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| userId |字符串 |是 |路径 |无 |要检索其时间线的用户的 ID。 |
| limit |integer |否 |查询 |无 |要检索的最大发布数 |
| 替换为 |字符串 |否 |查询 |无 |将发布列表限制到仅附加位置的发布。 |
| filter |字符串 |否 |查询 |无 |仅检索与特定流筛选器匹配的发布。 |
| fields |字符串 |否 |查询 |无 |指定想要返回的字段。 示例（ID、名称、图片）。 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| 400 |错误的请求 |
| 500 |内部服务器错误 |
| default |操作失败。 |

### <a name="post-to-page"></a>发送到页面
以已登录用户的身份将消息发布到 Facebook 页面。  
```POST: /{pageId}/feed```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| pageId |字符串 |是 |路径 |无 |要发布的页面的 ID。 |
| post |很多 |是 |body |无 |要发布的新消息。 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| 400 |错误的请求 |
| 500 |内部服务器错误 |
| default |操作失败。 |

## <a name="object-definitions"></a>对象定义
#### <a name="getfeedresponse"></a>GetFeedResponse
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| 数据 |数组 |否 |

#### <a name="triggerfeedresponse"></a>TriggerFeedResponse
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| 数据 |数组 |否 |

#### <a name="postitem-a-single-entry-in-a-profiles-feed"></a>PostItem：配置文件的源中的单个项
该配置文件可能是用户、页面、应用或组。 

| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| id |字符串 |否 |
| admin_creator |数组 |否 |
| caption |字符串 |否 |
| created_time |字符串 |否 |
| description |字符串 |否 |
| feed_targeting |未定义 |否 |
| from |未定义 |否 |
| icon |字符串 |否 |
| is_hidden |布尔值 |否 |
| is_published |布尔值 |否 |
| 链接 |字符串 |否 |
| message |字符串 |否 |
| 名称 |字符串 |否 |
| object_id |字符串 |否 |
| picture |字符串 |否 |
| place |未定义 |否 |
| privacy |未定义 |否 |
| properties |数组 |否 |
| 源 |字符串 |否 |
| status_type |字符串 |否 |
| story |字符串 |否 |
| targeting |未定义 |否 |
| to |数组 |否 |
| type |字符串 |否 |
| updated_time |字符串 |否 |
| with_tags |未定义 |否 |

#### <a name="triggeritem-a-single-entry-in-a-profiles-feed"></a>TriggerItem：配置文件的源中的单个项
该配置文件可能是用户、页面、应用或组。

| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| id |字符串 |否 |
| created_time |字符串 |否 |
| from |未定义 |否 |
| message |字符串 |否 |
| type |字符串 |否 |

#### <a name="adminitem"></a>AdminItem
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| id |字符串 |否 |
| 链接 |字符串 |否 |

#### <a name="propertyitem"></a>PropertyItem
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| 名称 |字符串 |否 |
| text |字符串 |否 |
| href |字符串 |否 |

#### <a name="userpostfeedrequest"></a>UserPostFeedRequest
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| message |字符串 |是 |
| 链接 |字符串 |否 |
| picture |字符串 |否 |
| 名称 |字符串 |否 |
| caption |字符串 |否 |
| description |字符串 |否 |
| place |字符串 |否 |
| 标记 |字符串 |否 |
| privacy |未定义 |否 |
| object_attachment |字符串 |否 |

#### <a name="pagepostfeedrequest"></a>PagePostFeedRequest
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| message |字符串 |是 |
| 链接 |字符串 |否 |
| picture |字符串 |否 |
| 名称 |字符串 |否 |
| caption |字符串 |否 |
| description |字符串 |否 |
| actions |数组 |否 |
| place |字符串 |否 |
| 标记 |字符串 |否 |
| object_attachment |字符串 |否 |
| targeting |未定义 |否 |
| feed_targeting |未定义 |否 |
| published |布尔值 |否 |
| scheduled_publish_time |字符串 |否 |
| backdated_time |字符串 |否 |
| backdated_time_granularity |字符串 |否 |
| child_attachments |数组 |否 |
| multi_share_end_card |布尔值 |否 |

#### <a name="postfeedresponse"></a>PostFeedResponse
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| id |字符串 |否 |

#### <a name="profilecollection"></a>ProfileCollection
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| 数据 |数组 |否 |

#### <a name="useritem"></a>UserItem
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| id |字符串 |否 |
| first_name |字符串 |否 |
| last_name |字符串 |否 |
| 名称 |字符串 |否 |
| gender |字符串 |否 |
| about |字符串 |否 |

#### <a name="actionitem"></a>ActionItem
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| 名称 |字符串 |否 |
| 链接 |字符串 |否 |

#### <a name="targetitem"></a>TargetItem
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| countries |数组 |否 |
| locales |数组 |否 |
| regions |数组 |否 |
| cities |数组 |否 |

#### <a name="feedtargetitem-object-that-controls-news-feed-targeting-for-this-post"></a>FeedTargetItem：控制面向此发布的新闻源的对象
这些组中的所有人都有可能看到此发布，其他人则不太可能看到。 仅适用于页面。

| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| countries |数组 |否 |
| regions |数组 |否 |
| cities |数组 |否 |
| age_min |integer |否 |
| age_max |integer |否 |
| genders |数组 |否 |
| relationship_statuses |数组 |否 |
| interested_in |数组 |否 |
| college_years |数组 |否 |
| interests |数组 |否 |
| relevant_until |integer |否 |
| education_statuses |数组 |否 |
| locales |数组 |否 |

#### <a name="placeitem"></a>PlaceItem
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| id |字符串 |否 |
| 名称 |字符串 |否 |
| overall_rating |数字 |否 |
| location |未定义 |否 |

#### <a name="locationitem"></a>LocationItem
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| city |字符串 |否 |
| country |字符串 |否 |
| latitude |数字 |否 |
| located_in |字符串 |否 |
| longitude |数字 |否 |
| 名称 |字符串 |否 |
| region |字符串 |否 |
| state |字符串 |否 |
| street |字符串 |否 |
| zip |字符串 |否 |

#### <a name="privacyitem"></a>PrivacyItem
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| description |字符串 |否 |
| value |字符串 |是 |
| allow |字符串 |否 |
| deny |字符串 |否 |
| friends |字符串 |否 |

#### <a name="childattachmentsitem"></a>ChildAttachmentsItem
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| 链接 |字符串 |否 |
| picture |字符串 |否 |
| image_hash |字符串 |否 |
| 名称 |字符串 |否 |
| description |字符串 |否 |

#### <a name="postphotorequest"></a>PostPhotoRequest
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| url |字符串 |是 |
| caption |字符串 |否 |

#### <a name="postphotoresponse"></a>PostPhotoResponse
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| id |字符串 |是 |
| post_id |字符串 |是 |

#### <a name="postvideorequest"></a>PostVideoRequest
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| videoData |字符串 |是 |
| description |字符串 |是 |
| title |字符串 |是 |
| uploadedVideoName |字符串 |否 |

#### <a name="getphotoresponse"></a>GetPhotoResponse
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| 数据 |未定义 |是 |

#### <a name="getphotoresponseitem"></a>GetPhotoResponseItem
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| url |字符串 |是 |
| is_silhouette |布尔值 |是 |
| height |字符串 |否 |
| width |字符串 |否 |

#### <a name="geteventresponse"></a>GetEventResponse
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| 数据 |数组 |是 |

#### <a name="geteventresponseitem"></a>GetEventResponseItem
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| id |字符串 |是 |
| 名称 |字符串 |是 |
| start_time |字符串 |否 |
| end_time |字符串 |否 |
| timezone |字符串 |否 |
| location |字符串 |否 |
| description |字符串 |否 |
| ticket_uri |字符串 |否 |
| rsvp_status |字符串 |是 |

## <a name="next-steps"></a>后续步骤
[创建逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)。




<!--HONumber=Jan17_HO3-->


