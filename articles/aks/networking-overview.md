---
title: Azure Kubernetes 服务 (AKS) 中的网络配置
description: 了解 Azure Kubernetes 服务 (AKS) 中的基本和高级网络配置。
services: container-service
author: mmacy
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/23/2018
ms.author: marsma
ms.openlocfilehash: cfe034d6dcac48d7c9e4b2ce17e4926a81a27886
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2018
ms.locfileid: "39216098"
---
# <a name="network-configuration-in-azure-kubernetes-service-aks"></a>Azure Kubernetes 服务 (AKS) 中的网络配置

创建 Azure Kubernetes 服务 (AKS) 群集时，可以从两个网络选项中选择：“基本”或“高级”。

## <a name="basic-networking"></a>基本网络

“基本”网络选项是用于创建 AKS 群集的默认配置。 群集的网络配置及其 Pod 完全由 Azure 管理，并适合用于不需要自定义 VNet 配置的部署。 选择基本网络时，无法控制分配给群集的子网或 IP 地址范围等网络配置。

为基本网络配置的 AKS 群集中的节点使用 [kubenet][kubenet] Kubernetes 插件。

## <a name="advanced-networking"></a>高级网络

“高级”网络将 Pod 放在配置的 Azure 虚拟网络 (VNet) 中，可让这些 Pod 自动连接到 VNet 资源，并与 VNet 提供的丰富功能集相集成。 目前，当使用 [Azure 门户][portal]、Azure CLI 或资源管理器模板部署 AKS 群集时，高级网络才可用。

为高级网络配置的 AKS 群集中的节点使用 [Azure 容器网络接口 (CNI)][cni-networking] Kubernetes 插件。

![显示两个节点的示意图，其中的网桥将每个节点连接到单个 Azure VNet][advanced-networking-diagram-01]

## <a name="advanced-networking-features"></a>高级网络功能

高级网络提供以下优势：

* 将 AKS 群集部署到现有的 VNet，或者为群集创建新的 VNet 和子网。
* 群集中的每个 Pod 在 VNet 中分配有一个 IP 地址，并可直接与群集中的其他 Pod 以及 VNet 中的其他节点通信。
* Pod 可以通过 ExpressRoute 和站点到站点 (S2S) VPN 连接来与对等互连 VNet 中的其他服务和本地网络建立连接。 还可以从本地访问 Pod。
* 通过 Azure 负载均衡器在外部或内部公开 Kubernetes 服务。 这也是基本网络的一项功能。
* 启用了服务终结点的子网中的 Pod 可以安全地连接到 Azure 服务（例如 Azure 存储和 SQL 数据库）。
* 使用用户定义的路由 (UDR) 将来自 Pod 的流量路由到网络虚拟设备。
* Pod 可以访问公共 Internet 上的资源。 这也是基本网络的一项功能。

## <a name="advanced-networking-prerequisites"></a>高级网络先决条件

