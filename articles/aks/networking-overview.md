---
title: Azure Kubernetes 服务 (AKS) 中的网络配置
description: 了解 Azure Kubernetes 服务 (AKS) 中的基本和高级网络配置。
services: container-service
author: mmacy
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/07/2018
ms.author: marsma
ms.openlocfilehash: 818bae2e05f6a3256ccbf0cbcc901dd337b9a260
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2018
---
# <a name="network-configuration-in-azure-kubernetes-service-aks"></a>Azure Kubernetes 服务 (AKS) 中的网络配置

创建 Azure Kubernetes 服务 (AKS) 群集时，可以从两个网络选项中选择：“基本”或“高级”。

## <a name="basic-networking"></a>基本网络

“基本”网络选项是用于创建 AKS 群集的默认配置。 群集的网络配置及其 Pod 完全由 Azure 管理，并适合用于不需要自定义 VNet 配置的部署。 选择基本网络时，无法控制分配给群集的子网或 IP 地址范围等网络配置。

为基本网络配置的 AKS 群集中的节点使用 [kubenet][kubenet] Kubernetes 插件。

## <a name="advanced-networking"></a>高级网络

“高级”网络将 Pod 放在配置的 Azure 虚拟网络 (VNet) 中，可让这些 Pod 自动连接到 VNet 资源，并与 VNet 提供的丰富功能集相集成。
目前，仅当使用 [Azure 门户][portal]或资源管理器模板部署 AKS 群集时，高级网络才可用。

为高级网络配置的 AKS 群集中的节点使用 [Azure 容器网络接口 (CNI)][cni-networking] Kubernetes 插件。

![显示两个节点的示意图，其中的网桥将每个节点连接到单个 Azure VNet][advanced-networking-diagram-01]

## <a name="advanced-networking-features"></a>高级网络功能

高级网络提供以下优势：

* 将 AKS 群集部署到现有的 VNet，或者为群集创建新的 VNet 和子网。
* 群集中的每个 Pod 在 VNet 中分配有一个 IP 地址，并可直接与群集中的其他 Pod 以及 VNet 中的其他 VM 通信。
* Pod 可以通过 ExpressRoute 和站点到站点 (S2S) VPN 连接来与对等互连 VNet 中的其他服务和本地网络建立连接。 还可以从本地访问 Pod。
* 通过 Azure 负载均衡器在外部或内部公开 Kubernetes 服务。 这也是基本网络的一项功能。
* 启用了服务终结点的子网中的 Pod 可以安全地连接到 Azure 服务（例如 Azure 存储和 SQL 数据库）。
* 使用用户定义的路由 (UDR) 将来自 Pod 的流量路由到网络虚拟设备。
* Pod 可以访问公共 Internet 上的资源。 这也是基本网络的一项功能。

> [!IMPORTANT]
> 为高级网络配置的 AKS 群集中的每个节点最多可以托管 **30 个 Pod**。 预配用于 Azure CNI 插件的每个 VNet 限制为 **4096 个 IP 地址** (/20)。

## <a name="configure-advanced-networking"></a>配置高级网络

在 Azure 门户中[创建 AKS 群集](kubernetes-walkthrough-portal.md)时，可为高级网络配置以下参数：

**虚拟网络**：要将 Kubernetes 群集部署到的 VNet。 若要为群集创建新的 VNet，请选择“新建”，并遵循“创建虚拟网络”部分中的步骤。

**子网**：要将群集部署到的 VNet 中的子网。 若要在 VNet 中为群集创建新的子网，请选择“新建”，并遵循“创建子网”部分中的步骤。

**Kubernetes 服务地址范围**：Kubernetes 群集服务 IP 的 IP 地址范围。 此范围不得在群集的 VNet IP 地址范围内。

**Kubernetes DNS 服务 IP 地址**：群集 DNS 服务的 IP 地址。 此地址必须在 Kubernetes 服务地址范围内。

**Docker 网桥地址**：分配给 Docker 网桥的 IP 地址和子网掩码。 此 IP 地址不得在群集的 VNet IP 地址范围内。

以下 Azure 门户屏幕截图显示了在创建 AKS 群集过程中配置这些设置的示例：

![Azure 门户中的高级网络配置][portal-01-networking-advanced]

## <a name="plan-ip-addressing-for-your-cluster"></a>规划群集的 IP 地址

使用高级网络配置的群集需要额外的规划。 VNet 及其子网的大小必须适应你要在群集中同时运行的 Pod 数，并符合缩放要求。

Pod 和群集节点的 IP 地址是从 VNet 中指定的子网分配的。 为每个节点配置一个主要 IP（即节点本身的 IP），以及 Azure CNI 预配置的 30 个附加 IP 地址，这些附加 IP 地址将分配给节点上安排的 Pod。 横向扩展群集时，将使用子网中的 IP 地址以类似方式配置每个节点。

如前所述，预配用于 Azure CNI 插件的每个 VNet 限制为 **4096 个 IP 地址** (/20)。 为高级网络配置的群集中的每个节点最多可以托管 **30 个 Pod**。

## <a name="frequently-asked-questions"></a>常见问题

以下问题和解答适用于“高级”网络配置。

* 是否可以使用 Azure CLI 配置高级网络？

  不会。 目前，仅当使用 Azure 门户或资源管理器模板部署 AKS 群集时，高级网络才可用。

* 是否可以在群集子网中部署 VM？

  不会。 不支持在 Kubernetes 群集使用的子网中部署 VM。 可将 VM 部署在同一 VNet 中，但必须部署在不同的子网中。

* *是否可以配置基于 Pod 的网络策略？*

  不会。 目前不支持基于 Pod 的网络策略。

* 可部署到节点的 Pod 数上限是否可配置？

  默认情况下，每个节点最多可以托管 30 个 Pod。 目前，只能在使用资源管理器模板部署群集时，通过修改 `maxPods` 属性来更改最大值。

* 如何配置创建 AKS 群集期间创建的子网的其他属性？例如服务终结点。

  可以在 Azure 门户的标准 VNet 配置页中，配置创建 AKS 群集期间创建的 VNet 和子网的完整属性列表。

## <a name="next-steps"></a>后续步骤

### <a name="networking-in-aks"></a>AKS 中的网络

通过以下文章详细了解 AKS 中的网络：

[将静态 IP 地址用于 Azure Kubernetes 服务 (AKS) 负载均衡器](static-ip.md)

[Azure 容器服务 (AKS) 中的 HTTPS 入口](ingress.md)

[使用包含 Azure 容器服务 (AKS) 的内部负载均衡器](internal-lb.md)

### <a name="acs-engine"></a>ACS 引擎

[Azure 容器服务引擎（ACS 引擎）][acs-engine]是一个开源项目，它能够生成 Azure 资源管理器模板用于在 Azure 上部署支持 Docker 的群集。 使用 ACS 引擎可以部署 Kubernetes、DC/OS、Swarm Mode 和 Swarm 业务流程协调程序。

使用 ACS 引擎创建的 Kubernetes 群集支持 [kubenet][kubenet] 和 [Azure CNI][cni-networking] 插件。 因此，ACS 引擎支持基本和高级网络方案。

<!-- IMAGES -->
[advanced-networking-diagram-01]: ./media/networking-overview/advanced-networking-diagram-01.png
[portal-01-networking-advanced]: ./media/networking-overview/portal-01-networking-advanced.png

<!-- LINKS - External -->
[acs-engine]: https://github.com/Azure/acs-engine
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet
[portal]: https://portal.azure.com

<!-- LINKS - Internal -->
[aks-ssh]: aks-ssh.md
