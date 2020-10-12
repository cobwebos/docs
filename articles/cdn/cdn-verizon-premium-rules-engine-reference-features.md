---
title: Verizon Premium Azure CDN 规则引擎功能
description: Verizon Premium Azure CDN 规则引擎功能的参考文档。
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 06/02/2020
ms.author: allensu
ms.openlocfilehash: 0ea4f167b992ccfbc4156ac06c8f636d2ef4a355
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "84343194"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-features"></a>Verizon Premium Azure CDN 规则引擎功能

本文列出 Azure 内容分发网络 (CDN) [规则引擎](cdn-verizon-premium-rules-engine.md)的可用功能的详细说明。

规则的第三部分是功能。 功能定义将向由一组匹配条件确定的请求类型应用的操作的类型。

## <a name="azure-cdn-from-verizon-premium-rules-engine-features-reference"></a><a name="top"></a>Verizon Premium Azure CDN 规则引擎功能参考

可用的功能类型包括：

* [访问](#access)
* [缓存](#caching)
* Comment 
* [标头](#headers)
* [日志](#logs)
* [优化](#optimize)
* [源](#origin)
* [特色](#specialty)
* [URL](#url)
* [Web 应用程序防火墙](#waf)

### <a name="access"></a><a name="access"></a>访问

以下功能旨在控制对内容的访问。

| 名称       | 目的                                                           |
|------------|-------------------------------------------------------------------|
| [拒绝访问 (403)](https://docs.vdms.com/cdn/Content/HRE/F/Deny-Access-403.htm) | 通过“403 禁止访问”响应确定是否拒绝了所有请求。 |
| [令牌身份验证](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth.htm) | 确定是否会向请求应用基于令牌的身份验证。 |
| [令牌身份验证拒绝代码](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth-Denial-Code.htm) | 确定以基于令牌的身份验证为由拒绝请求时为用户返回的响应类型。 |
| [令牌身份验证忽略 URL 大小写](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth-Ignore-URL-Case.htm) | 确定通过基于令牌的身份验证进行的 URL 比较是否区分大小写。 |
| [令牌身份验证参数](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth-Parameter.htm) | 确定是否应重命名基于令牌的身份验证查询字符串参数。 |

**[返回到顶部](#top)**

### <a name="caching"></a><a name="caching"></a>缓存

这些功能旨在自定义内容的缓存时间和方式。

| 名称       | 目的                                                           |
|------------|-------------------------------------------------------------------|
| [带宽参数](https://docs.vdms.com/cdn/Content/HRE/F/Bandwidth-Parameters.htm) | 确定是否会启用带宽限制参数（即 ec_rate 和 ec_prebuf）。 |
| [带宽限制](https://docs.vdms.com/cdn/Content/HRE/F/Bandwidth-Throttling.htm) | 限制边缘服务器提供的响应的带宽。 |
| [绕过缓存](https://docs.vdms.com/cdn/Content/HRE/F/Bypass-Cache.htm) | 确定请求是否可以利用我们的缓存技术。 |
| [Cache-Control 标头处理](https://docs.vdms.com/cdn/Content/HRE/F/Cache-Control-Header-Treatment.htm) |  在“外部最大有效期”功能启用时，控制边缘服务器生成 Cache-Control 标头。 |
| [Cache-Key 查询字符串](https://docs.vdms.com/cdn/Content/HRE/F/Cache-Key-Query-String.htm) | 确定 **缓存键*** 是否将包括或排除与请求关联的查询字符串参数。 <br> _* 唯一标识用于缓存目的的资产的相对路径。 在检查缓存内容时，边缘服务器使用此相对路径。 默认情况下，缓存键将不包含查询字符串参数。_ |
| [Cache-Key 重写](https://docs.vdms.com/cdn/Content/HRE/F/Cache-Key-Rewrite.htm) | 重写与请求关联的 cache-key。 |
| [完成缓存填充](https://docs.vdms.com/cdn/Content/HRE/F/Complete-Cache-Fill.htm) | 确定当请求导致边缘服务器部分缓存未命中时会发生什么情况。 |
| [压缩文件类型](https://docs.vdms.com/cdn/Content/HRE/F/Compress-File-Types.htm) | 定义会在服务器上压缩的文件的格式。 | 
| [默认的内部最大有效期](https://docs.vdms.com/cdn/Content/HRE/F/Default-Internal-Max-Age.htm) | 确定在进行从边缘服务器到源服务器的缓存重新验证时，默认的最大有效期时间间隔。 |
| [Expires 标头处理](https://docs.vdms.com/cdn/Content/HRE/F/Expires-Header-Treatment.htm) | 在“外部最大有效期”功能启用时，控制边缘服务器生成 Expires 标头。 |
| [外部最大有效期](https://docs.vdms.com/cdn/Content/HRE/F/External-Max-Age.htm) | 确定在进行从浏览器到边缘服务器的缓存重新验证时的最大有效期时间间隔。 |
| [强制内部最大有效期](https://docs.vdms.com/cdn/Content/HRE/F/Force-Internal-Max-Age.htm) | 确定在进行从边缘服务器到源服务器的缓存重新验证时的最大有效期时间间隔。 |
| [H.264 支持（HTTP 渐进式下载）](https://docs.vdms.com/cdn/Content/HRE/F/H.264-Support-HPD.htm) | 确定适用于流式处理内容的 H.264 文件格式的类型。 |
| [H-p 支持视频搜寻参数](https://docs.vdms.com/cdn/Content/HRE/F/H.264-Support-VSP-HPD.htm) | 使用 HTTP 渐进式下载时，将重写分配给控制通过 H-p 媒体进行查找的参数的名称。 |
| [遵循 No-Cache 请求](https://docs.vdms.com/cdn/Content/HRE/F/Honor-No-Cache-Request.htm) | 确定是否会将 HTTP 客户端的 no-cache 请求转发到源服务器。 |
| [忽略源服务器 No-Cache](https://docs.vdms.com/cdn/Content/HRE/F/Ignore-Origin-No-Cache.htm) | 确定 CDN 是否会忽略源服务器提供的某些指令。 |
| [忽略无法满足的范围](https://docs.vdms.com/cdn/Content/HRE/F/Ignore-Unsatisfiable-Ranges.htm) | 确定当请求生成“416 无法满足请求的范围”状态代码时，会为客户端返回的响应。 |
| [内部最大过时期限](https://docs.vdms.com/cdn/Content/HRE/F/Internal-Max-Stale.htm) | 控制在边缘服务器无法重新验证源服务器的缓存资产的情况下，允许边缘服务器在正常到期时间过后多长时间内提供缓存资产。 |
| [部分缓存共享](https://docs.vdms.com/cdn/Content/HRE/F/Partial-Cache-Sharing.htm) | 确定请求是否可以生成部分缓存的内容。 |
| [预验证缓存内容](https://docs.vdms.com/cdn/Content/HRE/F/Prevalidate-Cached-Content.htm) | 确定缓存内容在其 TTL 到期之前是否适合进行早期重新验证。 |
| [刷新零字节缓存文件](https://docs.vdms.com/cdn/Content/HRE/F/Refresh-Zero-Byte-Cache-Files.htm) | 确定边缘服务器如何处理 HTTP 客户端要求提供 0 字节缓存资产的请求。 |
| [设置“可缓存”状态代码](https://docs.vdms.com/cdn/Content/HRE/F/Set-Cacheable-Status-Codes.htm) | 定义一组允许进行内容缓存的状态代码。 |
| [在出错时交付过时的内容](https://docs.vdms.com/cdn/Content/HRE/F/Stale-Content-Delivery-on-Error.htm) | 确定在缓存重新验证时出错或者在从客户源服务器检索请求内容时出错的情况下，是否交付到期的缓存内容。 | 
| [在重新验证时交付过时的内容](https://docs.vdms.com/cdn/Content/HRE/F/Stale-While-Revalidate.htm) | 允许边缘服务器在重新验证时会过时的客户端内容提供给请求者，以便提高性能。 |

**[返回到顶部](#top)**

### <a name="comment"></a><a name="comment"></a>注释

注释功能允许在规则中添加注释。

**[返回到顶部](#top)**

### <a name="headers"></a><a name="headers"></a>标头

以下功能旨在添加、修改或删除请求或响应中的标头。

| 名称       | 目的                                                           |
|------------|-------------------------------------------------------------------|
| [Age 响应标头](https://docs.vdms.com/cdn/Content/HRE/F/Age-Response-Header.htm) | 确定是否会在发送给请求者的响应中包括 Age 响应标头。 |
| [调试缓存响应标头](https://docs.vdms.com/cdn/Content/HRE/F/Debug-Cache-Response-Headers.htm) | 确定响应是否可以包含 [X-EC-调试响应标头，该标头](https://docs.vdms.com/cdn/Content/Knowledge_Base/X_EC_Debug.htm) 提供有关所请求资产的缓存策略的信息。 |
| [修改客户端请求标头](https://docs.vdms.com/cdn/Content/HRE/F/Modify-Client-Request-Header.htm) | 覆盖、追加或删除请求的标头。 |
| [修改客户端响应标头](https://docs.vdms.com/cdn/Content/HRE/F/Modify-Client-Response-Header.htm) | 覆盖、追加或删除响应的标头。 |
| [设置客户端 IP 自定义标头](https://docs.vdms.com/cdn/Content/HRE/F/Set-Client-IP-Custom-Header.htm) | 允许将请求客户端的 IP 地址作为自定义请求标头添加到请求。 |

**[返回到顶部](#top)**

### <a name="logs"></a><a name="logs"></a>日志

这些功能旨在自定义存储在原始日志文件中的数据。

| 名称       | 目的                                                           |
|------------|-------------------------------------------------------------------|
| [自定义日志字段 1](https://docs.vdms.com/cdn/Content/HRE/F/Custom-Log-Field-1.htm) | 确定将要在原始日志文件中分配给自定义日志字段的格式和内容。 |
| [日志查询字符串](https://docs.vdms.com/cdn/Content/HRE/F/Log-Query-String.htm) | 确定是否会将查询字符串和 URL 一起存储在访问日志中。 |

**[返回到顶部](#top)**

### <a name="optimize"></a><a name="optimize"></a>优化

这些功能确定请求是否将经过边缘优化器提供的优化。

| 名称       | 目的                                                           |
|------------|-------------------------------------------------------------------|
| [边缘优化器](https://docs.vdms.com/cdn/Content/HRE/F/Edge-Optimizer.htm) | 确定是否可将边缘优化器应用到请求。 |
| [边缘优化器–实例化配置](https://docs.vdms.com/cdn/Content/HRE/F/Edge-Optimizer-Instantiate-Configuration.htm) | 实例化或激活与站点关联的边缘优化器配置。 |

**[返回到顶部](#top)**

### <a name="origin"></a><a name="origin"></a>格式

以下功能旨在控制 CDN 与源服务器的通信方式。

| 名称       | 目的                                                           |
|------------|-------------------------------------------------------------------|
| [最大 Keep-Alive 请求数](https://docs.vdms.com/cdn/Content/HRE/F/Maximum-Keep-Alive-Requests.htm) | 定义 Keep-Alive 连接在关闭前的最大请求数。 |
| [代理特殊标头](https://docs.vdms.com/cdn/Content/HRE/F/Proxy-Special-Headers.htm) | 定义一组 [特定于 CDN 的请求标头，这些标头](https://docs.vdms.com/cdn/Content/Knowledge_Base/Request-Format.htm#RequestHeaders) 将从边缘服务器转发给源服务器。 |

**[返回到顶部](#top)**

### <a name="specialty"></a><a name="specialty"></a>特色

以下功能属高级功能，仅供高级用户使用。

| 名称       | 目的                                                           |
|------------|-------------------------------------------------------------------|
| [可缓存的 HTTP 方法](https://docs.vdms.com/cdn/Content/HRE/F/Cacheable-HTTP-Methods.htm) | 确定一组可以在我们的网络上缓存的其他 HTTP 方法。 |
| [可缓存请求正文大小](https://docs.vdms.com/cdn/Content/HRE/F/Cacheable-Request-Body-Size.htm) | 定义的阈值用于确定 POST 响应是否可以缓存。 |
| [QUIC](https://docs.vdms.com/cdn/Content/HRE/F/QUIC.htm) | 确定是否通知客户端 CDN 服务支持 QUIC。 |
| [流优化](https://docs.vdms.com/cdn/Content/HRE/F/Streaming-Optimization.htm) | 调整缓存配置以优化实时流的性能，并减少源服务器上的负载。 |
| [User 变量](https://docs.vdms.com/cdn/Content/HRE/F/User-Variable.htm) | 向传递到订购流量处理解决方案的用户定义变量赋值。 |

**[返回到顶部](#top)**

### <a name="url"></a><a name="url"></a>URL

以下功能允许将请求重定向到其他 URL 或者将其重写。

| 名称       | 目的                                                           |
|------------|-------------------------------------------------------------------|
| [遵循重定向](https://docs.vdms.com/cdn/Content/HRE/F/Follow-Redirects.htm) | 确定是否可以将请求重定向到在客户源服务器返回的 Location 标头中定义的主机名。 |
| [URL 重定向](https://docs.vdms.com/cdn/Content/HRE/F/URL-Redirect.htm) | 通过 Location 标头重定向请求。 |
| [URL 重写](https://docs.vdms.com/cdn/Content/HRE/F/URL-Rewrite.htm) | 重写请求 URL。 |

**[返回到顶部](#top)**

### <a name="web-application-firewall"></a><a name="waf"></a>Web 应用程序防火墙

[Web 应用程序防火墙](https://docs.vdms.com/cdn/Content/HRE/F/Web_Application_Firewall.htm)功能确定是否将由 Web 应用程序防火墙筛选请求。

**[返回到顶部](#top)**

有关最新功能，请参阅 [Verizon 规则引擎文档](https://docs.vdms.com/cdn/index.html#Quick_References/HRE_QR.htm#Actions)。

## <a name="next-steps"></a>后续步骤

- [规则引擎参考](cdn-verizon-premium-rules-engine-reference.md)
- [规则引擎条件表达式](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [规则引擎匹配条件](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [使用规则引擎重写 HTTP 行为](cdn-verizon-premium-rules-engine.md)
- [Azure CDN 概述](cdn-overview.md)
