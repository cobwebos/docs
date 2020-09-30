---
title: Azure 前端-通过 Azure 的应用程序交付套件实现负载平衡 |Microsoft Docs
description: 本文将帮助你了解 Azure 如何向其应用程序交付套件提供负载均衡
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 50e047325ad17710794b1640715ab1938373fe85
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91542166"
---
# <a name="load-balancing-with-azures-application-delivery-suite"></a>使用 Azure 的应用程序传送套件进行负载均衡

## <a name="introduction"></a>简介
Microsoft Azure 提供各种全局和区域服务，用于管理网络流量的分布式和负载平衡方式： 

* 应用程序网关
* Front Door 
* 负载均衡器  
* 流量管理器

除了 Azure 的许多区域和区域体系结构外，通过结合使用这些服务，你可以构建强大、可缩放且高性能的应用程序。

:::image type="content" source="./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure1.png" alt-text="应用程序传送套件":::
 
这些服务分为两个类别：
1. 流量管理器和前端等**全局负载平衡服务**将来自你的最终用户的流量分布到各个云甚至你的混合本地服务中。 全局负载均衡将流量路由到最近的服务后端，并对服务可靠性的更改进行响应，以维持用户的 always on 可用性和高性能。 
1. **区域负载平衡服务** （例如负载均衡器和应用程序网关）可以将流量分配到虚拟网络中的虚拟机)  (虚拟网络 (vnet) 或区域内的服务终结点。

当你组合这些全局和区域服务时，你的应用程序将从你的最终用户发送到 IaaS、PaaS 或本地服务的可靠且安全的端到端流量中获益。 下一部分介绍上述各项服务。

## <a name="global-load-balancing"></a>全局负载均衡
**流量管理器** 提供全局 DNS 负载均衡。 它查看传入的 DNS 请求，并按照客户选择的路由策略以正常的后端响应。 路由方法选项包括：
- * * 性能路由将请求发送到延迟最少的最近的后端。
- **优先级路由** 将所有流量直接传递到后端，并使用其他后端作为备份。
- **加权轮循机制路由** 根据分配给每个后端的权重分配流量。
- **地理路由** 可确保来自特定地理区域的请求可以由为这些区域映射的后端处理。  (例如，来自西班牙的所有请求应定向到法国中央 Azure 区域) 
- 通过**子网路由**，你可以将 IP 地址范围映射到后端，以便将这些 ip 的传入请求发送到特定的后端。 例如， (从公司总部的 IP 地址范围进行连接的任何用户都应该获取不同于一般用户的 web 内容) 

客户端直接连接到该后端。 当某个后端运行不正常时，Azure 流量管理器可以检测到这种状态，然后将客户端重定向到另一个正常的实例。 有关服务的详细信息，请参阅 [Azure 流量管理器](../traffic-manager/traffic-manager-overview.md) 文档。

**Azure 前门** 提供动态网站加速 (DSA) 包含全局 HTTP 负载平衡。  它着眼于为指定的主机名、URL 路径和配置的规则路由到最近后端/区域的传入 HTTP 请求。  
Front Door 在 Microsoft 网络边缘终止 HTTP 请求，并主动进行探测以检测应用程序或基础结构在运行状况或延迟方面的变化。  然后始终将流量路由到可用（正常运行）的最远后端。 参阅 Front Door 的[路由基础结构](front-door-routing-architecture.md)详细信息以及[流量路由方法](front-door-routing-methods.md)深入了解该服务。

## <a name="regional-load-balancing"></a>区域负载均衡
应用程序网关以服务形式提供应用程序传送控制器 (ADC)，借此为应用程序提供许多第 7 层负载均衡功能。 客户可以用它将 CPU 密集型 TLS 终止卸载到应用程序网关，优化 Web 场工作效率。 其他其他第7层路由功能还包括传入流量的轮循机制分配、基于 cookie 的会话相关性、基于 URL 路径的路由，以及在单个应用程序网关后面托管多个网站的能力。 可将应用程序网关配置为面向 Internet 的终结点、仅限内部的终结点，或两者的组合。 应用程序网关完全受 Azure 管理，为你提供可伸缩性和高可用性。 它提供丰富的诊断和日志记录功能以改进可管理性。

负载均衡器是 Azure SDN 堆栈的有机组成部分，为你提供高性能、低延迟的第4层负载均衡服务，适用于所有 UDP 和 TCP 协议。 可以通过定义将入站连接映射到后端池的规则来配置公共或内部负载均衡的终结点。 通过使用 TCP 或 HTTPS 的运行状况探测监视，可帮助你管理服务可用性。

## <a name="choosing-a-global-load-balancer"></a>选择全局负载均衡器
选择是将负载均衡器还是 Azure Front Door 用作全局负载均衡器时，应考虑这两个服务的异同点。   它们均提供以下功能：
- **多地域冗余：** 如果一个区域出现服务中断，流量无缝路由到最近的区域，无需应用程序所有者介入。
- **最近区域路由：** 自动将流量路由到最近的区域

</br>下表描述了流量管理器与 Azure 前门之间的差异：</br>

| 流量管理器 | Azure Front Door |
| --------------- | ------------------------ |
|**任何协议：** 由于流量管理器在 DNS 层工作，因此你可以路由任意类型的网络流量;HTTP、TCP、UDP 等。 | **HTTP 加速：** 如果是前门，则会将流量代理到 Microsoft 网络的边缘。 HTTP/S 请求将会看到延迟和吞吐量改进，从而减少了 TLS 协商的延迟。|
|**本地路由：** 当路由位于 DNS 层时，流量始终从点到点进行。  从分支机构到本地数据中心的路由可以采用直接路径;甚至在你自己的网络上使用流量管理器。 | **独立的可伸缩性：** 由于前门适用于 HTTP 请求，因此可以根据规则和每个应用程序微服务的运行状况，将针对不同 URL 路径的请求路由到不同的后端/区域服务池 (微服务) 。|
|**** 计费形式：基于 DNS 的计费随用户数增加，适用于具有更多用户和更高稳定性的服务，从而为使用率较高的情况降低费用。 |**内联安全性：** Front Door 启用流量限速和 IP ACL 等规则，使你能在流量达到应用程序前保护后端。 

</br>我们建议客户对其 HTTP 工作负荷使用前门，因为 HTTP 与前门一起工作的性能、可操作性和安全优势。 流量管理器和 Front Door 可并行使用，为应用程序提供全部流量。 

## <a name="building-with-azures-application-delivery-suite"></a>使用 Azure 的应用程序传送套件进行生成 
建议所有网站、Api、服务在地理上都是冗余的，因此，只要可能，它就可以从最近的位置向其用户提供流量。  结合多个负载均衡服务，可以构建地理和区域冗余，以最大程度地提高可靠性和性能。

在下图中，我们将介绍一个示例体系结构，它使用所有这些服务的组合来构建全局 web 服务。 结构设计师使用流量管理器将流量路由到全局后端进行文件和对象传递。 使用前门时，将与模式/store/* 匹配的 URL 路径全局路由到它们迁移到应用服务的服务。 最后，将所有其他请求路由到区域应用程序网关。

在 IaaS 服务的每个区域中，应用程序开发人员已决定与模式/images/* 匹配的所有 Url 都从专用的 Vm 池提供。 此 Vm 池不同于 web 场的其余部分。

此外，提供动态内容的默认 VM 池需要与高可用性群集上托管的后端数据库通信。 整个部署通过 Azure 资源管理器进行配置。

下图显示了此方案的体系结构：

:::image type="content" source="./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure2.png" alt-text="应用程序传送套件":::

> [!NOTE]
> 此示例只是 Azure 提供的负载均衡服务的众多可能配置之一。 可采用最符合负载均衡需求的方式，混合搭配流量管理器、Front Door、应用程序网关和负载均衡器。 例如，如果不需要进行 TLS/SSL 卸载或第7层处理，则可以使用负载均衡器来代替应用程序网关。

## <a name="next-steps"></a>后续步骤

- 了解如何[创建 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的工作原理](front-door-routing-architecture.md)。
