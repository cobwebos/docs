---
title: Azure Front Door | Microsoft Docs
description: 本文提供 Azure Front Door 的概述。
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/27/2020
ms.author: duau
ms.openlocfilehash: e5b8e5059d4d7df8d7061698ea29d7a6785236f9
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2020
ms.locfileid: "91825644"
---
# <a name="what-is-azure-front-door"></a>什么是 Azure Front Door？

Azure Front Door 是可缩放的全局入口点，它使用 Microsoft 全球边缘网络来创建快速、安全且可大规模缩放的 Web 应用程序。 使用 Front Door，可将全球使用者应用程序和企业应用程序转换为可靠、高性能且个性化的新型应用程序，其内容可以通过 Azure 向全球受众提供。

<p align="center">
  <img src="./media/front-door-overview/front-door-visual-diagram.png" alt="Front Door architecture" width="600" title="Azure Front Door">
</p>

Front Door 在第 7 层（HTTP/HTTPS 层）工作，将任意广播协议与拆分 TCP 和 Microsoft 全球网络配合使用来改善全球连接。 根据路由方法，可以确保 Front Door 将客户端请求路由到最快且可用性最高的应用程序后端。 应用程序后端是托管在 Azure 内部或外部的任何面向 Internet 的服务。 Front Door 提供了多种[流量路由方法](front-door-routing-methods.md)和[后端运行状况监视选项](front-door-health-probes.md)来满足不同应用程序需求和自动故障转移方案。 与[流量管理器](../traffic-manager/traffic-manager-overview.md)类似，Front Door 可以灵活应对故障，包括整个 Azure 区域的故障。

>[!NOTE]
> Azure 为方案提供了一套完全托管的负载均衡解决方案。 
> * 若要进行基于 DNS 的全局路由，并且在传输层安全性 (TLS) 协议终止（“SSL 卸载”）、每 HTTP/HTTPS 请求或应用程序层处理方面没有要求，请查看[流量管理器](../traffic-manager/traffic-manager-overview.md)。 
> * 若要在应用程序层对某个区域内的多个服务器实现负载均衡，请查看[应用程序网关](../application-gateway/application-gateway-introduction.md)
> * 若要执行网络层负载均衡，请查看[负载均衡器](../load-balancer/load-balancer-overview.md)。 
> 
> 端到端场景可从结合所需的解决方案中受益。
> 有关 Azure 负载平衡选项的比较，请参阅 [Overview of load-balancing options in Azure](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview)（Azure 中的负载平衡选项概述）。

## <a name="why-use-azure-front-door"></a>为什么使用 Azure Front Door？

使用 Front Door 可以生成、运行和横向扩展动态 Web 应用程序与静态内容。 使用 Front Door，可以通过快速全局故障转移优化顶级最终用户的性能和可靠性，为你的 Web 流量定义、管理和监视全局路由。

Front Door 带有的主要功能：

* 使用基于[拆分 TCP](front-door-routing-architecture.md#splittcp)的[任意广播协议](front-door-routing-architecture.md#anycast)提高应用程序性能。

* 智能[运行状况探测](front-door-health-probes.md)，用于监视对后端资源。

*  对请求进行[基于 URL 路径的](front-door-route-matching.md)路由。

* 允许托管多个网站以实现高效的应用程序基础结构。 

* 基于 Cookie 的[会话关联](front-door-routing-methods.md#affinity)。

* [SSL 卸载](front-door-custom-domain-https.md)和证书管理。

* 定义自己的[自定义域](front-door-custom-domain.md)。 

* 集成了 [Web 应用程序防火墙 (WAF)](../web-application-firewall/overview.md) 的应用程序安全性。

* 使用 [URL 重定向](front-door-url-redirect.md)将 HTTP 流量重定向到 HTTPS。

* 自定义转发路径与 [URL 重写](front-door-url-rewrite.md)。

* 对端到端 IPv6 连接和 [HTTP/2 协议](front-door-http2.md)的原生支持。

## <a name="pricing"></a>定价

有关定价信息，请参阅 [Front Door 定价](https://azure.microsoft.com/pricing/details/frontdoor/)。 请参阅 [Azure Front Door 的 SLA](https://azure.microsoft.com/en-us/support/legal/sla/frontdoor/v1_0/)。

## <a name="whats-new"></a>新增功能

订阅 RSS 源，并在 [Azure 更新](https://azure.microsoft.com/updates/?category=networking&query=Azure%20Front%20Door)页上查看最新的 Azure Front Door 功能更新。

## <a name="next-steps"></a>后续步骤

- 了解如何[创建 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的工作原理](front-door-routing-architecture.md)。
