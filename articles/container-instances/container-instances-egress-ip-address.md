---
title: 配置静态出站 IP
description: 为针对入口和出口使用防火墙公共 IP 地址的 Azure 容器实例工作负荷配置 Azure 防火墙和用户定义的路由
ms.topic: article
ms.date: 07/16/2020
ms.openlocfilehash: 497645b9fe7f908cc9b8b4d7ed0ba5e201570160
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89566562"
---
# <a name="configure-a-single-public-ip-address-for-outbound-and-inbound-traffic-to-a-container-group"></a>为容器组的出站和入站流量配置单个公共 IP 地址

使用面向外部的 IP 地址设置[容器组](container-instances-container-groups.md)可以让外部客户端使用该 IP 地址访问组中的容器。 例如，浏览器可以访问在容器中运行的 Web 应用。 但在目前，容器组将另一 IP 地址用于出站流量。 此出口 IP 地址不以编程方式公开，这使得容器组监视与客户端防火墙规则配置变得更加复杂。

本文介绍如何通过相关步骤在集成了 [Azure 防火墙](../firewall/overview.md)的[虚拟网络](container-instances-virtual-network-concepts.md)中配置容器组。 通过设置通往容器组的用户定义的路由和防火墙规则，你可以路由并标识流入和流出该容器组的流量。 容器组入口和出口使用防火墙的公共 IP 地址。 在虚拟网络的子网（已委托到 Azure 容器实例）中部署的多个容器组可以使用单个出口 IP 地址。

本文使用 Azure CLI 为此方案创建资源：

* 部署在[虚拟网络](container-instances-vnet.md)的已委托子网中的容器组 
* 使用静态公共 IP 地址部署在网络中的 Azure 防火墙
* 容器组的子网中的用户定义路由
* 用于防火墙入口的 NAT 规则，以及用于出口的应用程序规则

然后通过防火墙验证示例容器组中的入口和出口。

## <a name="deploy-aci-in-a-virtual-network"></a>在虚拟网络中部署 ACI

通常情况下，你可能已经有一个要在其中部署容器组的 Azure 虚拟网络。 出于演示目的，在创建容器组后使用以下命令创建虚拟网络和子网。 此子网委托给 Azure 容器实例。 

容器组从 `aci-helloworld` 映像运行小型 Web 应用。 如本文档中的其他文章所述，此映像会打包一个以 Node.js 编写的、可提供静态 HTML 页面的小型 Web 应用。

如果需要一个，请先使用 [az group create][az-group-create] 命令创建 Azure 资源组。 例如：

```azurecli
az group create --name myResourceGroup --location eastus
```

若要简化以下命令示例，请将环境变量用于资源组的名称：

```console
export RESOURCE_GROUP_NAME=myResourceGroup
```

使用 [az container create][az-container-create] 命令创建容器组：

```azurecli
az container create \
  --name appcontainer \
  --resource-group $RESOURCE_GROUP_NAME \
  --image mcr.microsoft.com/azuredocs/aci-helloworld \
  --vnet aci-vnet \
  --vnet-address-prefix 10.0.0.0/16 \
  --subnet aci-subnet \
  --subnet-address-prefix 10.0.0.0/24
```

> [!TIP]
> 针对子网中你所需的 IP 地址空间调整 `--subnet address-prefix` 的值。 支持的最小子网为 /29，此子网提供八个 IP 地址。 某些 IP 地址已保留供 Azure 使用。

为了用于后面的步骤，请通过运行 [az container show][az-container-show] 命令获取容器组的专用 IP 地址：

```azurecli
ACI_PRIVATE_IP="$(az container show --name appcontainer \
  --resource-group $RESOURCE_GROUP_NAME \
  --query ipAddress.ip --output tsv)"
```

## <a name="deploy-azure-firewall-in-network"></a>在网络中部署 Azure 防火墙

在以下部分，请使用 Azure CLI 在虚拟网络中部署 Azure 防火墙。 有关背景，请参阅[教程：使用 Azure 门户部署和配置 Azure 防火墙](../firewall/deploy-cli.md)。

首先，使用 [az network vnet subnet create][az-network-vnet-subnet-create] 为防火墙添加名为 AzureFirewallSubnet 的子网。 AzureFirewallSubnet 是此子网的必需名称。

```azurecli
az network vnet subnet create \
  --name AzureFirewallSubnet \
  --resource-group $RESOURCE_GROUP_NAME \
  --vnet-name aci-vnet   \
  --address-prefix 10.0.1.0/26
```

使用以下 [Azure CLI 命令](../firewall/deploy-cli.md)在子网中创建防火墙。

如果尚未安装，请使用 [az extension add][az-extension-add] 命令将防火墙扩展添加到 Azure CLI：

```azurecli
az extension add --name azure-firewall
```

