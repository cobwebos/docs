---
title: 缓存工作原理 | Microsoft Docs
description: 缓存即在本地存储数据的过程，以便将来可以更快地访问数据请求。
services: cdn
documentationcenter: ''
author: dksimpson
manager: akucer
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/30/2018
ms.author: v-deasim
ms.openlocfilehash: bb0824995972b49febdb1695e41f45fbd0966cd1
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
ms.locfileid: "33765784"
---
# <a name="how-caching-works"></a>缓存工作原理

本文概述了一般缓存概念以及 [Azure 内容分发网络 (CDN)](cdn-overview.md) 如何使用缓存来提高性能。 如果想了解如何在 CDN 终结点上自定义缓存行为，请参阅[使用缓存规则控制 Azure CDN 缓存行为](cdn-caching-rules.md)和[使用查询字符串控制 Azure CDN 缓存行为](cdn-query-string.md)。

## <a name="introduction-to-caching"></a>缓存简介

缓存即在本地存储数据的过程，以便将来可以更快地访问数据请求。 Web 浏览器缓存是最常见的缓存类型中，在此类缓存中，Web 浏览器将静态数据的副本本地存储在本地硬盘上。 通过使用缓存，Web 浏览器可避免多次往返服务器，而只需在本地访问相同的数据，从而节省时间和资源。 缓存非常适合用于在本地管理小型静态数据，如静态图像、CSS 文件和 JavaScript 文件。

同样，内容分发网络在靠近用户的边缘服务器上使用缓存，以避免请求返回源服务器，从而减少用户延迟。 与仅适用于单个用户的 Web 浏览器缓存不同，CDN 具有共享缓存。 在 CDN 共享缓存中，其他用户稍后可访问某一用户请求的文件，这大大减少了对源服务器的请求数量。

无法缓存频繁更改或单个用户的唯一动态资源。 不过，这些类型的资源可利用 Azure 内容分发网络上的动态站点加速 (DSA) 优化来提高性能。

可在源服务器和最终用户之间的多个级别上进行缓存：

- Web 服务器：使用共享缓存（适用于多个用户）。
- 内容分发网络：使用共享缓存（适用于多个用户）。
- Internet 服务提供商 (ISP)：使用共享缓存（适用于多个用户）。
- Web 浏览器：使用专用缓存（适用于一个用户）。

