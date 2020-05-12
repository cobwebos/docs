---
title: 在 Azure Kubernetes 服务 (AKS) 中配置 kubenet 网络
description: 了解如何在 Azure Kubernetes 服务 (AKS) 中配置 kubenet（基本）网络，以将 AKS 群集部署到现有虚拟网络和子网。
services: container-service
ms.topic: article
ms.date: 06/26/2019
ms.reviewer: nieberts, jomore
ms.openlocfilehash: 060e98f2617da503068911ec1e687241d909dabc
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83120906"
---
# <a name="use-kubenet-networking-with-your-own-ip-address-ranges-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes 服务 (AKS) 中结合自己的 IP 地址范围使用 kubenet 网络

默认情况下，AKS 群集使用 [kubenet][kubenet]，系统会为你创建 Azure 虚拟网络和子网。 节点使用 *kubenet* 从 Azure 虚拟网络子网获取 IP 地址。 Pod 接收从逻辑上不同的地址空间到节点的 Azure 虚拟网络子网的 IP 地址。 然后配置网络地址转换 (NAT)，以便 Pod 可以访问 Azure 虚拟网络上的资源。 流量的源 IP 地址通过 NAT 转换为节点的主 IP 地址。 这种方法大大减少了需要在网络空间中保留供 Pod 使用的 IP 地址数量。

借助 [Azure 容器网络接口 (CNI)][cni-networking]，每个 Pod 都可以从子网获得 IP 地址，并且可供直接访问。 这些 IP 地址在网络空间中必须唯一，并且必须事先计划。 每个节点都有一个配置参数来表示它支持的最大 Pod 数。 这样，就会为每个节点预留相应的 IP 地址数。 使用此方法需要经过更详细的规划，并且经常会耗尽 IP 地址，或者在应用程序需求增长时需要在更大的子网中重建群集。 你可以在创建群集时或创建新节点池时，配置可部署到节点的最大 pod。 如果在创建新节点池时没有指定 maxPods，则会收到 kubenet 的默认值110。

本文介绍如何使用 *kubenet* 网络来创建和使用 AKS 群集的虚拟网络子网。 有关网络选项和注意事项的详细信息，请参阅 [Kubernetes 和 AKS 的网络概念][aks-network-concepts]。

## <a name="prerequisites"></a>必备条件

* AKS 群集的虚拟网络必须允许出站 Internet 连接。
* 不要在同一子网中创建多个 AKS 群集。
* AKS 群集可能不会使用 Kubernetes 服务地址范围的 `169.254.0.0/16`、`172.30.0.0/16`、`172.31.0.0/16` 或 `192.0.2.0/24`。
* AKS 群集使用的服务主体必须至少拥有虚拟网络中子网的 "[网络参与者](../role-based-access-control/built-in-roles.md#network-contributor)" 角色。 如果希望定义[自定义角色](../role-based-access-control/custom-roles.md)而不是使用内置的网络参与者角色，则需要以下权限：
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

> [!WARNING]
> 若要使用 Windows Server 节点池，必须使用 Azure CNI。 使用 kubenet 作为网络模型不适用于 Windows Server 容器。

## <a name="before-you-begin"></a>开始之前

