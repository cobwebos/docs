---
title: "使用查询字符串控制 Azure 内容交付网络缓存行为 | Microsoft Docs"
description: "Azure CDN 的查询字符串缓存可以控制文件在包含查询字符串时被缓存的方式。"
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 17410e4f-130e-489c-834e-7ca6d6f9778d
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: mazha
ms.openlocfilehash: 9ffd05a0eb4d976dc40a1c5d45fd22ebf9bd4db1
ms.sourcegitcommit: 4ac89872f4c86c612a71eb7ec30b755e7df89722
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/07/2017
---
# <a name="control-azure-content-delivery-network-caching-behavior-with-query-strings"></a>使用查询字符串控制 Azure 内容交付网络缓存行为
> [!div class="op_single_selector"]
> * [标准](cdn-query-string.md)
> * [来自 Verizon 的高级 Azure CDN](cdn-query-string-premium.md)
> 

## <a name="overview"></a>概述
使用 Azure 内容交付网络 (CDN)，可以控制针对包含查询字符串的 Web 请求缓存文件的方式。 在包含查询字符串的 Web 请求中，查询字符串是问号 (?) 后出现的请求部分。 查询字符串可以包含一个或多个键值对，其中字段名称和其值由等号 (=) 分隔。 每个键值对由与号 (&) 分隔。 例如，`http://www.contoso.com/content.mov?field1=value1&field2=value2`。 如果请求的查询字符串中有多个键值对，其顺序并不重要。 

> [!IMPORTANT]
> 标准和高级 CDN 产品提供相同的查询字符串缓存功能，但用户界面不同。  本文介绍**来自 Akamai 的标准 Azure CDN0** 和**来自 Verizon 的标准 Azure CDN** 的界面。 关于**来自 Verizon 的标准 Azure CDN** 的查询字符串，请参阅[控制含查询字符串的 CDN 请求的缓存行为 - 高级](cdn-query-string-premium.md)。

可用的三种查询字符串模式如下：

- **忽略查询字符串**：默认模式。 在此模式下，CDN 边缘节点将来自请求者的查询字符串传递到第一个请求上的源并缓存该资产。 所有由边缘节点处理的该资产的后续请求将忽略查询字符串，直至缓存的资产到期。
- **绕过查询字符串的缓存**：在此模式下，包含查询字符串的请求不会被缓存在 CDN 边缘节点。 边缘节点直接从源检索资产，并将其传递给每个请求的请求者。
- **缓存每个唯一的 URL**：在此模式下，包含唯一 URL 的每个请求（包括查询字符串）将视为具有其自己的缓存的唯一资产。 例如，源对 `example.ashx?q=test1` 的请求做出的响应将缓存在边缘节点，并为具有同一查询字符串的后续缓存返回该响应。 `example.ashx?q=test2` 的请求将作为具有其自己的生存时间设置的单独资产来缓存。

## <a name="changing-query-string-caching-settings-for-standard-cdn-profiles"></a>为标准 CDN 配置文件更改查询字符串缓存设置
1. 打开 CDN 配置文件，然后选择要管理的 CDN 终结点。
   
   ![CDN 配置文件终结点](./media/cdn-query-string/cdn-endpoints.png)
   
2. 在左窗格中的“设置”下面，单击“缓存规则”。
   
    ![CDN 缓存规则按钮](./media/cdn-query-string/cdn-caching-rules-btn.png)
   
3. 在“查询字符串缓存行为”列表中，选择查询字符串模式，然后单击“保存”。
   
   ![CDN 查询字符串缓存选项](./media/cdn-query-string/cdn-query-string.png)

> [!IMPORTANT]
> 由于注册通过 CDN 传播需要时间，缓存字符串设置更改不会立即显示。 对于**来自 Akamai 的 Azure CDN** 配置文件，传播通常可在一分钟内完成。 对于**来自 Verizon 的 Azure CDN** 配置文件，传播通常在 90 分钟内完成，但某些情况下可能更长。


