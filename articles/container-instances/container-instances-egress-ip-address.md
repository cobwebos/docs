---
title: 配置静态出站 IP
description: 为使用防火墙公共 IP 地址进行入口和出口的 Azure 容器实例工作负荷配置 Azure 防火墙和用户定义的路由
ms.topic: article
ms.date: 07/16/2020
author: dlepow
ms.author: danlep
ms.openlocfilehash: d748e3e6239ba913afc5b8aadd7e85dcd1027c04
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87023698"
---
# <a name="configure-a-single-public-ip-address-for-outbound-and-inbound-traffic-to-a-container-group"></a>为容器组的出站和入站流量配置单个公共 IP 地址

设置具有面向外部的 IP 地址的[容器组](container-instances-container-groups.md)允许外部客户端使用该 ip 地址访问组中的容器。 例如，浏览器可以访问在容器中运行的 web 应用。 然而，当前一个容器组使用不同的 IP 地址进行出站通信。 此出口 IP 地址不以编程方式公开，这使得容器组监视和配置客户端防火墙规则变得更加复杂。

本文介绍如何在与[Azure 防火墙](../firewall/overview.md)集成的[虚拟网络](container-instances-virtual-network-concepts.md)中配置容器组。 通过将用户定义的路由设置到容器组和防火墙规则，你可以路由和标识到容器组的流量。 容器组入口和出口使用防火墙的公共 IP 地址。 部署到 Azure 容器实例的虚拟网络子网中部署的多个容器组可以使用单个出口 IP 地址。

本文使用 Azure CLI 为此方案创建资源：

* 部署在[虚拟网络中](container-instances-vnet.md)的委托子网上的容器组 
* 使用静态公共 IP 地址部署在网络中的 Azure 防火墙
* 容器组子网中的用户定义路由
* 用于防火墙入口的 NAT 规则，以及出口的应用程序规则

然后通过防火墙验证示例容器组中的入口和出口。

## <a name="deploy-aci-in-a-virtual-network"></a>在虚拟网络中部署 ACI

在典型情况下，你可能已经有一个要在其中部署容器组的 Azure 虚拟网络。 出于演示目的，在创建容器组时，以下命令将创建一个虚拟网络和子网。 此子网将委托给 Azure 容器实例。 

容器组从映像运行小型 web 应用 `aci-helloworld` 。 如本文档中的其他文章所述，此映像会打包一个以 Node.js 编写的、可提供静态 HTML 页面的小型 Web 应用。

如果需要，请先使用[az group create][az-group-create]命令创建 Azure 资源组。 例如：

```azurecli
az group create --name myResourceGroup --location eastus
```

若要简化以下命令示例，请将环境变量用于资源组的名称：

```console
export RESOURCE_GROUP_NAME=myResourceGroup
```

用[az container create][az-container-create]命令创建容器组：

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
> 调整 `--subnet address-prefix` 子网中所需的 IP 地址空间的值。 支持的最小子网为/29，此子网提供八个 IP 地址。 某些 IP 地址保留供 Azure 使用。

若要在后面的步骤中使用，请通过运行 [az container show] [az-show] 命令获取容器组的专用 IP 地址：

```azurecli
ACI_PRIVATE_IP="$(az container show --name appcontainer \
  --resource-group $RESOURCE_GROUP_NAME \
  --query ipAddress.ip --output tsv)"
```

## <a name="deploy-azure-firewall-in-network"></a>在网络中部署 Azure 防火墙

在以下部分中，使用 Azure CLI 在虚拟网络中部署 Azure 防火墙。 有关背景知识，请参阅[教程：使用 Azure 门户部署和配置 Azure 防火墙](../firewall/deploy-cli.md)。

首先，使用[az network vnet subnet create][az-network-vnet-subnet-create]为防火墙添加名为 AzureFirewallSubnet 的子网。 AzureFirewallSubnet 是此子网*所需*的名称。

```azurecli
az network vnet subnet create \
  --name AzureFirewallSubnet \
  --resource-group $RESOURCE_GROUP_NAME \
  --vnet-name aci-vnet   \
  --address-prefix 10.0.1.0/26
```

使用以下[Azure CLI 命令](../firewall/deploy-cli.md)在子网中创建防火墙。

如果尚未安装，请使用[az extension add][az-extension-add]命令将防火墙扩展添加到 Azure CLI：

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

使用[az network firewall update][az-network-firewall-update]命令更新防火墙配置：

```azurecli
az network firewall update \
  --name myFirewall \
  --resource-group $RESOURCE_GROUP_NAME
```

使用[az network firewall IP-config list][az-network-firewall-ip-config-list]命令获取防火墙的专用 IP 地址。 此专用 IP 地址在后面的命令中使用。


```azurecli
FW_PRIVATE_IP="$(az network firewall ip-config list \
  --resource-group $RESOURCE_GROUP_NAME \
  --firewall-name myFirewall \
  --query "[].privateIpAddress" --output tsv)"
```
使用[az network 公共 ip show][az-network-public-ip-show]命令获取防火墙的公共 ip 地址。 此公共 IP 地址用于后面的命令中。