* AKS 群集的 VNet 必须允许出站 Internet 连接。
* 不要在同一子网中创建多个 AKS 群集。
* AKS 群集可能不会使用 Kubernetes 服务地址范围的 `169.254.0.0/16`、`172.30.0.0/16` 或 `172.31.0.0/16`。
* AKS 群集使用的服务主体在 VNet 中的子网上必须至少具有[网络参与者](../role-based-access-control/built-in-roles.md#network-contributor)权限。 如果希望定义[自定义角色](../role-based-access-control/custom-roles.md)而不是使用内置的网络参与者角色，则需要以下权限：
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

## <a name="plan-ip-addressing-for-your-cluster"></a>规划群集的 IP 地址

使用高级网络配置的群集需要额外的规划。 VNet 及其子网的大小必须适应你计划运行的 Pod 数以及群集的节点数。

Pod 和群集节点的 IP 地址是从 VNet 中指定的子网分配的。 为每个节点配置一个主要 IP（即节点的 IP），以及 Azure CNI 预配置的 30 个附加 IP 地址，这些附加 IP 地址将分配给节点上安排的 Pod。 横向扩展群集时，将使用子网中的 IP 地址以类似方式配置每个节点。

AKS 群集 IP 地址计划包括 VNet、至少一个节点和 Pod 子网以及 Kubernetes 服务地址范围。

| 地址范围 / Azure 资源 | 限制和调整大小 |
| --------- | ------------- |
| 虚拟网络 | Azure VNet 最大可以为 /8，但可能只有 16,000 个配置的 IP 地址。 |
| 子网 | 大小必须足以容纳群集中可能预配的节点、Pod 以及所有 Kubernetes 和 Azure 资源。 例如，如果部署内部 Azure 负载均衡器，其前端 IP 分配自群集子网（而不是公共 IP）。 <p/>计算最小子网大小：`(number of nodes) + (number of nodes * pods per node)` <p/>50 个节点群集的示例：`(50) + (50 * 30) = 1,550`（/21 或更大） |
| Kubernetes 服务地址范围 | 此范围不应由此 VNet 上或连接到此 VNet 的任何网络元素使用。 服务地址 CIDR 必须小于 /12。 |
| Kubernetes DNS 服务 IP 地址 | Kubernetes 服务地址范围内的 IP 地址将由群集服务发现 (kube-dns) 使用。 |
| Docker 桥地址 | IP 地址（采用 CIDR 表示法）用作节点上的 Docker 桥 IP 地址。 默认地址为 172.17.0.1/16。 |

预配用于 Azure CNI 插件的每个 VNet 限制为 16,000 个配置的 IP 地址。

## <a name="maximum-pods-per-node"></a>每个节点的最大 Pod 数

AKS 群集中每个节点的默认最大 Pod 数因基础网络和高级网络以及群集部署方法而异。

### <a name="default-maximum"></a>默认最大值

* 基础网络：每个节点 110 个 Pod
* 高级网络：每个节点 30 个 Pod

### <a name="configure-maximum"></a>配置最大值

根据部署方法，可能能够修改 AKS 群集中每个节点的最大 Pod 数。

* **Azure CLI**：使用 [az aks create][az-aks-create] 命令部署群集时，指定 `--max-pods` 参数。
* **资源管理器模板**：使用资源管理器模板部署群集时，在 [ManagedClusterAgentPoolProfile] 对象中指定 `maxPods` 属性。
* **Azure 门户**：使用 Azure 门户部署群集时，不能修改每个节点的最大 Pod 数。 在 Azure 门户中部署高级网络群集时，每个节点的 Pod 数限制为 30 个。

## <a name="deployment-parameters"></a>部署参数

创建 AKS 群集时，可为高级网络配置以下参数：

**虚拟网络**：要将 Kubernetes 群集部署到的 VNet。 若要为群集创建新的 VNet，请选择“新建”，并遵循“创建虚拟网络”部分中的步骤。 VNet 限制为 16,000 个配置的 IP 地址。

**子网**：要将群集部署到的 VNet 中的子网。 若要在 VNet 中为群集创建新的子网，请选择“新建”，并遵循“创建子网”部分中的步骤。

Kubernetes 服务地址范围：Kubernetes 服务地址范围是来自分配给群集中 Kubernetes 服务的地址的 IP 范围（有关 Kubernetes 服务的详细信息，请参阅 Kubernetes 文档中的[服务][services]）。

Kubernetes 服务 IP 地址范围：

* 不得在群集的 VNet IP 地址范围内
* 不得与群集 VNet 对等的任何其他 VNet 重叠
* 不得与任何本地 IP 重叠

如果使用重叠的 IP 范围，则可能导致不可预测的行为。 例如，如果 pod 尝试访问群集外的 IP，并且该 IP 也恰好是服务 IP，则可能会看到不可预测的行为和失败。

**Kubernetes DNS 服务 IP 地址**：群集 DNS 服务的 IP 地址。 此地址必须在 Kubernetes 服务地址范围内。

**Docker 网桥地址**：分配给 Docker 网桥的 IP 地址和子网掩码。 此 IP 地址不得在群集的 VNet IP 地址范围内。

## <a name="configure-networking---cli"></a>配置网络 - CLI

使用 Azure CLI 创建 AKS 群集时，还可以配置高级网络。 使用以下命令在启用了高级网络功能的状态下创建新的 AKS 群集。

首先，将现有子网的子网资源 ID 加入到 AKS 群集将加入的子网资源 ID：

```console
$ az network vnet subnet list --resource-group myVnet --vnet-name myVnet --query [].id --output tsv

/subscriptions/d5b9d4b7-6fc1-46c5-bafe-38effaed19b2/resourceGroups/myVnet/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/default
```

使用带有 `--network-plugin azure` 参数的 [az aks create][az-aks-create] 命令创建具有高级网络的群集。 使用上一步中收集的子网 ID 更新 `--vnet-subnet-id` 值：

```azurecli
az aks create --resource-group myAKSCluster --name myAKSCluster --network-plugin azure --vnet-subnet-id <subnet-id> --docker-bridge-address 172.17.0.1/16 --dns-service-ip 10.2.0.10 --service-cidr 10.2.0.0/24
```

## <a name="configure-networking---portal"></a>配置网络 - 门户

以下 Azure 门户屏幕截图显示了在创建 AKS 群集过程中配置这些设置的示例：

![Azure 门户中的高级网络配置][portal-01-networking-advanced]

## <a name="frequently-asked-questions"></a>常见问题

以下问题和解答适用于“高级”网络配置。

* 是否可以在群集子网中部署 VM？

  不是。 不支持在 Kubernetes 群集使用的子网中部署 VM。 可将 VM 部署在同一 VNet 中，但必须部署在不同的子网中。

* *是否可以配置基于 Pod 的网络策略？*

  不是。 目前不支持基于 Pod 的网络策略。

* 可部署到节点的 Pod 数上限是否可配置？

  是的，使用 Azure CLI 或资源管理器模板部署群集时可配置。 请参阅[每个节点的最大 Pod 数](#maximum-pods-per-node)。

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
[services]: https://kubernetes.io/docs/concepts/services-networking/service/
[portal]: https://portal.azure.com

<!-- LINKS - Internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[aks-ssh]: aks-ssh.md
[ManagedClusterAgentPoolProfile]: /azure/templates/microsoft.containerservice/managedclusters#managedclusteragentpoolprofile-object
