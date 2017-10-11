---
title: "使用查询字符串控制 Azure CDN 缓存行为 - 高级 | Microsoft Docs"
description: "Azure CDN 的查询字符串缓存可以控制在文件包含查询字符串时被缓存的方式。"
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 99db4a85-4f5f-431f-ac3a-69e05518c997
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 145067c2ce50b41c4783f4de4052c0e7cb529fc7
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="control-azure-cdn-caching-behavior-with-query-strings---premium"></a>使用查询字符串控制 Azure CDN 缓存行为 - 高级
> [!div class="op_single_selector"]
> * [标准](cdn-query-string.md)
> * [来自 Verizon 的高级 Azure CDN](cdn-query-string-premium.md)
> 
> 

## <a name="overview"></a>概述
查询字符串缓存可以控制在文件包含查询字符串时被缓存的方式。

> [!IMPORTANT]
> 标准和高级 CDN 产品提供相同的查询字符串缓存功能，但用户界面不同。  本文档描述**来自 Verizon 的高级 Azure CDN** 的界面。  关于**来自 Akamai 的标准 Azure CDN** 和**来自 Verizon 的标准 Azure CDN**，请参阅[控制含查询字符串的 CDN 请求的缓存行为](cdn-query-string.md)。
> 
> 

有以下三种模式可用：

* **标准缓存**：这是默认模式。  CDN 边缘节点将来自请求者的查询字符串传递到第一个请求上的源并缓存该资产。  所有由边缘节点支持的该资产的后续请求将忽略查询字符串，直至缓存的资产到期。
* **无缓存**：在此模式下，包含查询字符串的请求不会被缓存在 CDN 边缘节点。  边缘节点直接从源检索资产，并将其传递给每个请求的请求者。
* **唯一缓存**：此模式将具有查询字符串的每个请求视为具有其自己的缓存的唯一资产。  例如，来自于源对 *foo.ashx?q=bar* 的请求做出的响应将缓存在边缘节点，并为具有该查询字符串的后续缓存返回该响应。  对 *foo.ashx?q=somethingelse* 做出的请求将作为具有其自己的生存时间的单独资产来缓存。

## <a name="changing-query-string-caching-settings-for-premium-cdn-profiles"></a>为高级 CDN 配置文件更改查询字符串缓存设置
1. 从 CDN 配置文件的边栏选项卡，单击“**管理**”按钮。
   
    ![CDN 配置文件边栏选项卡管理按钮](./media/cdn-query-string-premium/cdn-manage-btn.png)
   
    随即 CDN 管理门户打开。
2. 将鼠标悬停在“**HTTP 大**”选项卡上，然后悬停在“**缓存设置**”浮出控件。  单击“**查询字符串缓存**”。
   
    将显示查询字符串缓存选项。
   
    ![CDN 查询字符串缓存选项](./media/cdn-query-string-premium/cdn-query-string.png)
3. 完成选择后，单击“**更新**”按钮。

> [!IMPORTANT]
> 设置更改不会立即显示，因为注册通过 CDN 传播需要时间。  对于<b>来自 Verizon 的 Azure CDN</b> 配置文件，传播通常会在 90 分钟内完成，但某些情况下可能更长。
> 
> 

