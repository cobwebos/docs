---
title: RSS | Microsoft Docs
description: "使用 Azure App Service 创建逻辑应用。 RSS 连接器允许用户发布和检索摘要项。 它还允许用户在将新项发布到摘要时触发操作。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: a10a6277-ed29-4e68-a881-ccdad6fd0ad8
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: 5e13e126fecda66a453b4ced619016121af98b2c


---
# <a name="get-started-with-the-rss-connector"></a>RSS 连接器入门
RSS 是一种流行的 Web 联合格式，用于发布频繁更新的内容，如博客文章和新闻标题。  许多内容发布者提供 RSS 源，允许用户订阅它。  当 RSS 源中发布新项时，使用 RSS 连接器检索源信息并触发流。

> [!NOTE]
> 此文章版本适用于 2015 年 8 月 1 日预览版架构的逻辑应用。 
> 
> 

若要立即开始创建逻辑应用，请参阅[创建逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)。

## <a name="triggers-and-actions"></a>触发器和操作
RSS 连接器可用作操作；它具有触发器。 所有连接器都支持采用 JSON 和 XML 格式的数据。 

 RSS 连接器具有以下可用操作和/或触发器：

### <a name="rss-actions"></a>RSS 操作
可执行以下操作：

| 操作 | 说明 |
| --- | --- |
| [ListFeedItems](connectors-create-api-rss.md#listfeeditems) |获取所有 RSS 源项。 |

### <a name="rss-triggers"></a>RSS 触发器
可侦听以下事件：

| 触发器 | 说明 |
| --- | --- |
| 新源项发布时 |发布新源时，触发工作流 |

## <a name="create-a-connection-to-rss"></a>创建到 RSS 的连接
> [!INCLUDE [Steps to create a connection to an RSS feed](../../includes/connectors-create-api-rss.md)]
> 
> [!TIP]
> 可在其他逻辑应用中使用此连接。
> 
> 

## <a name="reference-for-rss"></a>RSS 的参考
适用于版本：1.0

## <a name="onnewfeed"></a>OnNewFeed
新源项发布时：发布新源时，触发工作流 

```GET: /OnNewFeed``` 

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| feedUrl |字符串 |是 |查询 |无 |Feed url |

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

## <a name="listfeeditems"></a>ListFeedItems
列出所有 RSS 源项：获取所有 RSS 源项。 

```GET: /ListFeedItems``` 

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| feedUrl |字符串 |是 |查询 |无 |Feed url |

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
### <a name="triggerbatchresponsefeeditem"></a>TriggerBatchResponse[FeedItem]
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| value |数组 |否 |

### <a name="feeditem"></a>FeedItem
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| id |字符串 |是 |
| title |字符串 |是 |
| 内容 |字符串 |是 |
| links |数组 |否 |
| updatedOn |字符串 |否 |

## <a name="next-steps"></a>后续步骤
[创建逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)




<!--HONumber=Jan17_HO3-->


