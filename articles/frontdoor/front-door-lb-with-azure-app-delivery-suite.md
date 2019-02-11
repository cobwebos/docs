---
title: Azure Front Door 服务 - 通过 Azure 的应用程序交付套件进行负载均衡 | Microsoft Docs
description: 本文介绍 Azure 建议通过其应用程序传送套件进行负载均衡
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 5403b5506a3758ede5ad06640335b873b6b9aa96
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2019
ms.locfileid: "54820812"
---
# <a name="load-balancing-with-azures-application-delivery-suite"></a>使用 Azure 的应用程序传送套件进行负载均衡

## <a name="introduction"></a>介绍
Microsoft Azure 提供了多种全球和区域服务，用于管理网络流量的分布和负载均衡方式：流量管理器、Front Door 服务、应用程序网关和负载均衡器。  将这些服务与 Azure 的许多区域和分区体系结构一起使用，能够生成可靠且可缩放的高性能应用程序。

![应用程序传送套件 ][1]
 
这些服务分为两个类别：
1. **负载均衡服务**（例如流量管理器和 Front Door），此类服务将最终用户的流量跨区域后端、云或混合本地服务分布。 全局负载均衡将流量路由到最近的服务后端，并对服务可靠性或性能方面的更改作出响应，从而为用户提供始终可用的最佳性能。 
2. **区域负载均衡服务**（例如标准负载均衡器或应用程序网关），此类服务能够将虚拟网络 (VNET) 内的流量跨区域内的虚拟机 (VM) 或分区服务终结点分布。

将应用程序中的全局或区域服务结合，可提供一种在用户与 IaaS、PaaS 或本地服务之间路由流量的端到端方式，这种方式可靠、性能好、安全性高。 下一部分介绍上述各项服务。

## <a name="global-load-balancing"></a>全局负载均衡
**流量管理器**提供全局 DNS 负载均衡。 它着眼于传入的 DNS 请求，并根据客户选择的路由策略使用正常的后端进行响应。 路由方法选项包括：
- 性能路由 - 根据延迟将请求者发送到最近的后端。
- 优先级路由 - 将所有流量定向到一个后端，其他后端作为备份。
- 加权轮循机制路由 - 根据分配给每个后端的权重进行流量分布。
- 地理路由 - 确保将位于特定地理区域中的请求者定向到映射到那些区域的后端（例如，所有位于西班牙的请求者都将被定向到法国中部这一个 Azure 区域）
- 子网路由 - 能够将 IP 地址范围映射到后端，从而将这些范围内的请求者发送到指定后端（例如，从公司总部的 IP 地址范围中进行连接的所有用户得到的 Web 内容与普通用户不同）

客户端直接连接到该后端。 当某个后端运行不正常时，Azure 流量管理器可以检测到这种状态，然后将客户端重定向到另一个正常的实例。 若要详细了解此服务，请参阅 [Azure 流量管理器文档](../traffic-manager/traffic-manager-overview.md)。

**Azure Front Door 服务**提供包括全局 HTTP 负载平衡在内的动态网站加速 (DSA)。  它着眼于为指定的主机名、URL 路径和配置的规则路由到最近后端/区域的传入 HTTP 请求。  
Front Door 在 Microsoft 网络边缘终止 HTTP 请求，并主动进行探测以检测应用程序或基础结构在运行状况或延迟方面的变化。  然后始终将流量路由到可用（正常运行）的最远后端。 参阅 Front Door 的[路由基础结构](front-door-routing-architecture.md)详细信息以及[流量路由方法](front-door-routing-methods.md)深入了解该服务。

## <a name="regional-load-balancing"></a>区域负载均衡
应用程序网关以服务形式提供应用程序传送控制器 (ADC)，借此为应用程序提供许多第 7 层负载均衡功能。 客户可以用它将 CPU 密集型 SSL 终止卸载到应用程序网关，优化 Web 场工作效率。 其他第 7 层路由功能包括传入流量的轮循机制分布、基于 Cookie 的会话相关性、基于 URL 路径的路由，以及在单个应用程序网关后托管多个网站的功能。 可以将应用程序网关配置为面向 Internet 的网关、仅限内部访问的网关或二者合一的网关。 应用程序网关完全受 Azure 管理，可缩放且高度可用。 它提供丰富的诊断和日志记录功能以改进可管理性。
负载均衡器是 Azure SDN 堆栈的组成部分，为所有 UDP 和 TCP 协议提供高性能、低延迟的第 4 层负载均衡服务。 它管理入站和出站连接。 可以配置公共和内部负载均衡终结点，定义规则将入站连接映射到后端池目标，并在其中包含 TCP 和 HTTP 运行状况探测选项来管理服务的可用性。


