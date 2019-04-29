---
title: 使用缓存规则控制 Azure CDN 缓存行为 | Microsoft Docs
description: 可以使用 CDN 缓存规则全局性地和根据条件（例如 URL 路径和文件扩展名）设置或修改默认的缓存过期行为。
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: magattus
ms.openlocfilehash: 3a94b8252feb7c5c345d678579c477fce02d6e03
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60679145"
---
# <a name="control-azure-cdn-caching-behavior-with-caching-rules"></a>使用缓存规则控制 Azure CDN 缓存行为

> [!NOTE] 
> 缓存规则仅适用于 **Verizon 提供的标准 Azure CDN** 和 **Akamai 提供的标准 Azure CDN** 配置文件。 对于 **Verizon 提供的高级 Azure CDN** 配置文件，必须在**管理**门户中使用 [Azure CDN 规则引擎](cdn-rules-engine.md)来获得类似的功能。
 
Azure 内容分发网络 (CDN) 提供了两种方法来控制文件的缓存方式： 

- 缓存规则：本文介绍如何使用内容分发网络 (CDN) 缓存规则设置或修改默认的缓存过期行为，全局和自定义条件，例如 URL 路径和文件扩展名。 Azure CDN 提供两种类型的缓存规则：

   - 全局缓存规则：可以在配置文件，这会影响到的终结点的所有请求中设置每个终结点的一个全局缓存的规则。 全局缓存规则会替代所有 HTTP 缓存指令标头（如果已设置）。

   - 自定义缓存规则：在配置文件中，可以设置每个终结点的一个或多个条自定义缓存规则。 自定义缓存规则将匹配特定的路径和文件扩展名，按顺序进行处理，并替代全局缓存规则（如果已设置）。 

- 查询字符串缓存：您可以调整 Azure CDN 如何处理的请求的查询字符串缓存。 有关信息，请参阅[使用查询字符串控制 Azure CDN 缓存行为](cdn-query-string.md)。 如果文件不可缓存，则根据缓存规则和 CDN 默认行为，查询字符串缓存设置不起作用。

有关默认缓存行为和缓存指令标头的信息，请参阅[缓存工作原理](cdn-how-caching-works.md)。 


## <a name="accessing-azure-cdn-caching-rules"></a>访问 Azure CDN 缓存规则

1. 打开 Azure 门户，依次选择一个 CDN 配置文件和一个终结点。

2. 在左窗格中的“设置”下，选择“缓存规则”。

   ![CDN 缓存规则按钮](./media/cdn-caching-rules/cdn-caching-rules-btn.png)

   “缓存规则”页随即出现。

   ![CDN“缓存规则”页](./media/cdn-caching-rules/cdn-caching-rules-page.png)


## <a name="caching-behavior-settings"></a>缓存行为设置
对于全局和自定义缓存规则，可指定以下“缓存行为”设置：

- **绕过缓存**:不缓存，并忽略源提供的缓存指令标头。

- **重写**:忽略源提供的缓存持续时间;改为使用提供的缓存持续时间。 这不会重写缓存控制： 无缓存。

- **缺少时设置**:遵循源提供的缓存指令标头，如果它们存在;否则，请使用提供的缓存持续时间。

![全局缓存规则](./media/cdn-caching-rules/cdn-global-caching-rules.png)

![自定义缓存规则](./media/cdn-caching-rules/cdn-custom-caching-rules.png)

## <a name="cache-expiration-duration"></a>缓存过期持续时间
对于全局和自定义缓存规则，可以使用天、小时、分钟和秒作为单位指定缓存过期持续时间：

- 对于“缓存行为”设置“替代”和“缺少时设置”，有效的缓存持续时间范围为 0 秒到 366 天。 如果值为 0 秒，则 CDN 会缓存内容，但必须在源服务器上重新验证每个请求。

- 对于“绕过缓存”设置，缓存持续时间将自动设置为 0 秒且不可更改。

## <a name="custom-caching-rules-match-conditions"></a>自定义缓存规则的匹配条件

对于自定义缓存规则，可以使用两个匹配条件：
 
- **路径**：此条件匹配的路径的 URL，不包括域名，并支持通配符 (\*)。 例如，_/myfile.html_、_/my/folder/*_ 和 _/my/images/*.jpg_。 最大长度为 260 个字符。

- **扩展**:此条件匹配所请求的文件的文件扩展名。 可以提供要匹配的文件扩展名的逗号分隔列表。 例如，_.jpg_、_.mp3_ 或 _.png_。 扩展名数目上限为 50 个，每个扩展名的字符数上限为 16 个。 

## <a name="global-and-custom-rule-processing-order"></a>全局和自定义规则的处理顺序
全局和自定义缓存规则的处理顺序如下：

- 全局缓存规则优先于默认的 CDN 缓存行为（HTTP 缓存指令标头设置）。 

- 自定义缓存规则（如果适用）优先于全局缓存规则。 自定义缓存规则按从上到下的顺序进行处理。 也就是说，如果某个请求与两个条件匹配，则位于列表底部的规则优先于列表顶部的规则。 因此，应将更具体的规则放在列表的较低位置。

**示例**：
- 全局缓存规则： 
   - 缓存行为：**Override**
   - 缓存过期持续时间：1 天

- 自定义缓存规则 #1：
   - 匹配条件：**路径**
   - 匹配值：_/home/*_
   - 缓存行为：**Override**
   - 缓存过期持续时间：2 天

- 自定义缓存规则 #2：
   - 匹配条件：**扩展**
   - 匹配值：_.html_
   - 缓存行为：**缺少时设置**
   - 缓存过期持续时间：3 天

当设置这些规则，为请求_&lt;终结点主机名&gt;_.azureedge.net/home/index.html 触发器自定义缓存规则 #2 中，它设置为：**缺少时设置**和 3 天。 因此，如果 *index.html* 文件包含 `Cache-Control` 或 `Expires` HTTP 标头，则会遵循这些标头；如果未设置这些标头，则会将文件缓存 3 天。

> [!NOTE] 
> 在发生规则更改之前缓存的文件会保留其源缓存持续时间设置。 若要重置其缓存持续时间，必须[清除文件](cdn-purge-endpoint.md)。 
>
> Azure CDN 配置更改可能需要一段时间，才能在整个网络中传播： 
> - 对于 **Akamai 的 Azure CDN 标准版**配置文件，传播通常可在一分钟内完成。 
> - 对于“Verizon 提供的 Azure CDN 标准版”配置文件，传播通常在 10 分钟内完成。  
>

## <a name="see-also"></a>另请参阅

- [缓存工作原理](cdn-how-caching-works.md)
- [教程：设置 Azure CDN 缓存规则](cdn-caching-rules-tutorial.md)
