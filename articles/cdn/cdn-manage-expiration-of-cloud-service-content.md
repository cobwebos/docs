---
title: "在 Azure 内容交付网络中管理 Web 内容的到期时间 | Microsoft Docs"
description: "了解如何管理 Azure CDN 中 Azure Web 应用/云服务、ASP.NET 或 IIS 内容的过期问题。"
services: cdn
documentationcenter: .NET
author: zhangmanling
manager: erikre
editor: 
ms.assetid: bef53fcc-bb13-4002-9324-9edee9da8288
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 11/10/2017
ms.author: mazha
ms.openlocfilehash: fe519c3ad5f99899277bf005929142c52a4c4724
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/23/2017
---
# <a name="manage-expiration-of-web-content-in-azure-content-delivery-network"></a>在 Azure 内容交付网络中管理 Web 内容的到期时间
> [!div class="op_single_selector"]
> * [Azure Web 内容](cdn-manage-expiration-of-cloud-service-content.md)
> * [Azure Blob 存储](cdn-manage-expiration-of-blob-content.md)
> 

来自任何可公开访问的源 Web 服务器的文件均可在 Azure 内容交付网络 (CDN) 中进行缓存，直到其生存时间 (TTL) 结束。 TTL 由来自源服务器的 HTTP 响应中的 `Cache-Control` 标头决定。 本文介绍如何为 Microsoft Azure 应用服务的 Web 应用功能、Azure 云服务、ASP.NET 应用程序和 Internet Information Services (IIS) 网站设置 `Cache-Control` 标头，所有标头的配置方式都类似。 可以使用配置文件或以编程方式设置 `Cache-Control` 标头。

> [!TIP]
> 可以选择不对文件设置 TTL。 在这种情况下，Azure CDN 会自动应用默认为 7 天的 TTL。 此默认 TTL 仅适用于常规 Web 交付优化。 对于大型文件优化，默认 TTL 为一天；对于媒体流优化，默认 TTL 为一年。
> 
> 有关 Azure CDN 如何加速访问文件和其他资源的详细信息，请参阅 [Azure 内容交付网络概述](cdn-overview.md)。
> 

## <a name="setting-cache-control-headers-by-using-configuration-files"></a>使用配置文件设置 Cache-Control 标头
对于静态内容（如图像和样式表），可以通过修改 Web 应用程序的 **applicationHost.config** 或 **Web.config** 配置文件来控制更新频率。 其中任一文件的 `<system.webServer>/<staticContent>/<clientCache>` 元素会设置内容的 `Cache-Control` 标头。

### <a name="using-applicationhostconfig-files"></a>使用 ApplicationHost.config 文件
**ApplicationHost.config** 文件是 IIS 配置系统的根文件。 **ApplicationHost.config** 文件中的配置设置会影响站点上的所有应用程序，但可通过 Web 应用程序的任何 **Web.config** 文件的设置进行重写。

### <a name="using-webconfig-files"></a>使用 Web.config 文件
使用 **Web.config** 文件，可以自定义整个 Web 应用程序或 Web 应用程序的特定目录的行为方式。 通常情况下，Web 应用程序的根文件夹中至少有一个**Web.config** 文件。 对于特定文件夹中的每个 **Web.config** 文件，配置设置将影响该文件夹及所有子文件夹中的所有内容，除非这些设置由另一 **Web.config** 文件在子文件夹级别进行了重写。 例如，可以设置 Web 应用程序根文件夹的 **Web.config** 文件中的 `<clientCache>` 元素，以便将 Web 应用程序的所有静态内容缓存 3 天。 还可以在包含更多可变内容的子文件夹（例如，`\frequent`）中添加 **Web.config** 文件并设置其 `<clientCache>` 元素，以便将该子文件夹的内容缓存 6 小时。 最终结果是，除了 `\frequent` 目录中的任何内容（这些内容将仅仅缓存 6 小时）之外，整个网站的内容将缓存 3 天。  

以下 XML 示例演示如何设置配置文件中的 `<clientCache>` 元素，将最长存在时间指定为 3 天：  

```xml
<configuration>
    <system.webServer>
        <staticContent>
            <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" />
        </staticContent>
    </system.webServer>
</configuration>
```

若要使用 **cacheControlMaxAge** 属性，必须将 **cacheControlMode** 属性的值设为 `UseMaxAge`。 此设置导致了 HTTP 标头和指令 `Cache-Control: max-age=<nnn>` 添加到响应中。 **cacheControlMaxAge** 属性的时间跨度值格式为 `<days>.<hours>:<min>:<sec>`。 其值将转换为秒，并且会作为 `Cache-Control` `max-age` 指令的值使用。 有关 `<clientCache>` 元素的详细信息，请参阅[客户端缓存<clientCache>](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)。  

## <a name="setting-cache-control-headers-programmatically"></a>以编程方式设置 Cache-Control 标头
对于 ASP.NET 应用程序，可以通过设置 .NET API 的 **HttpResponse.Cache** 属性，以编程方式控制 CDN 缓存行为。 有关 **HttpResponse.Cache** 属性的信息，请参阅 [HttpResponse.Cache 属性](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx)和 [HttpCachePolicy 类](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx)。  

若要以编程方式在 ASP.NET 中缓存应用程序内容，请执行以下步骤：
   1. 验证是否已通过将 `HttpCacheability` 设置为 `Public` 将内容标记为可缓存。 
   2. 调用以下 `HttpCachePolicy` 方法之一设置缓存验证器：
      - 调用 `SetLastModified` 以设置 `Last-Modified` 标头的时间戳值。
      - 调用 `SetETag` 以设置 `ETag` 标头的值。
   3. （可选）调用 `SetExpires` 以设置 `Expires` 标头的值，从而指定缓存到期时间。 否则，将应用本文前面所述的默认缓存试探程序。

例如，要将内容缓存 1 小时，可添加以下 C# 代码：  

```csharp
// Set the caching parameters.
Response.Cache.SetExpires(DateTime.Now.AddHours(1));
Response.Cache.SetCacheability(HttpCacheability.Public);
Response.Cache.SetLastModified(DateTime.Now);
```

## <a name="testing-the-cache-control-header"></a>测试 Cache-Control 标头
可以轻松验证 Web 内容 的 TTL 设置。 使用浏览器的[开发人员工具](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)，测试 Web 内容是否包含 `Cache-Control` 响应标头。 还可以使用 **wget**、[Postman](https://www.getpostman.com/) 或 [Fiddler](http://www.telerik.com/fiddler) 等工具检查响应标头。

## <a name="next-steps"></a>后续步骤
* [阅读有关 **clientCache** 元素](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)的详细信息
* [阅读有关 **HttpResponse.Cache** 属性的文档](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) 
* [阅读有关 **HttpCachePolicy 类**的文档](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx)。  

