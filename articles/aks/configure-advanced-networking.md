---
title: 在 Azure Kubernetes 服务 (AKS) 中配置高级网络
description: 了解如何在 Azure Kubernetes 服务 (AKS) 中配置高级网络，包括将 AKS 群集部署到现有虚拟网络和子网中。
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 10/11/2018
ms.author: iainfou
ms.openlocfilehash: 87c3ab9624116e9c1c61041531fdf5d3b26117e1
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2018
ms.locfileid: "49380514"
---
# <a name="configure-advanced-networking-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes 服务 (AKS) 中配置高级网络

默认情况下，AKS 群集使用基本网络，基本网络创建并配置虚拟网络和子网以供群集使用。 要对这些网络选项（如 IP 范围）进行更多控制，可以改用高级网络。 使用高级网络，还可以在现有虚拟网络和子网中创建 AKS 群集。 该现有虚拟网络通常使用 Azure ExpressRoute 或站点到站点 VPN 提供到本地网络的连接。

本文介绍如何使用高级网络创建和使用包含 AKS 群集的虚拟网络。 有关网络的更多常规信息，请参阅 [Kubernetes 和 AKS 的网络概念][aks-network-concepts]。

## <a name="prerequisites"></a>先决条件

* AKS 群集的虚拟网络必须允许出站 Internet 连接。
* 不要在同一子网中创建多个 AKS 群集。
* AKS 群集可能不会使用 Kubernetes 服务地址范围的 `169.254.0.0/16`、`172.30.0.0/16` 或 `172.31.0.0/16`。
* AKS 群集使用的服务主体在虚拟网络中的子网上必须至少具有[网络参与者](../role-based-access-control/built-in-roles.md#network-contributor)权限。 如果希望定义[自定义角色](../role-based-access-control/custom-roles.md)而不是使用内置的网络参与者角色，则需要以下权限：
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

## <a name="plan-ip-addressing-for-your-cluster"></a>规划群集的 IP 地址

使用高级网络配置的群集需要额外的规划。 虚拟网络及其子网的大小必须适应计划运行的 Pod 数以及群集的节点数。

Pod 和群集节点的 IP 地址是从虚拟网络中指定的子网分配的。 为每个节点配置一个主要 IP（即节点的 IP），以及 Azure CNI 预配置的 30 个附加 IP 地址，这些附加 IP 地址将分配给节点上安排的 Pod。 横向扩展群集时，将使用子网中的 IP 地址以类似方式配置每个节点。

AKS 群集 IP 地址计划包括虚拟网络、至少一个节点和 Pod 子网以及 Kubernetes 服务地址范围。

| 地址范围 / Azure 资源 | 限制和调整大小 |
| --------- | ------------- |
| 虚拟网络 | Azure 虚拟网络的大小可以为 /8，但仅限于 65,536 个已配置的 IP 地址。 |
| 子网 | 大小必须足以容纳群集中可能预配的节点、Pod 以及所有 Kubernetes 和 Azure 资源。 例如，如果部署内部 Azure 负载均衡器，其前端 IP 分配自群集子网（而不是公共 IP）。 <p/>计算最小子网大小：`(number of nodes) + (number of nodes * pods per node)` <p/>50 个节点群集的示例：`(50) + (50 * 30) = 1,550`（/21 或更大） |
| Kubernetes 服务地址范围 | 此范围不应由此虚拟网络上或连接到此虚拟网络的任何网络元素使用。 服务地址 CIDR 必须小于 /12。 |
| Kubernetes DNS 服务 IP 地址 | Kubernetes 服务地址范围内的 IP 地址将由群集服务发现 (kube-dns) 使用。 |
| Docker 桥地址 | IP 地址（采用 CIDR 表示法）用作节点上的 Docker 桥 IP 地址。 默认地址为 172.17.0.1/16。 |

## <a name="maximum-pods-per-node"></a>每个节点的最大 Pod 数

AKS 群集中每个节点的默认最大 Pod 数因基础网络和高级网络以及群集部署方法而异。

| 部署方法 | 基本 | 高级 | 可在部署时配置 |
| -- | :--: | :--: | -- |
| Azure CLI | 110 | 30 | 是（最大 110） |
| 资源管理器模板 | 110 | 30 | 是（最大 110） |
| 门户 | 110 | 30 | 否 |

### <a name="configure-maximum---new-clusters"></a>配置最大值 - 新群集

只能在群集部署时配置每个节点的最大 Pod 数。 如果使用 Azure CLI 或资源管理器模板进行部署，则可以将每个节点的最大 Pod 数设置为 110。

* **Azure CLI**：使用 [az aks create][az-aks-create] 命令部署群集时，指定 `--max-pods` 参数。 最大值为 110。
* **资源管理器模板**：使用资源管理器模板部署群集时，在 [ManagedClusterAgentPoolProfile] 对象中指定 `maxPods` 属性。 最大值为 110。
* **Azure 门户**：使用 Azure 门户部署群集时，不能更改每个节点的最大 Pod 数。 使用 Azure 门户部署时，高级网络群集中每个节点的 Pod 数限制为 30 个。

### <a name="configure-maximum---existing-clusters"></a>配置最大值 - 现有群集

无法在现有 AKS 群集上更改每个节点的最大 Pod 数。 只能在最初部署群集时调整数量。

## <a name="deployment-parameters"></a>部署参数

创建 AKS 群集时，可为高级网络配置以下参数：

**虚拟网络**：要将 Kubernetes 群集部署到的虚拟网络。 要为群集创建新的虚拟网络，请选择“新建”，并按照“创建虚拟网络”部分中的步骤操作。 有关 Azure 虚拟网络的限制和配额的信息，请参阅 [Azure 订阅和服务限制、配额和约束](../azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits)。

**子网**：要将群集部署到的虚拟网络中的子网。 若要在虚拟网络中为群集创建新的子网，请选择“新建”，并按照“创建子网”部分中的步骤操作。

**Kubernetes 服务地址范围**：这是一组虚拟 IP，是 Kubernetes 分配给群集中的[服务][services]的。 可以使用任何专用地址范围，只要其符合以下要求即可：

* 不得在群集的虚拟网络 IP 地址范围内
* 不得与群集虚拟网络对等互连的任何其他虚拟网络重叠
* 不得与任何本地 IP 重叠
* 不得在范围 `169.254.0.0/16`、`172.30.0.0/16` 或 `172.31.0.0/16` 中

虽然从技术上来说可以在群集所在的虚拟网络中指定一个服务地址范围，但建议不要这样做。 如果使用重叠的 IP 范围，则可能导致不可预测的行为。 有关详细信息，请参阅本文中的[常见问题解答](#frequently-asked-questions)部分。 有关 Kubernetes 服务的详细信息，请参阅 Kubernetes 文档中的[服务][services]。

**Kubernetes DNS 服务 IP 地址**：群集 DNS 服务的 IP 地址。 此地址必须在 Kubernetes 服务地址范围内。

**Docker 网桥地址**：分配给 Docker 网桥的 IP 地址和子网掩码。 此 IP 地址不得在群集的虚拟网络 IP 地址范围内。

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

  不是。 不支持在 Kubernetes 群集使用的子网中部署 VM。 可将 VM 部署在同一虚拟网络中，但必须部署在不同的子网中。

* *是否可以配置基于 Pod 的网络策略？*

  不是。 目前不支持基于 Pod 的网络策略。

* 可部署到节点的 Pod 数上限是否可配置？

  是的，使用 Azure CLI 或资源管理器模板部署群集时可配置。 请参阅[每个节点的最大 Pod 数](#maximum-pods-per-node)。

  无法在现有群集上更改每个节点的最大 Pod 数。

* 如何配置创建 AKS 群集期间创建的子网的其他属性？例如服务终结点。

  可以在 Azure 门户的标准虚拟网络配置页中，配置创建 AKS 群集期间创建的虚拟网络和子网的完整属性列表。

* 是否可以在我的群集虚拟网络中将另一子网用于 Kubernetes 服务地址范围？

  此配置是可以的，但建议不要这样做。 服务地址范围是一组虚拟 IP (VIP)，是 Kubernetes 分配给群集中的服务的。 Azure 网络无法查看 Kubernetes 群集的服务 IP 范围。 由于无法查看群集的服务地址范围，因此有可能以后会在群集虚拟网络中创建新的子网，该子网与服务地址范围重叠。 如果出现这种形式的重叠，则 Kubernetes 为服务分配的 IP 可能是子网中另一资源正在使用的，导致不可预测的行为或故障。 如果能够确保所用地址范围不在群集的虚拟网络中，则可避免这种重叠风险。

## <a name="next-steps"></a>后续步骤

### <a name="networking-in-aks"></a>AKS 中的网络

通过以下文章详细了解 AKS 中的网络：

- [将静态 IP 地址用于 Azure Kubernetes 服务 (AKS) 负载均衡器](static-ip.md)
- [使用包含 Azure 容器服务 (AKS) 的内部负载均衡器](internal-lb.md)

- [使用外部网络连接创建基本入口控制器][aks-ingress-basic]
- [启用 HTTP 应用程序路由附加产品][aks-http-app-routing]
- [创建使用内部、专用网络和 IP 地址的入口控制器][aks-ingress-internal]
- [使用动态公共 IP 创建入口控制器并配置 Let 's Encrypt 以自动生成 TLS 证书][aks-ingress-tls]
- [使用静态公共 IP 创建入口控制器并配置 Let 's Encrypt 以自动生成 TLS 证书][aks-ingress-static-tls]

### <a name="acs-engine"></a>ACS 引擎

[Azure 容器服务引擎（ACS 引擎）][acs-engine]是一个开源项目，它能够生成 Azure 资源管理器模板用于在 Azure 上部署支持 Docker 的群集。 使用 ACS 引擎可以部署 Kubernetes、DC/OS、Swarm Mode 和 Swarm 业务流程协调程序。

使用 ACS 引擎创建的 Kubernetes 群集支持 [kubenet][kubenet] 和 [Azure CNI][cni-networking] 插件。 因此，ACS 引擎支持基本和高级网络方案。

<!-- IMAGES -->
[advanced-networking-diagram-01]: ./media/networking-overview/advanced-networking-diagram-01.png
[portal-01-networking-advanced]: ./media/networking-overview/portal-01-networking-advanced.png

<!-- LINKS - External -->
[acs-engine]: https://github.com/Azure/acs-engine
[services]: https://kubernetes.io/docs/concepts/services-networking/service/
[portal]: https://portal.azure.com
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet

<!-- LINKS - Internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[aks-ssh]: ssh.md
[ManagedClusterAgentPoolProfile]: /azure/templates/microsoft.containerservice/managedclusters#managedclusteragentpoolprofile-object
[aks-network-concepts]: concepts-network.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-internal]: ingress-internal-ip.md
