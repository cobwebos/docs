---
title: 将 Azure API 管理与 Azure Kubernetes Service 中部署的微服务配合使用 |Microsoft Docs
description: 本文介绍了通过 AKS 部署 API 管理的选项
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
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75480991"
---
# <a name="use-azure-api-management-with-microservices-deployed-in-azure-kubernetes-service"></a>将 Azure API 管理与 Azure Kubernetes 服务中部署的微服务配合使用

微服务非常适合用于生成 Api。 使用[Azure Kubernetes 服务](https://azure.microsoft.com/services/kubernetes-service/)（AKS），可以在云中快速部署和运行[基于微服务的体系结构](https://docs.microsoft.com/azure/architecture/guide/architecture-styles/microservices)。 然后，可以利用[AZURE API 管理](https://aka.ms/apimrocks)（API 管理）将微服务发布为内部和外部使用的 api。 本文介绍了通过 AKS 部署 API 管理的选项。 它假定 Kubernetes、API 管理和 Azure 网络的基本知识。 

## <a name="background"></a>背景

将微服务发布为用于使用的 Api 时，管理微服务和使用它们的客户端之间的通信可能会很困难。 需要考虑多种跨切削问题，例如身份验证、授权、限制、缓存、转换和监视。 不管是否向内部或外部客户端公开微服务，这些问题都是有效的。 

[API 网关](https://docs.microsoft.com/dotnet/architecture/microservices/architect-microservice-container-applications/direct-client-to-microservice-communication-versus-the-api-gateway-pattern)模式解决了这些问题。 API 网关充当微服务的前端，将客户端与微服务分离，增加额外的安全层，并通过消除处理交叉切削问题的负担，降低微服务的复杂性。 

[AZURE Api 管理](https://aka.ms/apimrocks)是一种全包式解决方案，用于解决 API 网关需求。 可以为微服务快速创建一致的新式网关，并将其发布为 Api。 它还提供了其他功能，包括用于 API 发现、API 生命周期管理和 API 分析的自助服务开发人员门户。

结合使用时，AKS 和 API 管理提供了一个平台，用于部署、发布、保护、监视和管理基于微服务的 Api。 在本文中，我们将逐步介绍几个与 API 管理一起部署 AKS 的选项。 

## <a name="kubernetes-services-and-apis"></a>Kubernetes 服务和 Api

在 Kubernetes[群集中，容器部署在 pod](https://kubernetes.io/docs/concepts/workloads/pods/pod/)中，并具有生命周期。 辅助角色节点停止运行时，节点上运行的盒将丢失。 因此，Pod 的 IP 地址可随时更改。 我们不能依靠它来与 pod 通信。 

为了解决此问题，Kubernetes 引入了[服务](https://kubernetes.io/docs/concepts/services-networking/service/)的概念。 Kubernetes 服务是一种抽象层，它定义了 pod 的逻辑组，并为这些 pod 启用了外部流量泄露、负载平衡和服务发现。 

当我们准备通过 API 管理将微服务发布为 Api 时，我们需要考虑如何将 Kubernetes 中的服务映射到 API 管理中的 Api。 没有设置规则。 这取决于你在一开始就将业务功能或域划分到微服务中的方式。 例如，如果服务背后的 pod 负责给定资源的所有操作（例如，客户），则该服务可能会映射到一个 API。 如果对资源的操作分区为多个微服务（例如，GetOrder、PlaceOrder），则可以在 API 管理中将多个服务逻辑聚合为一个 API （请参阅图1）。 

映射也可以发展。 由于 API 管理在微服务的前面创建了一个外观，因此，我们可以在一段时间内重构微服务并调整其大小。 

![将服务映射到 Api](./media/api-management-aks/service-api-mapping.png)

## <a name="deploy-api-management-in-front-of-aks"></a>在 AKS 之前部署 API 管理

有几个选项可用于在 AKS 群集前面部署 API 管理。 

当 AKS 群集始终部署在虚拟网络（VNet）中时，不需要在 VNet 中部署 API 管理实例。 如果 API 管理不在群集 VNet 中，AKS 群集必须发布公共终结点，以便 API 管理连接到。 在这种情况下，需要保护 API 管理和 AKS 之间的连接。 换句话说，我们需要确保群集只能通过 API 管理以独占方式访问。 我们来看一下这些选项。 

### <a name="option-1-expose-services-publicly"></a>选项1：公开公开服务

可以使用 NodePort、LoadBalancer 或 ExternalName[服务类型](https://docs.microsoft.com/azure/aks/concepts-network)公开公开 AKS 群集中的服务。 在这种情况下，可以直接从公共 internet 访问服务。 将 API 管理部署到群集之前，我们需要确保所有入站流量通过 API 管理，方法是在微服务中应用身份验证。 例如，API 管理可以在对群集发出的每个请求中包含一个访问令牌。 每个微服务负责在处理请求之前验证令牌。 


这可能是在 AKS 之前部署 API 管理的最简单选项，尤其是在微服务中实现了身份验证逻辑时。 

![直接发布服务](./media/api-management-aks/direct.png)

优点：
* API 管理端上的轻松配置，因为无需注入群集 VNet
* 如果服务已公开公开并且微服务中已存在身份验证逻辑，则无 AKS 端的更改。

缺点：
* 服务终结点的公共可见性导致的潜在安全风险
* 没有入站群集流量的单一入口点
* 利用重复的身份验证逻辑使微服务复杂化

### <a name="option-2-install-an-ingress-controller"></a>选项2：安装入口控制器

尽管选项1可能更简单，但它具有上面提到的显著缺点。 如果某个 API 管理实例不在群集 VNet 中，则相互 TLS 身份验证（mTLS）是一种可靠的方法，可确保在 API 管理实例和 AKS 群集之间两个方向的流量是安全和信任的。 

API 管理在[本机上支持](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)相互 TLS 身份验证，并且可以通过[安装入口控制器](https://docs.microsoft.com/azure/aks/ingress-own-tls)在 Kubernetes 中启用该身份验证（图3）。 因此，将在入口控制器中执行身份验证，这将简化微服务。 此外，可以通过入口将 API 管理的 IP 地址添加到允许列表，以确保只有 API 管理可以访问群集。  

 
![通过入口控制器发布](./media/api-management-aks/ingress-controller.png)


优点：
* API 管理端上的轻松配置，因为无需注入群集 VNet，而 mTLS 是本机支持的
* 为入口控制器层上的入站群集流量集中保护
* 通过最大程度减少公开可见的群集终结点来降低安全风险

缺点：
* 增加群集配置的复杂性，原因是安装、配置和维护入口控制器并管理用于 mTLS 的证书
* 由于入口控制器终结点公开可见的安全风险


通过 API 管理发布 API 时，使用订阅密钥保护对这些 API 的访问容易且常见。 需要使用已发布 API 的开发人员在调用这些 API 时必须在 HTTP 请求中包括一个有效的订阅密钥。 否则，API 管理网关会立即拒绝调用。 不会将它们转发到后端服务。

若要获取访问 API 所需的订阅密钥，必须拥有订阅。 订阅实质上是一个已命名的容器，该容器包含一对订阅密钥。 需要使用已发布 API 的开发人员可以获取订阅。 不需要 API 发布者批准。 API 发布者也可以直接为 API 使用者创建订阅。

### <a name="option-3-deploy-apim-inside-the-cluster-vnet"></a>选项3：在群集 VNet 内部署 APIM

在某些情况下，由于公开的终结点，具有法规约束或严格安全要求的客户可能会找到选项1和2不可行的解决方案。 在其他情况下，AKS 群集和使用微服务的应用程序可能驻留在同一 VNet 中，因此没有理由公开群集，因为所有 API 流量将保留在 VNet 中。 在这些情况下，你可以将 API 管理部署到群集 VNet 中。 [API 管理高级层](https://aka.ms/apimpricing)支持 VNet 部署。 

将[API 管理部署到 VNet 中](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet)的模式有两种：外部和内部。 

如果 API 使用者不在群集 VNet 中，则应使用外部模式（图4）。 在此模式下，API 管理网关注入群集 VNet，但可通过外部负载均衡器从公共 internet 访问。 它有助于完全隐藏群集，同时仍允许外部客户端使用微服务。 此外，还可以使用 Azure 网络功能，例如网络安全组（NSG）来限制网络流量。

![外部 VNet 模式](./media/api-management-aks/vnet-external.png)

如果所有 API 使用者都位于群集 VNet 中，则可以使用内部模式（图5）。 在此模式下，API 管理网关注入群集 VNET，只能通过内部负载均衡器在此 VNet 内访问。 无法从公共 internet 访问 API 管理网关或 AKS 群集。 

![内部 VNet 模式](./media/api-management-aks/vnet-internal.png)

 在这两种情况下，AKS 群集都不公开可见。 与选项2相比，入口控制器可能不是必需的。 根据你的方案和配置，可能仍需要在 API 管理与微服务之间进行身份验证。 例如，如果采用服务网格，则它始终需要相互 TLS 身份验证。 

优点：
* 最安全的选项，因为 AKS 群集没有公共终结点
* 简化群集配置，因为它没有公共终结点
* 能够使用内部模式在 VNet 中隐藏 API 管理和 AKS
* 使用 Azure 网络功能（如网络安全组（NSG））控制网络流量的能力

缺点：
* 增加在 VNet 中部署和配置 API 管理的复杂性

## <a name="next-steps"></a>后续步骤

* 详细了解[AKS 中应用程序的网络概念](https://docs.microsoft.com/azure/aks/concepts-network)
* 详细了解[如何在虚拟网络中使用 API 管理](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet)