需要安装并配置 Azure CLI 2.0.65 或更高版本。 运行  `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅 [安装 Azure CLI][install-azure-cli]。

## <a name="overview-of-kubenet-networking-with-your-own-subnet"></a>使用自有子网的 kubenet 网络概述

在许多环境中，你已定义了具有分配的 IP 地址范围的虚拟网络和子网。 这些虚拟网络资源用于支持多个服务和应用程序。 若要提供网络连接，AKS 群集可以使用 *kubenet*（基本网络）或 Azure CNI（高级网络）。**

使用 *kubenet* 时，只有节点接收虚拟网络子网中的 IP 地址。 Pod 无法直接相互通信。 用户定义的路由 (UDR) 和 IP 转发用于不同节点中 Pod 之间的连接。 此外，可以在接收分配的 IP 地址的服务后面部署 Pod，并对应用程序的流量进行负载均衡。 下图显示了 AKS 节点（不是 Pod）如何接收虚拟网络子网中的 IP 地址：

![使用 AKS 群集的 Kubenet 网络模型](media/use-kubenet/kubenet-overview.png)

Azure 在一个 UDR 中最多支持 400 个路由，因此，AKS 群集中的节点数不能超过 400 个。 *Kubenet*不支持 AKS[虚拟节点][virtual-nodes]和 Azure 网络策略。  可以使用[Calico 网络策略][calico-network-policies]，因为在 kubenet 中支持它们。

使用 *Azure CNI* 时，每个 Pod 将接收 IP 子网中的 IP 地址，并且可以直接与其他 Pod 和服务通信。 群集的最大大小可为指定的 IP 地址范围上限。 但是，必须提前规划 IP 地址范围，AKS 节点根据它们支持的最大 Pod 数消耗所有 IP 地址。 *AZURE CNI*支持高级网络功能和方案，如[虚拟节点][virtual-nodes]或网络策略（azure 或 Calico）。

### <a name="ip-address-availability-and-exhaustion"></a>IP 地址可用性和耗尽

使用 *Azure CNI* 时，一个常见问题是分配的 IP 地址范围太小，以致在扩展或升级群集时需要添加更多的节点。 网络团队可能无法提供足够大的 IP 地址范围来支持预期的应用程序需求。

作为一种折衷方案，可以创建使用 *kubenet* 的 AKS 群集并连接到现有虚拟网络子网。 这种方法可让节点接收定义的 IP 地址，而无需提前为群集中可能运行的所有潜在 Pod 节点预留大量的 IP 地址。

使用 *kubenet* 时，可以大幅减小要使用的 IP 地址范围，并且可以支持大型群集和应用程序的需求。 例如，即使使用 */27* IP 地址范围，也能运行包括 20-25 节点个的群集，并且可以提供足够的空间用于扩展或升级。 此群集大小最多支持 *2,200-2,750* 个 Pod（每个节点的最大 Pod 数默认为 110 个）。 可以在 AKS 中使用 *kubenet* 配置的每个节点的最大 Pod 数为 110。

以下基本计算方法对网络模型的差异做了比较：

- **kubenet** -一个简单的 */24* IP 地址范围最多可以支持群集中的 *251* 个节点（每个 Azure 虚拟网络子网预留前三个 IP 地址用于管理操作）
  - 此节点计数最多可以支持 *27,610* 个 Pod（使用 *kubenet* 的每个节点的最大 Pod 数默认为 110 个）
- **Azure CNI** - 相同的基本 */24* 子网范围最多只能支持群集中的 *8* 个节点
  - 此节点计数最多只能支持 *240* 个 Pod（使用 *Azure CNI* 的每个节点的最大 Pod 数默认为 30 个）

> [!NOTE]
> 这些最大值未考虑到帐户升级或扩展操作。 在实践中，不可能会运行子网 IP 地址范围支持的节点数上限。 必须留出一些 IP 地址，供扩展或升级操作期间使用。

### <a name="virtual-network-peering-and-expressroute-connections"></a>虚拟网络对等互连和 ExpressRoute 连接

若要提供本地连接，*kubenet* 和 *Azure CNI* 网络方法都可以使用 [Azure 虚拟网络对等互连][vnet-peering]或 [ExpressRoute 连接][express-route]。 精心规划 IP 地址范围，以防止地址重叠和流量路由错误。 例如，许多本地网络使用通过 ExpressRoute 连接播发的 *10.0.0.0/8* 地址范围。 建议在此地址范围（例如 *172.16.0.0/16*）外部的 Azure 虚拟网络子网中创建 AKS 群集。

### <a name="choose-a-network-model-to-use"></a>选择要使用的网络模型

选择用于 AKS 群集的网络插件通常需要在灵活性与高级配置需求之间进行平衡。 如果每种网络模型似乎都很合适，以下考虑因素可帮助你做出决策：

对于以下情况，可使用 *kubenet*：

- IP 地址空间有限。
- 大部分 Pod 通信在群集中进行。
- 不需要虚拟节点或 Azure 网络策略等高级 AKS 功能。  使用 [Calico 网络策略][calico-network-policies]。

对于以下情况，可使用 *Azure CNI*：

- 有可用的 IP 地址空间。
- 大部分 Pod 通信是与群集外部的资源进行的。
- 你不想要管理 UDR。
- 需要虚拟节点或 Azure 网络策略等 AKS 高级功能。  使用 [Calico 网络策略][calico-network-policies]。

有关帮助你决定使用哪个网络模型的详细信息，请参阅[比较网络模型及其支持范围][network-comparisons]。

## <a name="create-a-virtual-network-and-subnet"></a>创建虚拟网络和子网

若要开始使用 *kubenet* 和自己的虚拟网络子网，请先使用 [az group create][az-group-create] 命令创建一个资源组。 以下示例在 eastus 位置创建名为 myResourceGroup 的资源组：  

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

如果没有可用的现有虚拟网络和子网，请使用 [az network vnet create][az-network-vnet-create] 命令创建这些网络资源。 在以下示例中，虚拟网络名为 *myVnet*，其地址前缀为 *192.168.0.0/16*。 创建了名为 *myAKSSubnet*、地址前缀为 *192.168.1.0/24* 的子网。

```azurecli-interactive
az network vnet create \
    --resource-group myResourceGroup \
    --name myAKSVnet \
    --address-prefixes 192.168.0.0/16 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 192.168.1.0/24
