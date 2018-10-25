---
title: 概念 - Azure Kubernetes 服务 (AKS) 中的网络
description: 了解 Azure Kubernetes 服务 (AKS) 中的网络，包括基本和高级网络、入口控制器、负载均衡器和静态 IP 地址。
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: iainfou
ms.openlocfilehash: 62ba98f221041d5bbf9bb095a02d052218eb0fd0
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2018
ms.locfileid: "49380547"
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

- **LoadBalancer** - 创建 Azure 负载均衡器资源、配置外部 IP 地址并将请求的 Pod 连接到负载均衡器后端池。 为允许客户流量发送到应用程序，要在所需端口上创建负载均衡规则。 

    ![显示 AKS 群集中负载均衡器流量的示意图][aks-loadbalancer]

    针对入站流量的其他控制和路由，可能要改用[入口控制器](#ingress-controllers)。

- **ExternalName** - 创建特定的 DNS 条目，便于访问应用程序。

可以动态分配负载均衡器和服务的 IP 地址，也可以指定要使用的现有静态 IP 地址。 可以分配内部和外部静态 IP 地址。 这个现有静态 IP 地址通常与 DNS 条目绑定。

可以创建内部和外部负载均衡器。 内部负载均衡器仅分配一个专用 IP 地址，因此无法从 Internet 访问。

## <a name="azure-virtual-networks"></a>Azure 虚拟网络

在 AKS 中，可以部署使用以下两种网络模型之一的群集：

- 基本网络 - 在部署 AKS 群集时创建和配置网络资源。
- 高级网络 - AKS 群集连接到现有的虚拟网络资源和配置。

### <a name="basic-networking"></a>基本网络

基本网络选项是用于创建 AKS 群集的默认配置。 Azure 平台管理群集和 Pod 的网络配置。 基本网络适用于无需自定义虚拟网络配置的部署。 使用基本网络时，无法定义网络配置，例如子网名称或分配给 AKS 群集的 IP 地址范围。

为基本网络配置的 AKS 群集中的节点使用 [kubenet][kubenet] Kubernetes 插件。

基本网络提供以下功能：

- 通过 Azure 负载均衡器在外部或内部公开 Kubernetes 服务。
- Pod 可以访问公共 Internet 上的资源。

### <a name="advanced-networking"></a>高级网络

高级网络将 Pod 放置在配置的 Azure 虚拟网络中。 此虚拟网络提供与其他 Azure 资源的自动连接，并与一组丰富的功能相集成。 高级网络适用于需要特定虚拟网络配置的部署，例如使用现有子网和连接。 使用高级网络，可以定义这些子网名称和 IP 地址范围。

为高级网络配置的 AKS 群集中的节点使用 [Azure 容器网络接口 (CNI)][cni-networking] Kubernetes 插件。

![显示两个节点的示意图，其中的网桥将每个节点连接到单个 Azure VNet][advanced-networking-diagram]

高级网络基于基本网络提供以下功能：

- 将 AKS 群集部署到现有的 Azure 虚拟网络，或者为群集创建新的虚拟网络和子网。
- 在虚拟网络中，群集中的每个 Pod 都分配了一个 IP 地址。 Pod 可以直接与群集中的其他 Pod 以及虚拟网络中的其他节点通信。
- Pod 可以通过 ExpressRoute 和站点到站点 (S2S) VPN 连接来与对等互连虚拟网络中的其他服务（包括本地网络）建立连接。 还可以从本地访问 Pod。
- 启用了服务终结点的子网中的 Pod 可以安全连接到 Azure 服务（例如 Azure 存储和 SQL DB）。
- 可以创建用户定义的路由 (UDR) 将来自 Pod 的流量路由到网络虚拟设备。

有关详细信息，请参阅[为 AKS 群集配置高级网络][aks-configure-advanced-networking]。

## <a name="ingress-controllers"></a>入口控制器

创建 LoadBalancer 类型服务时，系统会创建基础 Azure 负载均衡器资源。 负载均衡器配置为在给定端口上将流量分发到服务中的 Pod。 LoadBalancer 仅在第 4 层工作 - 服务不知道实际的应用程序，不会考虑任何其他路由。

入口控制器在第 7 层工作，可使用更智能的规则来分发应用程序流量。 入口控制器的一个常见用途是基于入站 URL 将 HTTP 流量路由到不同的应用程序。

![显示 AKS 群集中入口流量的示意图][aks-ingress]

在 AKS 中，可以使用 NGINX 之类的服务器创建入口资源，或使用 AKS HTTP 应用程序路由功能。 为 AKS 群集启用 HTTP 应用程序路由时，Azure 平台会创建入口控制器和 External-DNS 控制器。 在 Kubernetes 中创建新的入口资源时，系统会在特定于群集的 DNS 区域中创建所需的 DNS A 记录。 有关详细信息，请参阅[部署 HTTP 应用程序路由][aks-http-routing]。

入口的另一个常见功能是 SSL/TLS 终止。 在通过 HTTPS 访问的大型 Web 应用程序上，TLS 终止可以由入口资源处理，而不是在应用程序自身内部处理。 要提供自动 TLS 认证生成和配置，可以将入口资源配置为使用 Let's Encrypt 之类的提供程序。 有关使用 Let's Encrypt 配置 NGINX 入口控制器的详细信息，请参阅 [Ingress 和 TLS ][aks-ingress-tls]。

## <a name="network-security-groups"></a>网络安全组

网络安全组筛选 VM（如 AKS 节点）的流量。 创建服务（如 LoadBalancer）时，Azure 平台会自动配置所需的任何网络安全组规则。 请勿手动配置网络安全组规则，以筛选 AKS 群集中 Pod 的流量。 定义任何所需端口并作为 Kubernetes 服务清单的一部分转发，让 Azure 平台创建或更新相应的规则。

流量（如 SSH）存在默认网络安全组规则。 这些默认规则适用于群集管理和故障排除访问。 删除这些默认规则可能会导致 AKS 管理出现问题，并破坏服务级别目标 (SLO)。

## <a name="next-steps"></a>后续步骤

要开始使用 AKS 网络，请参阅[为 AKS 群集创建和配置高级网络][aks-configure-advanced-networking]。

有关核心 Kubernetes 和 AKS 概念的详细信息，请参阅以下文章：

- [Kubernetes/AKS 群集和工作负荷][aks-concepts-clusters-workloads]
- [Kubernetes/AKS 访问和标识][aks-concepts-identity]
- [Kubernetes/AKS 安全性][aks-concepts-security]
- [Kubernetes/AKS 存储][aks-concepts-storage]
- [Kubernetes/AKS 规模][aks-concepts-scale]

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
[aks-configure-advanced-networking]: configure-advanced-networking.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-identity]: concepts-identity.md