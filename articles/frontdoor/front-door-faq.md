---
title: Azure 的第一道防线服务-第一道防线的常见问题 |Microsoft Docs
description: 此页提供有关 Azure 第一道防线服务常见问题的解答
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2019
ms.author: sharadag
ms.openlocfilehash: b033f463722ddb3a0b7beabdf659900e7d7188df
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2019
ms.locfileid: "67330871"
---
# <a name="frequently-asked-questions-for-azure-front-door-service"></a>有关 Azure 第一道防线服务常见问题

本文回答有关 Azure 第一道防线服务特性和功能的常见问题。 如果本文未能涵盖你的问题，欢迎通过以下渠道联系我们（以升序排列）：

1. 本文评论部分。
2. [Azure 的第一道防线服务 UserVoice](https://feedback.azure.com/forums/217313-networking?category_id=345025)。
3. **Microsoft 支持部门：** 若要创建新的支持请求，请在 Azure 门户中的“帮助”  选项卡上，选择“帮助和支持”  按钮，然后选择“新建支持请求”  。

## <a name="general"></a>常规

### <a name="what-is-azure-front-door-service"></a>什么是 Azure Front Door 服务？

Azure 的第一道防线服务是作为一种服务，提供各种第 7 层负载平衡功能为应用程序的应用程序传送网络 (ADN)。 它提供动态站点加速 (DSA) 以及全局负载平衡和近乎实时的故障转移。 它是一个高度可用且可伸缩的服务，它由 Azure 完全管理。

### <a name="what-features-does-azure-front-door-service-support"></a>Azure 第一道防线服务支持哪些功能？

第一道防线的 azure 服务支持动态站点加速 (DSA)、 SSL 卸载和端到端 SSL、 Web 应用程序防火墙、 基于 cookie 的会话相关性、 基于 url 路径的路由、 可用证书和多个域管理和其他人。 有关支持的功能的完整列表，请参阅[Azure 第一道防线服务概述](front-door-overview.md)。

### <a name="what-is-the-difference-between-azure-front-door-service-and-azure-application-gateway"></a>Azure 第一道防线服务和 Azure 应用程序网关之间的区别是什么？

虽然第一道防线和应用程序网关是层 7 (HTTP/HTTPS) 负载均衡器，主要区别是第一道防线是全局服务，而应用程序网关是一种区域服务。 虽然第一道防线可以在负载平衡跨区域在不同的缩放单位/群集/戳单元之间，应用程序网关可以是缩放单位内你的虚拟机/容器等之间进行负载平衡。

### <a name="when-should-we-deploy-an-application-gateway-behind-front-door"></a>何时我们应部署应用程序网关背后第一道防线？

为什么一个应该使用应用程序网关背后第一道防线的关键方案是：

- 第一道防线可以执行基于路径的负载均衡只能在全局级别，但如果有希望流量进行负载平衡进一步对其虚拟网络 (VNET) 中它们应使用应用程序网关。
- 由于在虚拟机/容器级别，第一道防线不起作用，因此不能连接排出。 但是，应用程序网关可以执行连接排出。 
- 借助背后 AFD 应用程序网关，一个可以实现 SSL 卸载和路由 HTTP 请求仅对其虚拟网络 (VNET) 中的 100%。
- 第一道防线和应用程序网关都支持会话相关性。 第一道防线可以定向到同一群集或给定区域中的后端的后续流量来自用户会话，而应用程序网关可以直接将关联到同一群集中服务器的流量。  

### <a name="can-we-deploy-azure-load-balancer-behind-front-door"></a>我们可以将部署 Azure 负载均衡器后面第一道防线？

Azure 的第一道防线服务需要使用公共 VIP 或将流量路由到公开可用的 DNS 名称。 部署 Azure 负载均衡器后面第一道防线是一个常见用例。

### <a name="what-protocols-does-azure-front-door-service-support"></a>Azure 第一道防线服务支持哪些协议？

第一道防线的 azure 服务支持 HTTP、 HTTPS 和 HTTP/2。

### <a name="how-does-azure-front-door-service-support-http2"></a>Azure 第一道防线服务如何支持 HTTP/2？

HTTP/2 协议支持可供客户端只连接到 Azure 的第一道防线服务。 通过 HTTP/1.1 到后端池中的后端的通信。 默认情况下启用 HTTP/2 支持。

### <a name="what-resources-are-supported-today-as-part-of-backend-pool"></a>目前支持在后端池中添加哪些资源？

后端池可以包含的存储、 Web 应用、 Kubernetes 实例或任何其他自定义主机名具有公共连接。 Azure 的第一道防线服务需要通过公共 IP 或可公开解析的 DNS 主机名定义后端。 只要它们具有公共连接，可以跨区域，区域，或甚至在 Azure 外部的后端池成员。

### <a name="what-regions-is-the-service-available-in"></a>该服务已在哪些区域推出？

第一道防线的 azure 服务是全局服务，并且不依赖于任何特定的 Azure 区域。 您需要创建第一道防线时指定的唯一位置是资源组位置，这基本上指定的资源组的元数据将存储位置。 第一道防线资源本身创建为全局资源，配置全局部署到所有 Pop （接入点）。 

### <a name="what-are-the-pop-locations-for-azure-front-door-service"></a>什么是 Azure 第一道防线服务的 POP 位置？

Azure 的第一道防线服务都有来自 Microsoft 的 Azure CDN 的 POP （接入点） 的位置相同的列表。 有关我们 Pop 的完整列表，请参阅[从 Microsoft Azure CDN POP 位置](https://docs.microsoft.com/azure/cdn/cdn-pop-locations)。

### <a name="is-azure-front-door-service-a-dedicated-deployment-for-my-application-or-is-it-shared-across-customers"></a>是我的应用程序专门的部署的第一道防线的 Azure 服务或为其所有客户之间都共享？

第一道防线的 azure 服务是全球分布的多租户服务。 因此，所有客户之间共享的基础结构的第一道防线。 但是，通过创建的第一道防线配置文件，你定义你的应用程序所需的特定配置，对您指定的地址进行任何更改会影响其他第一道防线配置。

### <a name="is-http-https-redirection-supported"></a>是否支持 HTTP 到 HTTPS 的重定向？

是的。 事实上，Azure 第一道防线服务支持主机，将路径和查询字符串重定向，以及 URL 重定向的一部分。 详细了解如何[URL 重定向](front-door-url-redirect.md)。 

### <a name="in-what-order-are-routing-rules-processed"></a>在何种顺序的路由规则处理？

在第一道防线的路由未排序并基于最佳匹配项选择具体的路由。 详细了解如何[前门如何匹配请求路由规则](front-door-route-matching.md)。

### <a name="how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door-service"></a>如何向我的后端到仅 Azure 第一道防线服务锁定的访问？

你可以配置为接受来自 Azure 第一道防线服务的流量仅在后端的 IP ACLing。 你可以限制应用程序接受仅来自 Azure 第一道防线服务的后端 IP 空间的传入连接。 我们正在致力于与集成[Azure IP 范围和服务标记](https://www.microsoft.com/download/details.aspx?id=56519)但现在可以引用按如下所示的 IP 范围：
 
- **IPv4** - `147.243.0.0/16`
- **IPv6** - `2a01:111:2050::/44`

> [!WARNING]
> 我们的后端 IP 空间可能会更高版本更改，但是，我们将确保之前发生这种情况，我们将使用已集成[Azure IP 范围和服务标记](https://www.microsoft.com/download/details.aspx?id=56519)。 我们建议你订阅了[Azure IP 范围和服务标记](https://www.microsoft.com/download/details.aspx?id=56519)进行任何更改或更新。 

### <a name="can-the-anycast-ip-change-over-the-lifetime-of-my-front-door"></a>任意广播 IP 可以更改我第一道防线的生存期内？

在第一道防线的前端任意广播 IP 通常不能更改和可能的第一道防线生存期内保持静态。 但是，有**不能保证**的相同。 请先将其做不需要任何直接依赖项在 IP。  

### <a name="does-azure-front-door-service-support-static-or-dedicated-ips"></a>Azure 第一道防线服务是否支持静态或专用 Ip？

不可以，目前 Azure 第一道防线服务不支持任意播静态或专用前端 Ip。 

### <a name="does-azure-front-door-service-support-x-forwarded-for-headers"></a>Azure 第一道防线服务是否支持 x-转发-对于标头？

是的 Azure 第一道防线服务支持 X-转发-对于、 X-转发的主机和 X 转发 Proto 标头。 X-转发-对于 if 标头已经存在，则第一道防线将客户端套接字 IP 追加到其中。 否则，它将使用客户端套接字的 IP 标头添加的值。 对于 X 转发主机和 X 转发 Proto，重写值。

详细了解如何[第一道防线支持的 HTTP 标头](front-door-http-headers-protocol.md)。  

### <a name="how-long-does-it-take-to-deploy-an-azure-front-door-service-does-my-front-door-still-work-when-being-updated"></a>若要部署 Azure 第一道防线服务需要多长时间？ 我第一道防线仍的工作原理时正在更新？

创建新的第一道防线或对现有的第一道防线的任何更新需要大约 3 到 5 分钟为全局部署。 这就意味着在 3 到 5 分钟内，跨所有我们 Pop 将全局部署你的第一道防线配置。

请注意-自定义 SSL 证书更新大约需要 30 分钟时间来全局部署。

## <a name="configuration"></a>配置

### <a name="can-azure-front-door-load-balance-or-route-traffic-within-a-virtual-network"></a>Azure 第一道防线的负载平衡或路由流量是否可以通过在虚拟网络中？

Azure 的第一道防线 (AFD) 需要公共 IP 或将流量路由到可公开解析的 DNS 名称。 因此，答案是没有 AFD 直接无法路由在虚拟网络中，但使用的应用程序网关或 Azure 负载均衡器之间将实施此方案。

### <a name="what-are-the-various-timeouts-and-limits-for-azure-front-door-service"></a>各种超时和 Azure 第一道防线服务的限制是什么？

了解有关所有已编档[超时和 Azure 第一道防线服务的限制](https://docs.microsoft.com/azure/azure-subscription-service-limits#azure-front-door-service-limits)。

## <a name="performance"></a>性能

### <a name="how-does-azure-front-door-service-support-high-availability-and-scalability"></a>Azure 第一道防线服务如何支持高可用性和可伸缩性？

第一道防线的 azure 服务是容量的全球分布式多租户平台，具有大量以满足特定应用程序的可伸缩性需求。 第一道防线传递从 Microsoft 的全球网络的边缘，提供全局负载平衡功能，可用于跨区域或不同的云故障转移整个应用程序或甚至是单个微服务。

## <a name="ssl-configuration"></a>SSL 配置

### <a name="what-tls-versions-are-supported-by-azure-front-door-service"></a>由 Azure 第一道防线服务支持哪些 TLS 版本？

第一道防线支持 TLS 版本 1.0、 1.1 和 1.2。 尚不支持 TLS 1.3。

### <a name="what-certificates-are-supported-on-azure-front-door-service"></a>在 Azure 第一道防线服务上支持哪些证书？

若要启用用于安全地交付内容在第一道防线自定义域的 HTTPS 协议，你可以选择使用由 Azure 第一道防线服务管理的证书或使用你自己的证书。
第一道防线托管 Digicert 通过标准的 SSL 证书的选项预配并存储在 Front 门的密钥保管库。 如果您选择使用你自己的证书，则可以登记受支持的 ca 颁发的证书，可以为标准 SSL、 扩展的验证证书或甚至的通配符证书。 不支持自签名的证书。 了解[如何为自定义域启用 HTTPS](https://aka.ms/FrontDoorCustomDomainHTTPS)。

### <a name="does-front-door-support-auto-rotation-of-certificates"></a>第一道防线是否支持自动轮换的证书？

对于您自己的自定义 SSL 证书，不支持自动轮换。 类似于它的方式安装程序首次为给定的自定义域，你将需要正确的证书版本到点第一道防线，密钥保管库中并确保第一道防线的服务主体仍有权访问密钥保管库。 此更新的证书推出操作的第一道防线是完全原子和不会导致任何生产影响提供使用者名称或 SAN 证书不会更改。
</br>第一道防线托管证书选项时，证书自动旋转第一道防线。

### <a name="what-are-the-current-cipher-suites-supported-by-azure-front-door-service"></a>Azure 第一道防线服务支持的当前密码套件是什么？

以下是 Azure 第一道防线服务支持的当前密码套件：

- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA

### <a name="does-azure-front-door-service-also-support-re-encryption-of-traffic-to-the-backend"></a>Azure 第一道防线服务是否也支持重新加密的流量到后端？

是的 Azure 第一道防线服务支持 SSL 卸载和端到端 SSL，因此重新加密发送到后端的流量。 事实上，由于通过连接到后端来完成它是公共 IP，建议配置第一道防线，以使用 HTTPS 作为转发协议。

### <a name="can-i-configure-ssl-policy-to-control-ssl-protocol-versions"></a>是否可以配置 SSL 策略来控制 SSL 协议版本？

不可以，目前不支持第一道防线拒绝特定 TLS 版本，也可以设置最小的 TLS 版本。 

### <a name="can-i-configure-front-door-to-only-support-specific-cipher-suites"></a>可以配置第一道防线，以仅支持特定的密码套件吗？

否，不支持为特定的密码套件配置第一道防线。 

## <a name="diagnostics-and-logging"></a>诊断和日志记录

### <a name="what-types-of-metrics-and-logs-are-available-with-azure-front-door-service"></a>可以使用 Azure 第一道防线服务哪些类型的指标和日志？

有关日志和其他诊断功能的信息，请参阅[监视指标和日志的第一道防线](front-door-diagnostics.md)。

### <a name="what-is-the-retention-policy-on-the-diagnostics-logs"></a>什么是诊断日志的保留策略？

诊断日志将发往客户存储帐户，客户可以根据偏好设置保留策略。 此外，可将诊断日志发送到事件中心或 Azure Monitor 日志。 有关详细信息，请参阅[Azure 第一道防线服务诊断](front-door-diagnostics.md)。

### <a name="how-do-i-get-audit-logs-for-azure-front-door-service"></a>如何获取 Azure 第一道防线服务审核日志？

审核日志都可用于 Azure 的第一道防线服务。 在门户中，单击**活动日志**第一道防线，若要访问审核日志菜单边栏选项卡中。 

### <a name="can-i-set-alerts-with-azure-front-door-service"></a>可以设置警报与 Azure 第一道防线服务？

是的 Azure 第一道防线服务支持警报。 警报是针对指标配置的。 

## <a name="next-steps"></a>后续步骤

- 了解如何[创建 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的工作原理](front-door-routing-architecture.md)。