```azurecli
FW_PUBLIC_IP="$(az network public-ip show \
  --name fw-pip \
  --resource-group $RESOURCE_GROUP_NAME \
  --query ipAddress --output tsv)"
```

## <a name="define-user-defined-route-on-aci-subnet"></a>定义 ACI 子网上用户定义的路由

在 ACI 子网上定义使用定义的路由，以将流量转移到 Azure 防火墙。 有关详细信息，请参阅[路由网络流量](../virtual-network/tutorial-create-route-table-cli.md)。 

### <a name="create-route-table"></a>创建路由表

首先，运行以下[az network route create][az-network-route-table-create]命令，以创建路由表。 在虚拟网络所在的同一区域中创建路由表。

```azurecli
az network route-table create \
  --name Firewall-rt-table \
  --resource-group $RESOURCE_GROUP_NAME \
  --location eastus \
  --disable-bgp-route-propagation true
```

### <a name="create-route"></a>创建路由

运行[az network route 路由 create][az-network-route-table-route-create]以在路由表中创建路由。 若要将流量路由到防火墙，请将 "下一跃点类型" 设置为 `VirtualAppliance` ，并将防火墙的专用 IP 地址作为下一个跃点地址进行传递。

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

运行[az network vnet subnet update][az-network-vnet-subnet-update]命令，将路由表与委托给 Azure 容器实例的子网相关联。

```azurecli
az network vnet subnet update \
  --name aci-subnet \
  --resource-group $RESOURCE_GROUP_NAME \
  --vnet-name aci-vnet \
  --address-prefixes 10.0.0.0/24 \
  --route-table Firewall-rt-table
```

## <a name="configure-rules-on-firewall"></a>在防火墙上配置规则

默认情况下，Azure 防火墙拒绝入站和出站流量。 

### <a name="configure-nat-rule-on-firewall-to-aci-subnet"></a>将防火墙上的 NAT 规则配置到 ACI 子网

在防火墙上创建[NAT 规则](../firewall/rule-processing.md)，以将入站 internet 流量转换和筛选到之前在网络中启动的应用程序容器。 有关详细信息，请参阅[通过 Azure 防火墙筛选入站 Internet 流量 DNAT](../firewall/tutorial-firewall-dnat.md)

使用[az network firewall nat-t create][az-network-firewall-nat-rule-create]命令创建 NAT 规则和集合：

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

根据需要添加 NAT 规则，以将流量筛选为子网中的其他 IP 地址。 例如，子网中的其他容器组可以为入站流量公开 IP 地址，或在重启后将其他内部 IP 地址分配到容器组。

### <a name="create-outbound-application-rule-on-the-firewall"></a>创建防火墙上的出站应用程序规则

运行以下[az network firewall application-rule create][az-network-firewall-application-rule-create]命令，在防火墙上创建出站规则。 此示例规则允许从委托给 Azure 容器实例的子网访问到 FQDN `checkip.dyndns.org` 。 稍后的步骤中将使用对该站点的 HTTP 访问，以确认 Azure 容器实例中的出口 IP 地址。

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

以下部分验证委托给 Azure 容器实例的子网是否已正确配置在 Azure 防火墙后面。 前面的步骤会将传入流量路由到子网，并将来自子网的传出流量路由到防火墙。

### <a name="test-ingress-to-a-container-group"></a>到容器组的测试入口

通过浏览到防火墙的公共 IP 地址，测试对虚拟网络中运行的*appcontainer*的入站访问。 以前，你已在变量 $FW _PUBLIC_IP 中存储公共 IP 地址：

```bash
echo $FW_PUBLIC_IP
```

输出类似于：

```console
52.142.18.133
```

如果正确配置了防火墙上的 NAT 规则，当你在浏览器中输入防火墙的公共 IP 地址时，将看到以下内容：

:::image type="content" source="media/container-instances-egress-ip-address/aci-ingress-ip-address.png" alt-text="浏览防火墙的公共 IP 地址":::

### <a name="test-egress-from-a-container-group"></a>测试容器组中的出口


将以下示例容器部署到虚拟网络中。 当它运行时，它将向发出一个 HTTP 请求 `http://checkip.dyndns.org` ，其中显示发件人的 ip 地址（出口 ip 地址）。 如果正确配置了防火墙上的应用程序规则，则返回防火墙的公共 IP 地址。

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

查看容器日志以确认 IP 地址与防火墙的公共 IP 地址相同。

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

本文介绍如何在 Azure 防火墙后面的虚拟网络中设置容器组。 在防火墙上配置了用户定义的路由、NAT 和应用程序规则。 通过使用此配置，可以设置单个静态 IP 地址，用于从 Azure 容器实例进行入口和出口。

有关管理流量和保护 Azure 资源的详细信息，请参阅[Azure 防火墙](../firewall/index.yml)文档。



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