```

## <a name="create-a-service-principal-and-assign-permissions"></a>创建服务主体并分配权限

若要允许 AKS 群集与其他 Azure 资源交互，请使用 Azure Active Directory 服务主体。 服务主体需要有权管理 AKS 节点使用的虚拟网络和子网。 若要创建服务主体，请使用 [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] 命令：

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

以下示例输出显示了服务主体的应用程序 ID 和密码。 其他步骤中使用了这些值向服务主体分配角色，然后创建 AKS 群集：

```azurecli
az ad sp create-for-rbac --skip-assignment
```

```output
{
  "appId": "476b3636-5eda-4c0e-9751-849e70b5cfad",
  "displayName": "azure-cli-2019-01-09-22-29-24",
  "name": "http://azure-cli-2019-01-09-22-29-24",
  "password": "a1024cd7-af7b-469f-8fd7-b293ecbb174e",
  "tenant": "72f998bf-85f1-41cf-92ab-2e7cd014db46"
}
```

若要在剩余步骤中分配正确的委托，请使用 [az network vnet show][az-network-vnet-show] 和 [az network vnet subnet show][az-network-vnet-subnet-show] 命令获取所需的资源 ID。 这些资源 ID 存储为变量，并在剩余的步骤中引用：

```azurecli-interactive
VNET_ID=$(az network vnet show --resource-group myResourceGroup --name myAKSVnet --query id -o tsv)
SUBNET_ID=$(az network vnet subnet show --resource-group myResourceGroup --vnet-name myAKSVnet --name myAKSSubnet --query id -o tsv)
```

现在，使用 [az role assignment create][az-role-assignment-create] 命令为 AKS 群集的服务主体分配虚拟网络中的“参与者”权限。** 提供自己的* \< appId>* ，如上一个命令的输出中所示，创建服务主体：

```azurecli-interactive
az role assignment create --assignee <appId> --scope $VNET_ID --role Contributor
```

## <a name="create-an-aks-cluster-in-the-virtual-network"></a>在虚拟网络中创建 AKS 群集

现已创建虚拟网络和子网、已创建服务主体并为其分配了这些网络资源的使用权限。 现在，请使用 [az aks create][az-aks-create] 命令在虚拟网络和子网中创建 AKS 群集。 定义自己的服务主体* \< appId>* 和* \< 密码>*，如上一个命令的输出中所示，以创建服务主体。

在创建群集的过程中还定义了以下 IP 地址范围：

* *--service-cidr* 用于为 AKS 群集中的内部服务分配 IP 地址。 此 IP 地址范围应该是未在网络环境中的其他位置使用的地址空间。 如果你需要或者打算使用 Express Route 或站点到站点 VPN 连接来连接 Azure 虚拟网络，则此范围可包括任何本地网络范围。

* *--dns-service-ip* 地址应该是服务 IP 地址范围的 *.10* 地址。

* *--pod-cidr* 应该是未在网络环境中的其他位置使用的较大地址空间。 如果你需要或者打算使用 Express Route 或站点到站点 VPN 连接来连接 Azure 虚拟网络，则此范围可包括任何本地网络范围。
    * 此地址范围必须足够大，可以容纳预期要扩展到的节点数。 部署群集后，如果需要为更多的节点提供更多的地址，你无法更改此地址范围。
    * Pod IP 地址范围用于将 */24* 地址空间分配到群集中的每个节点。 在以下示例中，*--pod cidr**10.244.0.0/16* 为第一个节点分配 *10.244.0.0/24*，为第二个节点分配 *10.244.1.0/24*，为第三节点分配 *10.244.2.0/24*。
    * 群集扩展或升级时，Azure 平台会继续向每个新节点分配 Pod IP 地址范围。
    
* *--docker-bridge-address* 允许 AKS 节点与基础管理平台进行通信。 此 IP 地址不能在群集的虚拟网络 IP 地址范围内，并且不应当与网络上使用的其他地址范围重叠。

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 3 \
    --network-plugin kubenet \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --pod-cidr 10.244.0.0/16 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal <appId> \
    --client-secret <password>
```

> [!Note]
> 如果希望启用 AKS 群集以包括 [Calico 网络策略][calico-network-policies]，可以使用以下命令。

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 3 \
    --network-plugin kubenet --network-policy calico \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --pod-cidr 10.244.0.0/16 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal <appId> \
    --client-secret <password>
```

创建 AKS 群集时，将创建网络安全组和路由表。 这些网络资源可以通过 AKS 控制平面进行管理。 网络安全组自动与节点上的虚拟 NIC 相关联。 路由表自动与虚拟网络子网相关联。 创建和公开服务时，网络安全组规则和路由表会自动更新。

## <a name="next-steps"></a>后续步骤

在现有虚拟网络子网中部署 AKS 群集后，现在可以像平时一样使用该群集。 开始使用[Azure Dev Spaces 生成应用][dev-spaces]、[使用 Helm 部署现有应用][use-helm]或[使用 Helm 创建新应用][develop-helm]。

<!-- LINKS - External -->
[dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet
[Calico-network-policies]: https://docs.projectcalico.org/v3.9/security/calico-network-policy

<!-- LINKS - Internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-network-concepts]: concepts-network.md
[az-group-create]: /cli/azure/group#az-group-create
[az-network-vnet-create]: /cli/azure/network/vnet#az-network-vnet-create
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-network-vnet-show]: /cli/azure/network/vnet#az-network-vnet-show
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-aks-create]: /cli/azure/aks#az-aks-create
[develop-helm]: quickstart-helm.md
[use-helm]: kubernetes-helm.md
[virtual-nodes]: virtual-nodes-cli.md
[vnet-peering]: ../virtual-network/virtual-network-peering-overview.md
[express-route]: ../expressroute/expressroute-introduction.md
[network-comparisons]: concepts-network.md#compare-network-models