## <a name="choosing-a-global-load-balancer"></a>选择全局负载均衡器
选择是将负载均衡器还是 Azure Front Door 用作全局负载均衡器时，应考虑这两个服务的异同点。   它们均提供以下功能：
- **多地域冗余：** 如果一个区域出现故障，则将流量无缝路由到最近的区域，无需应用程序所有者做出任何干预。
- **最近区域路由：** 自动将流量路由到最近的区域

</br>下表说明流量管理器和 Azure Front Door 服务之间的差异：</br>

| 流量管理器 | Azure Front Door 服务 |
| --------------- | ------------------------ |
|**任何协议：** 流量管理器在 DNS 层运行，因此可以路由任何类型的网络流量，比如 HTTP、TCP、UDP 等。 | **HTTP 加速：** 在 Microsoft 网络边缘代理 Front Door 流量。  因此，HTTP(S) 请求在延迟和吞吐量方面有所改善，降低了 SSL 协商的延迟并使用热连接从 AFD 连接到应用程序。|
|**本地路由：** 在 DNS 层进行路由，始终点到点地路由流量。  即使在自己的网络上使用流量管理器，从分公司路由到本地数据中心亦可采用一条直接的路径。 | **独立的可伸缩性：** Front Door 适用于 HTTP 请求，因此，可根据各个应用程序微服务的规则和运行状况，将路由到不同 URL 路径的请求路由到不同的后端/区域服务池（微服务）。|
|**计费形式：** 基于 DNS 的计费随用户数增加，适用于具有更多用户和更高稳定性的服务，从而为使用率较高的情况降低费用。 |**内联安全性：** Front Door 启用流量限速和 IP ACL 等规则，使你能在流量达到应用程序前保护后端。 

</br>由于 Front Door 对 HTTP 工作负载具有的性能、可操作性和安全性优势，我们建议客户为其 HTTP 工作负载使用 Front Door。    流量管理器和 Front Door 可并行使用，为应用程序提供全部流量。 

## <a name="building-with-azures-application-delivery-suite"></a>使用 Azure 的应用程序传送套件进行生成 
建议所有网站、API 和服务均为地域冗余的，并在可能的情况下将流量从距用户最近（延迟最低）的位置传送给他们。  将流量管理器、Front Door 服务、应用程序网关和负载均衡器中的服务结合使用，可以生成地域及区域冗余，从而最大限度提高可靠性、规模和性能。

下图展示一个示例服务，它结合使用这些服务来生成一个全局 Web 服务。   其中，架构师使用流量管理器路由到全局后端供存档和对象传送，使用 Front Door 在全局范围内将与模式 /store/* 匹配的 URL 路径路由到他们已迁移到应用服务的服务，并将所有其他请求路由到区域应用程序网关。

在区域中，应用程序开发人员决定通过一个专用的 VM 池（不同于 Web 场中的其他池）来为其 IaaS 服务提供与模式 /images/* 匹配的所有 URL。

此外，提供动态内容的默认 VM 池需要与高可用性群集上托管的后端数据库通信。 整个部署是通过 Azure 资源管理器设置的。

下图展示了此方案的体系结构：

![应用程序传送套件详细体系结构][2] 

> [!NOTE]
> 此示例只是 Azure 提供的负载均衡服务的众多可能配置之一。 可采用最符合负载均衡需求的方式，混合搭配流量管理器、Front Door、应用程序网关和负载均衡器。 例如，如果 SSL 卸载或第 7 层处理没有必要，负载均衡器可以替代应用程序网关。


## <a name="next-steps"></a>后续步骤

- 了解如何[创建 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的工作原理](front-door-routing-architecture.md)。

<!--Image references-->
[1]: ./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure1.png
[2]: ./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure2.png