通常，每个缓存管理自己的资源刷新，并在文件过期时执行验证。 此行为在 HTTP 缓存规范 [RFC 7234](https://tools.ietf.org/html/rfc7234) 中定义。

### <a name="resource-freshness"></a>资源刷新

由于缓存资源可能会过期或过时（与源服务器上的相应资源相比），因此对任何缓存机制而言，控制刷新内容的时间至关重要。 为节省时间和带宽消耗，每次访问时都不会将缓存资源与源服务器上的版本进行比较。 相反，只要认定缓存资源是最新的，就认为它是当前版本，并将其直接发送到客户端。 如果缓存资源的寿命小于缓存设置定义的寿命或期限，则认为其是最新缓存资源。 例如，当浏览器重载网页时，它会验证硬盘上的每个缓存资源是否为最新并加载它。 如果资源不是最新的（过时），则从服务器加载最新副本。

### <a name="validation"></a>验证

如果认为资源已过时，则要求源服务器对其进行验证，即确定缓存中的数据是否仍与源服务器上的数据相匹配。 如果已在源服务器上修改了文件，缓存会更新其资源的版本。 否则，如果资源为最新，将直接从缓存中传递数据，而无需首先对其进行验证。

### <a name="cdn-caching"></a>CDN 缓存

缓存是 CDN 运行方式不可或缺的一部分，用于加速传递和减少静态资产（如图像、字体和视频）的源加载。 在 CDN 缓存中，选择性地将静态资源存储在策略性服务器上，该服务器对用户而言更本地化，并具有以下优势：

- 由于大多数 Web 流量是静态的（例如图像、字体和视频），CDN 缓存通过将内容移近用户来减少数据传输的距离，从而减少网络延迟。

- 通过将工作卸载到 CDN，缓存可减少网络流量和源服务器上的加载。 这样做可降低应用程序的成本和资源需求，即使在有大量用户的情况下也是如此。

与缓存在 Web 浏览器中的执行方式类似，可通过发送缓存指令标头来控制缓存在 CDN 中的执行方式。 缓存指令标头即 HTTP 标头，通常由源服务器添加。 尽管大部分标头最初都旨在解决客户端浏览器中的缓存问题，但现在所有中间缓存（如 CDN）也会使用这些标头。 

可使用两个标头来定义缓存刷新：`Cache-Control` 和 `Expires`。 如果两者都存在，则 `Cache-Control` 为最新且优先于 `Expires`。 还有两种用于验证的标头类型（称为验证程序）：`ETag` 和 `Last-Modified`。 如果两者均已定义，则 `ETag` 为最新且优先于 `Last-Modified`。  

## <a name="cache-directive-headers"></a>缓存指令标头

> [!IMPORTANT]
> 默认情况下，针对 DSA 进行了优化的 Azure CDN 终结点将忽略缓存指令标头，绕过缓存。 对于 **Verizon 提供的标准 Azure CDN** 和 **Akamai 提供的标准 Azure CDN** 配置文件，可以使用 [CDN 缓存规则](cdn-caching-rules.md)启用缓存，来调整 Azure CDN 终结点处理这些标头的方式。 仅对于 **Verizon 提供的高级 Azure CDN** 配置文件，可以使用[规则引擎](cdn-rules-engine.md)来启用缓存。

Azure CDN 支持以下 HTTP 缓存指令标头，它们定义了缓存持续时间和缓存共享。

**Cache-Control：**
- 在 HTTP 1.1 中引入，用于为 Web 发布者提供对其内容的更多控制权，并解决 `Expires` 标头的局限性。
- 如果同时定义了 `Expires` 和 `Cache-Control` 标头，则将替代前一个标头。
- 在从客户端发往 CDN POP 的 HTTP 请求中使用时，`Cache-Control` 会被所有 CDN 配置文件默认忽略。
- 在从客户端发往 CDN POP 的 HTTP 响应中使用时：
     - **Verizon 推出的 Azure CDN 标准版/高级版**和 **Microsoft 推出的 Azure CDN 标准版**支持所有 `Cache-Control` 指令。
     - **Akamai 推出的 Azure CDN 标准版**仅支持以下 `Cache-Control` 指令，将忽略所有其他指令：
         - `max-age`：缓存可存储指定秒数的内容。 例如，`Cache-Control: max-age=5`。 此指令指定了被视为最新内容的最长时间。
         - `no-cache`：缓存内容，但每次传送缓存中的内容前会对其进行验证。 等效于 `Cache-Control: max-age=0`。
         - `no-store`：从不缓存内容。 删除之前已存储的内容。

**Expires：**
- HTTP 1.0 中引入的旧标头支持向后兼容性。
- 使用基于日期的过期时间，精确到秒。 
- 类似于 `Cache-Control: max-age`。
- 当 `Cache-Control` 不存在时使用。

**Pragma：**
   - Azure CDN 默认情况下未采用。
   - HTTP 1.0 中引入的旧标头支持向后兼容性。
   - 用作具有以下指令的客户端请求标头：`no-cache`。 此指令指示服务器提供新的资源版本。
   - `Pragma: no-cache` 等效于 `Cache-Control: no-cache`。

## <a name="validators"></a>验证程序

当缓存过时时，使用 HTTP 缓存验证程序将文件的缓存版本与源服务器上的版本进行比较。 **Verizon 推出的 Azure CDN 标准版/高级版**默认支持 `ETag` 和 `Last-Modified` 验证程序，而 **Microsoft 推出的 Azure CDN 标准版**和 **Akamai 推出的 Azure CDN 标准版**默认仅支持 `Last-Modified`。

**ETag：**
- **Verizon 推出的 Azure CDN 标准版/高级版**默认支持 `ETag`，而 **Microsoft 推出的 Azure CDN 标准版**和 **Akamai 推出的 Azure CDN 标准版**则不。
- `ETag` 为每个文件和文件版本定义唯一字符串。 例如，`ETag: "17f0ddd99ed5bbe4edffdd6496d7131f"`。
- 在 HTTP 1.1 中引入，并且比 `Last-Modified` 更新。 当很难确定上次修改日期时，会非常有用。
- 支持强验证和弱验证，不过，Azure CDN 仅支持强验证。 对于强验证，两种资源表示形式的每个字节都必须相同。 
- 缓存通过在请求中发送带有一个或多个 `ETag` 验证程序的 `If-None-Match` 标头来验证使用 `ETag` 的文件。 例如，`If-None-Match: "17f0ddd99ed5bbe4edffdd6496d7131f"`。 如果服务器的版本与列表中的 `ETag` 验证程序相匹配，则在其响应中发送状态代码 304（未修改）。 如果版本不同，则服务器响应状态代码 200（确定）和更新后的资源。

**Last-Modified：**
- 如果 `ETag` 不是 HTTP 响应的一部分，则使用 `Last-Modified`（仅限 **Verizon 推出的 Azure CDN 标准版/高级版**）。 
- 指定源服务器已确定上次修改资源的日期和时间。 例如，`Last-Modified: Thu, 19 Oct 2017 09:28:00 GMT`。
- 缓存通过在请求中发送带有日期和时间的 `If-Modified-Since` 标头来验证使用 `Last-Modified` 的文件。 源服务器将该日期与最新资源的 `Last-Modified` 标头进行比较。 如果自指定时间以来未修改该资源，则服务器在其响应中返回状态代码 304（未修改）。 如果已修改该资源，则服务器返回状态代码 200（确定）和更新后的资源。

## <a name="determining-which-files-can-be-cached"></a>确定可以缓存哪些文件

并非所有资源均可缓存。 下表根据 HTTP 响应的类型显示了可缓存的资源。 无法缓存不满足所有条件的 HTTP 响应所提供的资源。 可使用规则引擎自定义上述某些条件（仅限 **Verizon 推出的 Azure CDN 高级版**）。

|                   | Microsoft 推出的 Azure CDN          | Verizon 的 Azure CDN | Akamai 的 Azure CDN        |
|-------------------|-----------------------------------|------------------------|------------------------------|
| HTTP 状态代码 | 200、203、206、300、301、410、416 | 200                    | 200、203、300、301、302、401 |
| HTTP 方法      | GET、HEAD                         | GET                    | GET                          |
| 文件大小限制  | 300 GB                            | 300 GB                 | - 常规 Web 传递优化：1.8 GB<br />- 媒体流式处理优化：1.8 GB<br />- 大型文件优化：150 GB |

若要在资源上使用 **Microsoft 推出的 Azure CDN 标准版**缓存，源服务器必须支持任何 HEAD 和 GET HTTP 请求，且资产的任何 HEAD 和 GET HTTP 响应的 content-length 值必须相同。 对于 HEAD 请求，源服务器必须支持 HEAD 请求，且必须使用接收 GET 请求时所使用的标头进行响应。

## <a name="default-caching-behavior"></a>默认缓存行为

下表介绍了 Azure CDN 产品的默认缓存行为及其优化。

|    | Microsoft：常规 Web 分发 | Verizon：常规 Web 分发 | Verizon：DSA | Akamai：常规 Web 分发 | Akamai：DSA | Akamai：大型文件下载 | Akamai：常规或 VOD 媒体流式处理 |
|------------------------|--------|-------|------|--------|------|-------|--------|
| **优先处理源**       | 是    | 是   | 否   | 是    | 否   | 是   | 是    |
| **CDN 缓存持续时间** | 2 天 |7 天 | 无 | 7 天 | 无 | 1 天 | 1 年 |

**优先处理源**：指定是否优先处理[支持的缓存指令标头](#http-cache-directive-headers)（如果它们存在于源服务器的 HTTP 响应中）。

**CDN 缓存持续时间**：指定在 Azure CDN 上可缓存资源的时间。 然而，如果“优先处理源”为“是”，并且源服务器的 HTTP 响应包括缓存指令标头 `Expires` 或 `Cache-Control: max-age`，则 Azure CDN 将改用由标头指定的持续时间值。 

## <a name="next-steps"></a>后续步骤

- 若要了解如何通过缓存规则自定义和替代 CDN 上的默认缓存行为，请参阅[使用缓存规则控制 Azure CDN 缓存行为](cdn-caching-rules.md)。 
- 若要了解如何使用查询字符串控制缓存行为，请参阅[使用查询字符串控制 Azure CDN 缓存行为](cdn-query-string.md)。