创建防火墙资源：

```azurecli
az network firewall create \
  --name myFirewall \
  --resource-group $RESOURCE_GROUP_NAME \
  --location eastus

az network public-ip create \
  --name fw-pip \
  --resource-group $RESOURCE_GROUP_NAME \
  --location eastus \
  --allocation-method static \
  --sku standard
    
az network firewall ip-config create \
  --firewall-name myFirewall \
  --name FW-config \
  --public-ip-address fw-pip \
  --resource-group $RESOURCE_GROUP_NAME \
  --vnet-name aci-vnet
```

使用 [az network firewall update][az-network-firewall-update] 命令更新防火墙配置：

```azurecli
az network firewall update \
  --name myFirewall \
  --resource-group $RESOURCE_GROUP_NAME
```

使用 [az network firewall ip-config list][az-network-firewall-ip-config-list] 命令获取防火墙的专用 IP 地址。 此专用 IP 地址在后面的命令中使用。


```azurecli
FW_PRIVATE_IP="$(az network firewall ip-config list \
  --resource-group $RESOURCE_GROUP_NAME \
  --firewall-name myFirewall \
  --query "[].privateIpAddress" --output tsv)"
```
使用 [az network public-ip show][az-network-public-ip-show] 命令获取防火墙的公共 IP 地址。 此公共 IP 地址在后面的命令中使用。

```azurecli
FW_PUBLIC_IP="$(az network public-ip show \
  --name fw-pip \
  --resource-group $RESOURCE_GROUP_NAME \
  --query ipAddress --output tsv)"
```

## <a name="define-user-defined-route-on-aci-subnet"></a>在 ACI 子网上定义用户定义的路由

在 ACI 子网上定义用户定义的路由，以将流量转到 Azure 防火墙。 有关详细信息，请参阅[路由网络流量](../virtual-network/tutorial-create-route-table-cli.md)。 

### <a name="create-route-table"></a>创建路由表

首先，运行以下 [az network route-table create][az-network-route-table-create] 命令以创建路由表。 在虚拟网络所在的区域中创建路由表。

```azurecli
az network route-table create \
  --name Firewall-rt-table \
  --resource-group $RESOURCE_GROUP_NAME \
  --location eastus \
  --disable-bgp-route-propagation true
```

### <a name="create-route"></a>创建路由

运行 [az network-route-table route create][az-network-route-table-route-create] 以在路由表中创建一个路由。 若要将流量路由到防火墙，请将下一跃点类型设置为 `VirtualAppliance`，并将防火墙的专用 IP 地址作为下一个跃点地址进行传递。

```azurecli
az network route-table route create \
  --resource-group $RESOURCE_GROUP_NAME \
  --name DG-Route \
  --route-table-name Firewall-rt-table \
  --address-prefix 0.0.0.0/0 \
  --next-hop-type VirtualAppliance \
  --next-hop-ip-address $FW_PRIVATE_IP
```

### <a name="associate-route-table-to-aci-subnet"></a>将路由表关联到 ACI 子网

运行 [az network vnet subnet update][az-network-vnet-subnet-update] 命令，以将路由表与委托给 Azure 容器实例的子网相关联。

```azurecli
az network vnet subnet update \
  --name aci-subnet \
  --resource-group $RESOURCE_GROUP_NAME \
  --vnet-name aci-vnet \
  --address-prefixes 10.0.0.0/24 \
  --route-table Firewall-rt-table
```

## <a name="configure-rules-on-firewall"></a>在防火墙上配置规则

默认情况下，Azure 防火墙拒绝（阻止）入站和出站流量。 

### <a name="configure-nat-rule-on-firewall-to-aci-subnet"></a>在 ACI 子网的防火墙上配置 NAT 规则

在防火墙上创建一项 [NAT 规则](../firewall/rule-processing.md)，用于转换和筛选发往此前在网络中启动的应用程序容器的入站 Internet 流量。 有关详细信息，请参阅[通过 Azure 防火墙 DNAT 筛选入站 Internet 流量](../firewall/tutorial-firewall-dnat.md)

使用 [az network firewall nat-rule create][az-network-firewall-nat-rule-create] 命令创建 NAT 规则和集合：

```azurecli
az network firewall nat-rule create \
  --firewall-name myFirewall \
  --collection-name myNATCollection \
  --action dnat \
  --name myRule \
  --protocols TCP \
  --source-addresses '*' \
  --destination-addresses $FW_PUBLIC_IP \
  --destination-ports 80 \
  --resource-group $RESOURCE_GROUP_NAME \
  --translated-address $ACI_PRIVATE_IP \
  --translated-port 80 \
  --priority 200
```

根据需要添加 NAT 规则，筛选发往子网中的其他 IP 地址的流量。 例如，子网中的其他容器组可以为入站流量公开 IP 地址，或者，其他内部 IP 地址可以在重启后分配给该容器组。

