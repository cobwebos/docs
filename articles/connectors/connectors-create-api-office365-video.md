---
title: "在逻辑应用中使用 Office 365 视频连接器 | Microsoft Docs"
description: "在 Microsoft Azure App Service 逻辑应用中开始使用 Office 365 视频连接器"
services: 
documentationcenter: 
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: 738e5aa7-2523-4116-8b65-211b9063852d
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 959eaca7aafd8516cfc6f3b5dd2a44ce21ec4825


---
# <a name="get-started-with-the-office365-video-connector"></a>Office 365 视频连接器入门
连接到 Office 365 视频，获取有关 Office 365 视频的信息、获取视频列表等。 可以从以下位置使用 Office 365 视频连接器：

* 逻辑应用 

> [!NOTE]
> 此文章版本适用于 2015 年 8 月 1 日预览版架构的逻辑应用。 任何以前的架构版本都不支持此连接器。
> 
> 

通过 Office 365 视频，你可以：

* 根据从 Office 365 视频中获取的数据生成你的业务流。 
* 使用检查视频门户状态、获取频道中所有视频列表等操作。 这些操作可获得响应，然后使输出可用于其他操作。 例如，可以使用必应搜索连接器搜索 Office 365 视频，然后使用 Office 365 视频连接器获取该视频的相关信息。 如果视频满足你的要求，可在 Facebook 上发布此视频。 

若要在逻辑应用中添加操作，请参阅[创建逻辑应用](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## <a name="triggers-and-actions"></a>触发器和操作
Office 365 视频连接器具有以下可用操作。 没有任何触发器。

| 触发器 | 操作 |
| --- | --- |
| 无 |<ul><li>检查视频门户状态</li><li>获取所有可观看频道</li><li>获取视频的 Azure 媒体服务清单的播放 URL</li><li>获取持有者令牌，获取解密视频的访问权限</li><li>获取有关特定 Office 365 视频的信息</li><li>列出频道中存在的所有 Office 365 视频</li></ul> |

所有连接器都支持采用 JSON 和 XML 格式的数据。 

## <a name="create-a-connection-to-office365-video-connector"></a>创建到 Office 365 视频连接器的连接
将此连接器添加到逻辑应用时，必须登录到 Office 365 视频帐户，并允许逻辑应用连接到你的帐户。

> [!INCLUDE [Steps to create a connection to Office 365 Video](../../includes/connectors-create-api-office365video.md)]
> 
> 

创建连接后，输入 Office 365 视频属性，如租户名称或频道 ID。 本主题中的 **REST API 参考**介绍了这些属性。

> [!TIP]
> 可在其他逻辑应用中使用此相同 Office 365 视频连接。
> 
> 

## <a name="swagger-rest-api-reference"></a>Swagger REST API 参考
适用于版本：1.0。

### <a name="checks-video-portal-status"></a>检查视频门户状态
检查视频门户状态，查看视频服务是否处于启用状态。  
```GET: /{tenant}/IsEnabled``` 

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| tenant |字符串 |是 |路径 |无 |用户所属目录的租户名称 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |操作成功 |
| 400 |BadRequest |
| 401 |未授权 |
| 404 |未找到 |
| 500 |内部服务器错误 |
| default |操作失败。 |

### <a name="get-all-viewable-channels"></a>获取所有可观看频道
获取用户有权查看的所有频道。  
```GET: /{tenant}/Channels``` 

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| tenant |字符串 |是 |路径 |无 |用户所属目录的租户名称 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |操作成功 |
| 400 |BadRequest |
| 401 |未授权 |
| 404 |未找到 |
| 500 |内部服务器错误 |
| default |操作失败。 |

### <a name="lists-all-the-office365-videos-present-in-a-channel"></a>列出频道中存在的所有 Office 365 视频
列出频道中存在的所有 Office 365 视频。  
```GET: /{tenant}/Channels/{channelId}/Videos``` 

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| tenant |字符串 |是 |路径 |无 |用户所属目录的租户名称 |
| channelId |字符串 |是 |路径 |无 |需要从中提取视频的频道 ID |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |操作成功 |
| 400 |BadRequest |
| 401 |未授权 |
| 404 |未找到 |
| 500 |内部服务器错误 |
| default |操作失败。 |

### <a name="gets-information-about-a-particular-office365-video"></a>获取有关特定 Office 365 视频的信息
获取有关特定 Office 365 视频的信息。  
```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}``` 

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| tenant |字符串 |是 |路径 |无 |用户所属目录的租户名称 |
| channelId |字符串 |是 |路径 |无 |频道 ID |
| videoId |字符串 |是 |路径 |无 |视频 ID |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |操作成功 |
| 400 |BadRequest |
| 401 |未授权 |
| 404 |未找到 |
| 500 |内部服务器错误 |
| default |操作失败。 |

### <a name="get-playback-url-of-the-azure-media-services-manifest-for-a-video"></a>获取视频的 Azure 媒体服务清单的播放 URL
获取视频的 Azure 媒体服务清单的播放 URL。  
```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}/playbackurl``` 

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| tenant |字符串 |是 |路径 |无 |用户所属目录的租户名称 |
| channelId |字符串 |是 |路径 |无 |频道 ID |
| videoId |字符串 |是 |路径 |无 |视频 ID |
| streamingFormatType |字符串 |是 |查询 |无 |流式处理格式类型。 1 - 平滑流式处理或 MPEG-DASH。 0 - HLS 流式处理 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |操作成功 |
| 400 |BadRequest |
| 401 |未授权 |
| 404 |未找到 |
| 500 |内部服务器错误 |
| default |操作失败。 |

### <a name="get-the-bearer-token-to-get-access-to-decrypt-the-video"></a>获取持有者令牌，获取解密视频的访问权限
获取持有者令牌，获取解密视频的访问权限。  
```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}/token```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| tenant |字符串 |是 |路径 |无 |用户所属目录的租户名称 |
| channelId |字符串 |是 |路径 |无 |频道 ID |
| videoId |字符串 |是 |路径 |无 |视频 ID |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |操作成功 |
| 400 |BadRequest |
| 401 |未授权 |
| 404 |未找到 |
| 500 |内部服务器错误 |
| default |操作失败。 |

## <a name="object-definitions"></a>对象定义
#### <a name="channel-channel-class"></a>Channel：频道类
| Name | 数据类型 | 必选 |
| --- | --- | --- |
| ID |字符串 |否 |
| 标题 |字符串 |否 |
| 说明 |字符串 |否 |

#### <a name="video"></a>视频
| Name | 数据类型 | 必选 |
| --- | --- | --- |
| ID |字符串 |否 |
| 标题 |字符串 |否 |
| 说明 |字符串 |否 |
| CreationDate |字符串 |否 |
| 所有者 |字符串 |否 |
| ThumbnailUrl |字符串 |否 |
| VideoUrl |字符串 |否 |
| VideoDuration |integer |否 |
| VideoProcessingStatus |integer |否 |
| ViewCount |integer |否 |

## <a name="next-steps"></a>后续步骤
[创建逻辑应用](../app-service-logic/app-service-logic-create-a-logic-app.md)。




<!--HONumber=Nov16_HO3-->


