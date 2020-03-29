---
title: 将 Azure API 管理与部署在 Azure 库伯奈斯服务中的微服务结合使用 |微软文档
description: 本文介绍了使用 AKS 部署 API 管理的选项
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/14/2019
ms.author: apimpm
ms.openlocfilehash: 1d6773b4daac256234c33bf50fb3736d585ac505
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75480991"
---
# <a name="use-azure-api-management-with-microservices-deployed-in-azure-kubernetes-service"></a>将 Azure API 管理与部署在 Azure 库伯奈斯服务中的微服务一起使用

微服务非常适合构建 API。 借助[Azure 库伯奈斯服务](https://azure.microsoft.com/services/kubernetes-service/)（AKS），您可以在云中快速部署和操作[基于微服务的体系结构](https://docs.microsoft.com/azure/architecture/guide/architecture-styles/microservices)。 然后，可以利用[Azure API 管理](https://aka.ms/apimrocks)（API 管理）将微服务发布为 API，以便内部和外部使用。 本文介绍了使用 AKS 部署 API 管理的选项。 它假定 Kubernetes、API 管理和 Azure 网络的基本知识。 

## <a name="background"></a>背景

将微服务发布为 API 以进行消费时，管理微服务与使用它们的客户端之间的通信可能具有挑战性。 存在许多贯穿各领域的问题，如身份验证、授权、限制、缓存、转换和监视。 无论微服务是向内部客户端还是外部客户端公开，这些顾虑都有效。 

[API 网关](https://docs.microsoft.com/dotnet/architecture/microservices/architect-microservice-container-applications/direct-client-to-microservice-communication-versus-the-api-gateway-pattern)模式解决了这些问题。 API 网关充当微服务的前门，将客户端与微服务分离，增加一层安全性，并通过消除处理交叉问题的负担来降低微服务的复杂性。 

[Azure API 管理](https://aka.ms/apimrocks)是解决 API 网关需求的交钥匙解决方案。 您可以快速为微服务创建一致且现代的网关，并将其发布为 API。 作为全生命周期 API 管理解决方案，它还提供了其他功能，包括用于 API 发现、API 生命周期管理和 API 分析的自助服务开发人员门户。

当同时使用时，AKS 和 API 管理提供了一个平台，用于部署、发布、保护、监视和管理基于微服务的 API。 在本文中，我们将介绍一些与 API 管理一起部署 AKS 的选项。 

## <a name="kubernetes-services-and-apis"></a>库伯奈斯服务和 API

在 Kubernetes 群集中，容器部署在[Pods](https://kubernetes.io/docs/concepts/workloads/pods/pod/)中，这些容器是短暂的，并且具有生命周期。 当辅助节点死亡时，在节点上运行的 Pod 将丢失。 因此，Pod 的 IP 地址可以随时更改。 我们不能依靠它与吊舱通信。 

为了解决这个问题，库伯内斯引入了[服务](https://kubernetes.io/docs/concepts/services-networking/service/)的概念。 Kubernetes 服务是一个抽象层，它定义一个 Pod 的逻辑组，并为这些 Pod 提供外部流量暴露、负载平衡和服务发现。 

当我们准备好通过 API 管理将微服务作为 API 发布时，我们需要考虑如何将库伯奈斯中的服务映射到 API 管理中的 API。 没有设置的规则。 这取决于您一开始就如何设计和将业务功能或域分区为微服务。 例如，如果服务后面的窗格负责给定资源（例如客户）的所有操作，则服务可能映射到一个 API。 如果将资源上的操作划分为多个微服务（例如 GetOrder、PlaceOrder），则多个服务可以在 API 管理中逻辑聚合到单个 API 中（参见图 1）。 

映射也可以发展。 由于 API 管理在微服务前面创建了一个立面，因此随着时间的推移，它使我们能够重构和调整微服务的大小。 

![将服务映射到 API](./media/api-management-aks/service-api-mapping.png)

## <a name="deploy-api-management-in-front-of-aks"></a>在 AKS 前面部署 API 管理

在 AKS 群集前面部署 API 管理有几个选项。 

虽然 AKS 群集始终部署在虚拟网络 （VNet） 中，但 API 管理实例不需要在 VNet 中部署。 当 API 管理不驻留在群集 VNet 中时，AKS 群集必须发布公共终结点，以便 API 管理连接到。 在这种情况下，需要保护 API 管理和 AKS 之间的连接。 换句话说，我们需要确保只能通过 API 管理专门访问群集。 让我们来讨论一下这些选项。 

### <a name="option-1-expose-services-publicly"></a>选项 1：公开公开服务

可以使用节点端口、负载平衡器或外部名称[的服务类型](https://docs.microsoft.com/azure/aks/concepts-network)公开公开 AKS 群集中的服务。 在这种情况下，服务可以直接从公共互联网访问。 在群集前面部署 API 管理后，我们需要通过在微服务中应用身份验证来确保所有入站流量都通过 API 管理。 例如，API 管理可以在对群集的每个请求中包含一个访问令牌。 每个微服务负责在处理请求之前验证令牌。 


这可能是在 AKS 前面部署 API 管理最简单的选项，尤其是在微服务中已经实现了身份验证逻辑时。 

![直接发布服务](./media/api-management-aks/direct.png)

优点：
* API 管理端的配置简单，因为它不需要注入群集 VNet
* 如果服务已公开公开且微服务中已存在身份验证逻辑，则 AKS 端没有更改

缺点：
* 由于服务终结点的公共可见性，存在潜在的安全风险
* 入站群集流量没有单入口点
* 使用重复身份验证逻辑使微服务复杂化

### <a name="option-2-install-an-ingress-controller"></a>选项 2：安装入口控制器

虽然选项 1 可能更容易，但它具有上述明显的缺点。 如果 API 管理实例不驻留在群集 VNet 中，则相互 TLS 身份验证 （mTLS） 是确保流量在 API 管理实例和 AKS 群集之间的双向可靠和受信任的可靠方法。 

互 TLS 身份验证由 API 管理[本机支持](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)，可以通过[安装入口控制器](https://docs.microsoft.com/azure/aks/ingress-own-tls)在库伯内斯启用（图 3）。 因此，身份验证将在入口控制器中执行，从而简化了微服务。 此外，您可以将 API 管理的 IP 地址添加到入口允许的列表中，以确保只有 API 管理才能访问群集。  

 
![通过入口控制器发布](./media/api-management-aks/ingress-controller.png)


优点：
* API 管理端的易于配置，因为它不需要注入群集 VNet，并且 mTLS 受本机支持
* 在入口控制器层集中保护入站群集流量
* 通过最小化公开可见的群集终结点来降低安全风险

缺点：
* 由于安装、配置和维护入口控制器和管理用于 mTLS 的证书的额外工作，增加了群集配置的复杂性
* 入口控制器终结点的公共可见性导致安全风险


通过 API 管理发布 API 时，使用订阅密钥保护对这些 API 的访问容易且常见。 需要使用已发布 API 的开发人员在调用这些 API 时必须在 HTTP 请求中包括一个有效的订阅密钥。 否则，API 管理网关会立即拒绝调用。 不会将它们转发到后端服务。

若要获取访问 API 所需的订阅密钥，必须拥有订阅。 订阅实质上是一个已命名的容器，该容器包含一对订阅密钥。 需要使用已发布 API 的开发人员可以获取订阅。 不需要 API 发布者批准。 API 发布者也可以直接为 API 使用者创建订阅。

### <a name="option-3-deploy-apim-inside-the-cluster-vnet"></a>选项 3：在群集 VNet 中部署 APIM

在某些情况下，具有法规约束或严格安全要求的客户可能会发现选项 1 和选项 2 由于公开公开的终结点而不可行的解决方案。 在另一些方面，AKS 群集和使用微服务的应用程序可能驻留在同一 VNet 中，因此没有理由公开群集，因为所有 API 流量都将保留在 VNet 中。 对于这些方案，可以将 API 管理部署到群集 VNet 中。 [API 管理高级层](https://aka.ms/apimpricing)支持 VNet 部署。 

将[API 管理部署到 VNet](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet)和外部和内部有两种模式。 

如果 API 使用者不驻留在群集 VNet 中，则应使用外部模式（图 4）。 在此模式下，API 管理网关被注入群集 VNet，但可通过外部负载均衡器从公共 Internet 访问。 它有助于完全隐藏群集，同时仍然允许外部客户端使用微服务。 此外，还可以使用 Azure 网络功能（如网络安全组 （NSG） 来限制网络流量。

![外部 VNet 模式](./media/api-management-aks/vnet-external.png)

如果所有 API 使用者都驻留在群集 VNet 中，则可以使用内部模式（图 5）。 在此模式下，API 管理网关被注入群集 VNET，并且只能通过内部负载均衡器在此 VNet 中访问。 无法从公共 Internet 到达 API 管理网关或 AKS 群集。 

![内部 VNet 模式](./media/api-management-aks/vnet-internal.png)

 在这两种情况下，AKS 群集都不可见。 与选项 2 相比，入口控制器可能不需要。 根据您的方案和配置，API 管理和微服务之间可能仍然需要身份验证。 例如，如果采用服务网格，它始终需要相互 TLS 身份验证。 

优点：
* 最安全的选项，因为 AKS 群集没有公共终结点
* 简化群集配置，因为它没有公共终结点
* 使用内部模式将 API 管理和 AKS 隐藏在 VNet 中的能力
* 使用 Azure 网络功能（如网络安全组 （NSG））控制网络流量的能力

缺点：
* 提高在 VNet 内部署和配置 API 管理的复杂性

## <a name="next-steps"></a>后续步骤

* 了解有关[AKS 中应用的网络概念的更多信息](https://docs.microsoft.com/azure/aks/concepts-network)
* 了解有关如何使用[API 管理与虚拟网络](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet)有关