### <a name="create-outbound-application-rule-on-the-firewall"></a>在防火墙上创建出站应用程序规则

运行以下 [az network firewall application-rule create][az-network-firewall-application-rule-create] 命令，以在防火墙上创建出站规则。 此示例规则允许从委托给 Azure 容器实例的子网访问 FQDN `checkip.dyndns.org`。 稍后的步骤中将使用对该站点的 HTTP 访问来确认 Azure 容器实例中的出口 IP 地址。

```azurecli
az network firewall application-rule create \
  --collection-name myAppCollection \
  --firewall-name myFirewall \
  --name Allow-CheckIP \
  --protocols Http=80 Https=443 \
  --resource-group $RESOURCE_GROUP_NAME \
  --target-fqdns checkip.dyndns.org \
  --source-addresses 10.0.0.0/24 \
  --priority 200 \
  --action Allow
```

## <a name="test-container-group-access-through-the-firewall"></a>通过防火墙测试容器组访问权限

以下部分验证委托给 Azure 容器实例的子网是否已在 Azure 防火墙后面正确配置。 前面的步骤已通过防火墙路由了发往子网的传入流量和来自子网的传出流量。

### <a name="test-ingress-to-a-container-group"></a>测试容器组的入口

通过浏览到防火墙的公共 IP 地址，测试对虚拟网络中运行的 appcontainer 的入站访问。 以前，你已在变量 $FW_PUBLIC_IP 中存储公共 IP 地址：

```bash
echo $FW_PUBLIC_IP
```

输出类似于：

```console
52.142.18.133
```

如果正确配置了防火墙上的 NAT 规则，则当你在浏览器中输入防火墙的公共 IP 地址时，会看到以下内容：

:::image type="content" source="media/container-instances-egress-ip-address/aci-ingress-ip-address.png" alt-text="浏览到防火墙的公共 IP 地址":::

### <a name="test-egress-from-a-container-group"></a>测试容器组的出口


将以下示例容器部署到虚拟网络中。 它在运行时会将单个 HTTP 请求发送到 `http://checkip.dyndns.org`，后者会显示发送方的 IP 地址（出口 IP 地址）。 如果正确配置了防火墙上的应用程序规则，则会返回防火墙的公共 IP 地址。

```azurecli
az container create \
  --resource-group $RESOURCE_GROUP_NAME \
  --name testegress \
  --image mcr.microsoft.com/azuredocs/aci-tutorial-sidecar \
  --command-line "curl -s http://checkip.dyndns.org" \
  --restart-policy OnFailure \
  --vnet aci-vnet \
  --subnet aci-subnet
```

查看容器日志以确认此 IP 地址是否与防火墙的公共 IP 地址相同。

```azurecli
az container logs \
  --resource-group $RESOURCE_GROUP_NAME \
  --name testegress 
```

输出类似于：

```console
<html><head><title>Current IP Check</title></head><body>Current IP Address: 52.142.18.133</body></html>
```

## <a name="next-steps"></a>后续步骤

本文介绍了如何在 Azure 防火墙后面的虚拟网络中设置容器组。 此外，还说明了如何在防火墙上配置用户定义的路由、NAT 和应用程序规则。 通过使用此配置，可以为 Azure 容器实例的入口和出口设置单个静态 IP 地址。

若要详细了解如何管理流量和保护 Azure 资源，请参阅 [Azure 防火墙](../firewall/index.yml)文档。



[az-group-create]: /cli/azure/group#az-group-create
[az-container-create]: /cli/azure/container#az-container-create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-network-firewall-update]: /cli/azure/ext/azure-firewall/network/firewall#ext-azure-firewall-az-network-firewall-update
[az-network-public-ip-show]: /cli/azure/network/public-ip/#az-network-public-ip-show
[az-network-route-table-create]:/cli/azure/network/route-table/#az-network-route-table-create
[az-network-route-table-route-create]: /cli/azure/network/route-table/route#az-network-route-table-route-create
[az-network-firewall-ip-config-list]: /cli/azure/ext/azure-firewall/network/firewall/ip-config#ext-azure-firewall-az-network-firewall-ip-config-list
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-update
[az-container-exec]: /cli/azure/container#az-container-exec
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-vm-open-port]: /cli/azure/vm#az-vm-open-port
[az-vm-list-ip-addresses]: /cli/azure/vm#az-vm-list-ip-addresses
[az-network-firewall-application-rule-create]: /cli/azure/ext/azure-firewall/network/firewall/application-rule#ext-azure-firewall-az-network-firewall-application-rule-create
[az-network-firewall-nat-rule-create]: /cli/azure/ext/azure-firewall/network/firewall/nat-rule#ext-azure-firewall-az-network-firewall-nat-rule-create






