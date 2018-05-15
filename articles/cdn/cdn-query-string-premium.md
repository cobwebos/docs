---
title: 使用查询字符串控制 Azure CDN 缓存行为 - 高级层 | Microsoft Docs
description: Azure CDN 的查询字符串缓存可以控制 Web 请求在包含查询字符串时被缓存的方式。 本指南介绍 Verizon Azure CDN 高级版产品中的查询字符串缓存。
services: cdn
documentationcenter: ''
author: dksimpson
manager: akucer
editor: ''
ms.assetid: 99db4a85-4f5f-431f-ac3a-69e05518c997
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: mazha
ms.openlocfilehash: a1ef5f3e502b5383343dbec2c427d36054a3edc4
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="control-azure-cdn-caching-behavior-with-query-strings---premium-tier"></a>使用查询字符串控制 Azure CDN 缓存行为 - 高级层
> [!div class="op_single_selector"]
> * [标准层](cdn-query-string.md)
> * [高级层](cdn-query-string-premium.md)
> 
> 

## <a name="overview"></a>概述
使用 Azure 内容分发网络 (CDN)，可以控制针对包含查询字符串的 Web 请求缓存文件的方式。 在包含查询字符串的 Web 请求中，查询字符串是问号 (?) 后出现的请求部分。 查询字符串可以包含一个或多个键值对，其中字段名称和其值由等号 (=) 分隔。 每个键值对由与号 (&) 分隔。 例如 http:\//www.contoso.com/content.mov?field1=value1&field2=value2。 如果请求的查询字符串中有多个键值对，其顺序并不重要。 

> [!IMPORTANT]
> 标准和高级 CDN 产品提供相同的查询字符串缓存功能，但用户界面不同。 本文介绍**来自 Verizon 的高级 Azure CDN** 的界面。 有关 Azure CDN 标准产品的查询字符串缓存，请参阅[使用查询字符串控制 Azure CDN 缓存行为 - 标准层](cdn-query-string.md)。
>


可用的三种查询字符串模式如下：

- **标准缓存**：默认模式。 在此模式下，CDN 接入点 (POP) 节点将来自请求者的查询字符串传递到第一个请求上的源服务器并缓存该资产。 所有由 POP 服务器处理的该资产的后续请求将忽略查询字符串，直至缓存的资产到期。

    >[!IMPORTANT] 
    > 如果对此帐户中的任何路径启用了令牌授权，则标准缓存模式是可用的唯一模式。 

- **无缓存**：在此模式下，包含查询字符串的请求不会被缓存在 CDN POP 节点。 POP 节点直接从源服务器检索资产，并将其传递给每个请求的请求者。

- **唯一缓存**：在此模式下，包含唯一 URL 的每个请求（包括查询字符串）将视为具有其自己的缓存的唯一资产。 例如，源服务器对 .ashx?q=test1 的请求做出的响应将缓存在 POP 节点，并为具有同一查询字符串的后续缓存返回该响应。 例如，.ashx?q=test2 的请求将作为具有其自己的生存时间设置的单独资产来缓存。
   
    >[!IMPORTANT] 
    > 如果查询字符串包含随每个请求更改的参数（例如会话 ID 或用户名），请不要使用此模式，因为这会导致缓存命中率降低。

## <a name="changing-query-string-caching-settings-for-premium-cdn-profiles"></a>为高级 CDN 配置文件更改查询字符串缓存设置
1. 打开 CDN 配置文件，单击“管理”。
   
    ![CDN 配置文件管理按钮](./media/cdn-query-string-premium/cdn-manage-btn.png)
   
    CDN 管理门户打开。
2. 将鼠标悬停在“HTTP 大”选项卡上，然后悬停在“缓存设置”浮出菜单上。 单击“查询字符串缓存”。
   
    将显示查询字符串缓存选项。
   
    ![CDN 查询字符串缓存选项](./media/cdn-query-string-premium/cdn-query-string.png)
3. 选择查询字符串模式，单击“更新”。

> [!IMPORTANT]
> 由于注册通过 CDN 传播需要时间，缓存字符串设置更改不会立即显示。 对于 **Verizon 的 Azure CDN 高级版**配置文件，传播通常在 90 分钟内完成。
 

