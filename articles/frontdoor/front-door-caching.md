---
title: Azure 前端-缓存 |Microsoft Docs
description: 本文将帮助你了解具有已启用缓存的路由规则的前端的行为。
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/29/2020
ms.author: duau
ms.openlocfilehash: 1a8064c3ff89c0bc8b0ceb5249492b912c219ce8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91535825"
---
# <a name="caching-with-azure-front-door"></a>用 Azure 前门进行缓存
以下文档使用已启用缓存的路由规则指定前门的行为。 前门是 (CDN) 的新式内容交付网络，具有动态站点加速和负载平衡，它还支持与任何其他 CDN 一样的缓存行为。

## <a name="delivery-of-large-files"></a>大型文件交付
Azure 前门提供大文件，没有文件大小上限。 Front Door 使用一种被称作对象区块的技术。 请求大型文件时，Front Door 会从后端检索文件的较小部分。 接收到完全或字节范围的文件请求后，前门环境将从后端以 8 MB 的块请求该文件。

</br>区块到达前门环境后，将缓存并立即为用户提供服务。 然后，Front Door 会并行预提取下一个区块。 此预提取可确保内容始终先于用户一个区块，从而减少延迟。 此过程将继续，直到) 请求下载整个文件 (或客户端关闭连接。

</br>有关字节范围请求的详细信息，请阅读 [RFC 7233](https://web.archive.org/web/20171009165003/http://www.rfc-base.org/rfc-7233.html)。
前门会缓存接收的任何区块，因此不需要在前端缓存中缓存整个文件。 对文件或字节范围的后续请求是从缓存中提供的。 如果块并非全部缓存，预提取用于从后端请求区块。 此优化依赖于后端支持字节范围请求的功能。 如果后端不支持字节范围请求，则此优化不起作用。

## <a name="file-compression"></a>文件压缩
前门可以动态地在边缘上压缩内容，导致更小、更快的客户端响应时间。 所有文件都可进行压缩。 但是，文件必须是 MIME 类型才能压缩。 目前，前门不允许更改此列表。 当前列表为：</br>
- "application/eot"
- "application/font"
- "application/font-sfnt"
- "application/javascript"
- "application/json"
- "application/opentype"
- "application/otf"
- "application/pkcs7-mime"
- "application/truetype"
- "application/ttf",
- "application/vnd.ms-fontobject"
- "application/xhtml+xml"
- "application/xml"
- "application/xml+rss"
- "application/x-font-opentype"
- "application/x-font-truetype"
- "application/x-font-ttf"
- "application/x-httpd-cgi"
- "application/x-mpegurl"
- "application/x-opentype"
- "application/x-otf"
- "application/x-perl"
- "application/x-ttf"
- "application/x-javascript"
- "font/eot"
- "font/ttf"
- "font/otf"
- "font/opentype"
- "image/svg+xml"
- "text/css"
- "text/csv"
- "text/html"
- "text/javascript"
- "text/js", "text/plain"
- "text/richtext"
- "text/tab-separated-values"
- "text/xml"
- "text/x-script"
- "text/x-component"
- "text/x-java-source"

此外，文件大小必须介于 1 KB 与 8 MB 之间（不含首尾）。

这些配置文件支持以下压缩编码：
- [Gzip (GNU zip) ](https://en.wikipedia.org/wiki/Gzip)
- [Brotli](https://en.wikipedia.org/wiki/Brotli)

如果请求同时支持 gzip 和 Brotli 压缩，则 Brotli 压缩优先。</br>
当对资产的请求指定压缩并且请求导致缓存未命中时，前门会直接在 POP 服务器上压缩资产。 此后，将从缓存提供压缩的文件。 通过 transfer-encoding: chunked 返回所生成的项。

## <a name="query-string-behavior"></a>查询字符串行为
借助 Front Door，可控制如何对包含查询字符串的 Web 请求缓存文件。 在包含查询字符串的 Web 请求中，查询字符串是问号 (?) 后出现的请求部分。 查询字符串可以包含一个或多个键值对，其中字段名称和其值由等号 (=) 分隔。 每个键值对由与号 (&) 分隔。 例如，`http://www.contoso.com/content.mov?field1=value1&field2=value2`。 如果请求的查询字符串中有多个键值对，则其顺序并不重要。
- **忽略查询字符串**：在此模式下，前门将查询字符串从请求方传递到第一个请求上的后端，并缓存该资产。 从前门环境提供的资产的所有后续请求将忽略查询字符串，直至缓存的资产到期。

- **缓存每个唯一的 URL**：在此模式下，包含唯一 URL 的每个请求（包括查询字符串）将视为具有其自己的缓存的唯一资产。 例如，来自的请求的后端的响应 `www.example.ashx?q=test1` 将缓存在前门环境中，并为具有相同查询字符串的后续缓存返回该响应。 `www.example.ashx?q=test2` 的请求将作为具有其自己的生存时间设置的单独资产来缓存。

## <a name="cache-purge"></a>缓存清除

前门会缓存资产，直到资产的生存时间 (TTL) 过期。 每当客户端请求的资产的 TTL 已过期时，前门环境会检索新的更新的资产副本以提供请求，然后存储已刷新的缓存。

确保用户始终获取资产的最新副本的最佳做法是针对每次更新将资产版本化，并将其发布为新 URL。 Front Door 将立即检索用于下一个客户端请求的新资产。 有时候可能希望从所有边缘节点清除缓存的内容，并强制其全部检索新的已更新资产。 原因可能是 web 应用程序的更新，也可能是快速更新包含错误信息的资产。

从边缘节点中选择要清除的资产。 若要清除所有资产，请选择 " **全部清除**"。 否则，在 " **路径**" 中，输入要清除的每个资产的路径。

要清除的路径列表支持这些格式：

- **单路径清除**：通过指定资产 (的完整路径（没有协议和域) ，使用文件扩展名，例如，/pictures/strasbourg.png;
- **通配符清除**：星号 (\*) 可用作通配符。 使用路径在路径中清除所有文件夹、子文件夹和文件， \* 或通过指定文件夹后跟/，清除特定文件夹下的所有子文件夹和文件， \* 例如，/pictures/ \* 。
- **根域清除**：清除路径中具有“/”的终结点的根。

> [!NOTE]
> **清除通配符域**：指定用于清除的缓存路径，如本部分中所述，不适用于与前门关联的任何通配符域。 目前，我们不支持直接清除通配符域。 可以通过指定 "针对特定" 子域和清除路径，清除特定子域中的路径。 例如，如果我的前门具有 `*.contoso.com` ，则可通过键入清除我的子域资产 `foo.contoso.com` `foo.contoso.com/path/*` 。 目前，如果适用，在清除内容路径中指定主机名将 imited 到通配符域的子域。
>

Front Door 的缓存清除不区分大小写。 此外，它们是不可知的查询字符串，这意味着清除 URL 将清除其所有查询字符串变体。 

## <a name="cache-expiration"></a>缓存到期
以下标头顺序用于确定项在缓存中的存储时间：</br>
1. Cache-Control: s-maxage=\<seconds>
2. 缓存控制：最大期限 =\<seconds>
3. 完 \<http-date>

表明响应不会缓存的响应标头（如缓存控制：专用、缓存控制：非缓存和缓存控制：不会进行缓存） Cache-Control。  如果没有 Cache-Control，则默认行为是前端将缓存资源的时间为 X，其中 X 随机选取时间为1到3天。

## <a name="request-headers"></a>请求标头

使用缓存时，以下请求标头不会转发到后端。
- Content-Length
- Transfer-Encoding

## <a name="cache-duration"></a>缓存持续时间

缓存持续时间可以在前门设计器和规则引擎中进行配置。 在前门设计器中设置的缓存持续时间是最小缓存持续时间。 如果源的缓存控制标头的 TTL 大于替代值，则此替代将不起作用。 

通过规则引擎设置的缓存持续时间是真正的缓存替代，这意味着它将使用替代值而不考虑源响应标头。

## <a name="next-steps"></a>后续步骤

- 了解如何[创建 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的工作原理](front-door-routing-architecture.md)。
