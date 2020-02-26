---
title: 概念 - Azure Kubernetes 服务 (AKS) 中的网络
description: 了解 Azure Kubernetes 服务 (AKS) 中的网络，包括 kubenet 和 Azure CNI、入口控制器、负载均衡器和静态 IP 地址。
ms.topic: conceptual
ms.date: 02/28/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 5800254ab44b5b0f1048ce2200f90c06a8d1666a
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77596158"
---
# <a name="network-concepts-for-applications-in-azure-kubernetes-service-aks"></a>Azure Kubernetes 服务 (AKS) 中应用程序的网络概念

在基于容器的微服务应用程序开发方法中，应用程序组件必须协同工作才能处理其任务。 Kubernetes 提供支持此应用程序通信的各种资源。 可以在内部或外部连接应用程序并将其公开。 要生成高可用性应用程序，可以对应用程序进行负载均衡。 更复杂的应用程序可能需要配置 SSL/TLS 终止的入口流量或多个组件的路由。 出于安全考虑，可能还需要限制流量流入或 Pod 和节点之间的流量。

本文介绍了为 AKS 中的应用程序提供网络的核心概念：

- [服务](#services)
- [Azure 虚拟网络](#azure-virtual-networks)
- [入口控制器](#ingress-controllers)
- [网络策略](#network-policies)

## <a name="kubernetes-basics"></a>Kubernetes 基础知识

为允许访问应用程序或让应用程序组件相互通信，Kubernetes 为虚拟网络提供了抽象层。 Kubernetes 节点连接到虚拟网络，可为 Pod 提供入站和出站连接。 kube-proxy 组件在每个节点上运行，以提供这些网络功能。

在 Kubernetes 中，服务以逻辑方式对 Pod 进行分组，以允许通过 IP 地址或 DNS 名称以及特定端口进行直接访问。 此外，还可使用负载均衡器分发流量。 使用入口控制器也可实现更复杂的应用程序流量路由。 使用 Kubernetes 网络策略可提供安全性，还可筛选 Pod 网络流量。

Azure 平台还有助于简化 AKS 群集的虚拟网络。 创建 Kubernetes 负载均衡器时，将创建和配置基础 Azure 负载均衡器资源。 打开 Pod 的网络端口时，会配置相应的 Azure 网络安全组规则。 对于 HTTP 应用程序路由，Azure 还可以在配置新的入口路由时配置外部 DNS。

## <a name="services"></a>服务

为简化应用程序工作负载的网络配置，Kubernetes 使用服务以逻辑方式对一组 Pod 进行分组并提供网络连接。 可用的服务类型如下：

- **群集 IP** - 创建在 AKS 群集中使用的内部 IP 地址。 适用于支持群集中其他工作负载的仅限内部使用的应用程序。

    ![显示 AKS 群集中群集 IP 流量的示意图][aks-clusterip]

- **NodePort** - 在基础节点上创建端口映射，该映射允许使用节点 IP 地址和端口直接访问应用程序。

    ![显示 AKS 群集中 NodePort 流量的示意图][aks-nodeport]

- **LoadBalancer** - 创建 Azure 负载均衡器资源、配置外部 IP 地址并将请求的 Pod 连接到负载均衡器后端池。 为了使客户的流量能够访问应用程序，会在所需端口上创建负载均衡规则。 

    ![显示 AKS 群集中负载均衡器流量的示意图][aks-loadbalancer]

    针对入站流量的其他控制和路由，可能要改用[入口控制器](#ingress-controllers)。

- **ExternalName** - 创建特定的 DNS 条目，便于访问应用程序。

可以动态分配负载均衡器和服务的 IP 地址，也可以指定要使用的现有静态 IP 地址。 可以分配内部和外部静态 IP 地址。 这个现有静态 IP 地址通常与 DNS 条目绑定。

可以创建内部和外部负载均衡器。 内部负载均衡器只分配有专用 IP 地址，因此不能从 Internet 访问它们。

## <a name="azure-virtual-networks"></a>Azure 虚拟网络

在 AKS 中，可以部署使用以下两种网络模型之一的群集：

- Kubenet 网络 - 通常在部署 AKS 群集时创建和配置网络资源。
- Azure 容器网络接口 (CNI) 网络 - AKS 群集连接到现有的虚拟网络资源和配置。

### <a name="kubenet-basic-networking"></a>Kubenet（基本）网络

kubenet 网络选项是用于创建 AKS 群集的默认配置。 节点使用 *kubenet* 从 Azure 虚拟网络子网获取 IP 地址。 Pod 接收从逻辑上不同的地址空间到节点的 Azure 虚拟网络子网的 IP 地址。 然后配置网络地址转换 (NAT)，以便 Pod 可以访问 Azure 虚拟网络上的资源。 流量的源 IP 地址通过 NAT 转换为节点的主 IP 地址。

节点使用[kubenet][kubenet] Kubernetes 插件。 可以让 Azure 平台创建和配置虚拟网络，或选择将 AKS 群集部署到现有虚拟网络子网中。 同样，只有节点会接收可路由的 IP 地址，并且 pod 使用 NAT 与 AKS 群集之外的其他资源进行通信。 这种方法大大减少了需要在网络空间中保留供 Pod 使用的 IP 地址数量。

有关详细信息，请参阅[Configure kubenet 联网 for a AKS cluster][aks-configure-kubenet-networking]。

### <a name="azure-cni-advanced-networking"></a>Azure CNI（高级）网络

借助 Azure CNI，每个 pod 都可以从子网获取 IP 地址，并且可以直接访问。 这些 IP 地址在网络空间中必须唯一，并且必须事先计划。 每个节点都有一个配置参数来表示它支持的最大 Pod 数。 这样，就会为每个节点预留相应的 IP 地址数。 这种方法需要进行更多的规划，因为这可能会导致 IP 地址耗尽，或者需要在较大的子网中重建群集，因为应用程序需求会增长。

节点使用[Azure 容器网络接口（CNI）][cni-networking] Kubernetes 插件。

![显示两个节点的示意图，其中的网桥将每个节点连接到单个 Azure VNet][advanced-networking-diagram]

有关详细信息，请参阅[Configure AZURE CNI for a AKS cluster][aks-configure-advanced-networking]。

### <a name="compare-network-models"></a>比较网络模型

Kubenet 和 Azure CNI 都为你的 AKS 群集提供网络连接。 不过，每种情况都有各自的优点和缺点。 在高级别上，需要注意以下事项：

* **kubenet**
    * 节省 IP 地址空间。
    * 使用 Kubernetes 内部或外部负载均衡器从群集外部到达 pod。
    * 你必须手动管理和维护用户定义的路由（Udr）。
    * 每个群集最多400个节点。
* **Azure CNI**
    * Pod 可获取完全虚拟网络连接，并可通过连接的网络通过其专用 IP 地址直接访问。
    * 需要更多 IP 地址空间。

Kubenet 和 Azure CNI 之间存在以下行为差异：

| 功能                                                                                   | Kubenet   | Azure CNI |
|----------------------------------------------------------------------------------------------|-----------|-----------|
| 在现有的或新的虚拟网络中部署群集                                            | 已支持-Udr 手动应用 | 支持 |
| Pod-pod 连接                                                                         | 支持 | 支持 |
| Pod-VM 连接;同一虚拟网络中的 VM                                          | 由 pod 启动时工作 | 采用两种方式 |
| Pod-VM 连接;对等互连虚拟网络中的 VM                                            | 由 pod 启动时工作 | 采用两种方式 |
| 使用 VPN 或 Express Route 进行本地访问                                                | 由 pod 启动时工作 | 采用两种方式 |
| 访问由服务终结点保护的资源                                             | 支持 | 支持 |
| 使用负载均衡器服务、应用程序网关或入口控制器公开 Kubernetes 服务 | 支持 | 支持 |
| 默认 Azure DNS 和专用区域                                                          | 支持 | 支持 |

关于 DNS，同时使用 kubenet 和 Azure CNI 插件 DNS 由 CoreDNS 提供，后者是运行在 AKS 中的守护程序集。 有关 Kubernetes 上的 CoreDNS 的详细信息，请参阅[自定义 DNS 服务](https://kubernetes.io/docs/tasks/administer-cluster/dns-custom-nameservers/)。 默认情况下，CoreDNS 配置为将未知域转发到节点 DNS 服务器，换言之，将其转发到部署 AKS 群集的 Azure 虚拟网络的 DNS 功能。 因此，Azure DNS 和专用区域将适用于在 AKS 中运行的 pod。

### <a name="support-scope-between-network-models"></a>网络型号之间的支持范围

无论使用何种网络模型，都可以通过以下方式之一部署 kubenet 和 Azure CNI：

* 创建 AKS 群集时，Azure 平台可以自动创建和配置虚拟网络资源。
* 创建 AKS 群集时，可以手动创建和配置虚拟网络资源并附加到这些资源。

尽管 kubenet 和 Azure CNI 支持服务终结点或 Udr 等功能，但 AKS 的[支持策略][support-policies]定义了你可以进行哪些更改。 例如：

* 如果手动创建 AKS 群集的虚拟网络资源，则在配置自己的 Udr 或服务终结点时，将支持。
* 如果 Azure 平台为你的 AKS 群集自动创建虚拟网络资源，则不支持手动更改这些 AKS 管理的资源来配置你自己的 Udr 或服务终结点。

## <a name="ingress-controllers"></a>入口控制器

创建 LoadBalancer 类型服务时，系统会创建基础 Azure 负载均衡器资源。 负载均衡器配置为在给定端口上将流量分发到服务中的 Pod。 LoadBalancer 仅在第 4 层工作 - 服务不知道实际的应用程序，不会考虑任何其他路由。

入口控制器在第 7 层工作，可使用更智能的规则来分发应用程序流量。 入口控制器的一个常见用途是基于入站 URL 将 HTTP 流量路由到不同的应用程序。

![显示 AKS 群集中入口流量的示意图][aks-ingress]

在 AKS 中，可以使用 NGINX 之类的服务器创建入口资源，或使用 AKS HTTP 应用程序路由功能。 为 AKS 群集启用 HTTP 应用程序路由时，Azure 平台会创建入口控制器和 External-DNS 控制器。 在 Kubernetes 中创建新的入口资源时，系统会在特定于群集的 DNS 区域中创建所需的 DNS A 记录。 有关详细信息，请参阅[部署 HTTP 应用程序路由][aks-http-routing]。

入口的另一个常见功能是 SSL/TLS 终止。 在通过 HTTPS 访问的大型 Web 应用程序上，TLS 终止可以由入口资源处理，而不是在应用程序自身内部处理。 要提供自动 TLS 认证生成和配置，可以将入口资源配置为使用 Let's Encrypt 之类的提供程序。 若要详细了解如何配置 NGINX 入口控制器，请参阅[入口和 TLS][aks-ingress-tls]。

你还可以配置入口控制器，以便在对 AKS 群集中的容器发出请求时保留客户端源 IP。 如果客户端的请求通过入口控制器路由到 AKS 群集中的容器，则该请求的原始源 IP 将不可用于目标容器。 如果启用*客户端源 ip 保留*，则客户端的源 ip 在 " *X 转发-对于*" 下的请求标头中可用。 如果使用的是客户端源 IP 保存在入口控制器上，则不能使用 SSL 直通。 客户端源 IP 保存和 SSL 传递可用于其他服务，例如*LoadBalancer*类型。

## <a name="network-security-groups"></a>网络安全组

网络安全组筛选 VM（例如 AKS 节点）的流量。 创建服务（如 LoadBalancer）时，Azure 平台会自动配置所需的任何网络安全组规则。 请勿手动配置网络安全组规则，以筛选 AKS 群集中 Pod 的流量。 定义任何所需端口并作为 Kubernetes 服务清单的一部分转发，让 Azure 平台创建或更新相应的规则。 你还可以使用下一部分中所述的网络策略，自动将流量筛选规则应用到 pod。

## <a name="network-policies"></a>网络策略

默认情况下，AKS 群集中的所有 Pod 都可以无限制地发送和接收流量。 为了提高安全性，你可能想要定义用来控制流量流的规则。 后端应用程序通常只向所需的前端服务公开，或者数据库组件仅可由连接到它们的应用程序层访问。

网络策略是 Kubernetes 中的一项功能，可用于控制 pod 之间的通信流。 可选择基于分配的标签、命名空间或流量端口等设置来允许或拒绝流量。 网络安全组更多是针对 AKS 节点，而不是针对 Pod。 使用网络策略是一种更合适的用来控制流量流的云本机方式。 因为 Pod 是在 AKS 群集中动态创建的，则可以动态应用所需的网络策略。

有关详细信息，请参阅[使用 Azure Kubernetes 服务中的网络策略在 pod 之间保护流量（AKS）][use-network-policies]。

## <a name="next-steps"></a>后续步骤

若要开始使用 AKS 网络，使用[kubenet][aks-configure-kubenet-networking]或[Azure CNI][aks-configure-advanced-networking]创建和配置具有自己的 IP 地址范围的 AKS 群集。

有关相关的最佳实践，请参阅[AKS 中的网络连接和安全最佳方案][operator-best-practices-network]。

有关核心 Kubernetes 和 AKS 概念的详细信息，请参阅以下文章：

- [Kubernetes/AKS 群集和工作负荷][aks-concepts-clusters-workloads]
- [Kubernetes/AKS 访问和标识][aks-concepts-identity]
- [Kubernetes/AKS 安全性][aks-concepts-security]
- [Kubernetes/AKS 存储][aks-concepts-storage]
- [Kubernetes/AKS scale][aks-concepts-scale]

<!-- IMAGES -->
[aks-clusterip]: ./media/concepts-network/aks-clusterip.png
[aks-nodeport]: ./media/concepts-network/aks-nodeport.png
[aks-loadbalancer]: ./media/concepts-network/aks-loadbalancer.png
[advanced-networking-diagram]: ./media/concepts-network/advanced-networking-diagram.png
[aks-ingress]: ./media/concepts-network/aks-ingress.png

<!-- LINKS - External -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet

<!-- LINKS - Internal -->
[aks-http-routing]: http-application-routing.md
[aks-ingress-tls]: ingress.md
[aks-configure-kubenet-networking]: configure-kubenet.md
[aks-configure-advanced-networking]: configure-azure-cni.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-identity]: concepts-identity.md
[use-network-policies]: use-network-policies.md
[operator-best-practices-network]: operator-best-practices-network.md
[support-policies]: support-policies.md
