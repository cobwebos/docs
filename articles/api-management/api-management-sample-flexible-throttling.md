---
title: "使用 Azure API 管理进行高级请求限制"
description: "了解如何使用 Azure API 管理创建并应用灵活的配额和速率限制策略。"
services: api-management
documentationcenter: 
author: darrelmiller
manager: erikre
editor: 
ms.assetid: fc813a65-7793-4c17-8bb9-e387838193ae
ms.service: api-management
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/15/2016
ms.author: apipm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 2a5078b34f74efd5d394587d8ace7f339ecedb5e


---
# <a name="advanced-request-throttling-with-azure-api-management"></a>使用 Azure API 管理进行高级请求限制
限制传入请求是 Azure API 管理的重要功能。 通过控制请求的速率或传输的请求/数据总量，API 管理让 API 提供程序能够保护其 API 不被滥用，为不同的 API 产品层创造价值。

## <a name="product-based-throttling"></a>基于产品的限制
到目前为止，速率限制功能局限于特定产品订阅的限定范围（本质上是一个密钥），在 API 管理发布者门户中定义。 API 提供程序可以使用它将限制应用到注册使用其 API 的开发人员，但是，举例而言，它无法帮助限制 API 的每个最终用户。 想让开发人员的应用程序的单个用户使用整个配额，并让开发人员的其他客户无法使用应用程序，是有可能的。 此外，生成大量请求的多个客户可能限制临时用户的访问权限。

## <a name="custom-key-based-throttling"></a>基于自定义密钥的限制
新的[基于密钥的速率限制](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey)和[基于密钥的配额](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey)策略提供灵活得多的流量控制解决方案。 使用这些新策略可以定义表达式，识别用于跟踪流量使用的密钥。 最好是举一个例子来演示其工作原理。 

## <a name="ip-address-throttling"></a>IP 地址限制
以下策略限制单个客户端 IP 地址每一分钟只有 10 个调用，等于每个月总数为 1,000,000 个调用和 10,000 KB 带宽。 

```xml
<rate-limit-by-key  calls="10"
          renewal-period="60"
          counter-key="@(context.Request.IpAddress)" />

<quota-by-key calls="1000000"
          bandwidth="10000"
          renewal-period="2629800"
          counter-key="@(context.Request.IpAddress)" />
```

如果 Internet 上的所有客户端都使用唯一 IP 地址，这是可能是限制用户使用量的有效方式。 但是，很有可能多个用户共享单个公共 IP 地址，由于他们通过 NAT 设备访问 Internet。 尽管如此，对允许未经身份验证访问的 API 而言，`IpAddress` 可能是最佳选项。

## <a name="user-identity-throttling"></a>用户标识限制
如果用户经过身份验证，可以根据该用户的唯一标识生成限制密钥。

```xml
<rate-limit-by-key calls="10"
    renewal-period="60"
    counter-key="@(context.Request.Headers.GetValueOrDefault("Authorization","").AsJwt()?.Subject)" />
```

在本示例中，我们要提取授权标头，将它转换为 `JWT` 对象，然后使用令牌的使用者来识别用户，并将它用作速率限制密钥。 如果用户标识作为声明之一存储在 `JWT` 中，可以改用该值。

## <a name="combined-policies"></a>组合策略
尽管新限制策略比现有限制策略提供更大的控制度，但仍有组合两种功能的值。 基于产品订阅密钥的限制（[按订阅限制调用率](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate)和[按订阅设置使用量配额](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota)）根据使用级别收费，是让 API 赚钱的最好方法。 更精细的按用户控制限制与其互补，防止一个用户的行为降低另一个用户的体验。 

## <a name="client-driven-throttling"></a>客户端驱动的限制
使用[策略表达式](https://msdn.microsoft.com/library/azure/dn910913.aspx)定义限制密钥时，API 提供程序将选择如何设置限制范围。 但是，开发人员可以控制自己客户的速率限制。 API 提供程序可以通过导入自定义标头来做到这一点，允许开发人员的客户端应用程序向 API 传递密钥。

```xml
<rate-limit-by-key calls="100"
          renewal-period="60"
          counter-key="@(request.Headers.GetValueOrDefault("Rate-Key",""))"/>
```

这样，开发人员的客户端应用程序便可以选择如何创建速率限制密钥。 加上一些奇思妙想，客户端开发人员可以通过分配密钥集给用户和轮流使用密钥，创建自己的速率层。

## <a name="summary"></a>摘要
Azure API 管理提供速率和配额限制，不但能保护 API 服务，而且能为 API 服务增加价值。 使用新的限制策略与自定义范围规则，可以更精细地控制这些策略，让客户构建更好的应用程序。 本文中的示例演示如何使用这些新策略，分别使用客户端 IP 地址、用户标识和客户端生成的值来创建速率限制密钥。 但是，消息中还有许多其他部分可供使用，例如用户代理、URL 路径段和消息大小。

## <a name="next-steps"></a>后续步骤
欢迎在本主题的 Disqus 贴子中提供反馈。 我们很想知道是否还可以在你的方案中合理地选择其他可能的密钥值。

## <a name="watch-a-video-overview-of-these-policies"></a>观看这些策略的视频概述
有关本文中所述的 [rate-limit-by-key](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) 和 [quota-by-key](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey) 策略的详细信息，请观看以下视频。

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Advanced-Request-Throttling-with-Azure-API-Management/player]
> 
> 




<!--HONumber=Nov16_HO3-->


