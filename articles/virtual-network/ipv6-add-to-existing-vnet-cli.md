---
title: 在 Azure 虚拟网络中将 IPv6 添加到 IPv4 应用程序-Azure CLI
titlesuffix: Azure Virtual Network
description: 本文介绍如何使用 Azure CLI 将 IPv6 地址部署到 Azure 虚拟网络中的现有应用程序。
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/23/2019
ms.author: kumud
ms.openlocfilehash: 0631ea51894e7e0642a55cedee54422fddab623b
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2019
ms.locfileid: "72952103"
---
# <a name="add-ipv6-to-an-ipv4-application-in-azure-virtual-network---azure-cli-preview"></a>在 Azure 虚拟网络中将 IPv6 添加到 IPv4 应用程序-Azure CLI （预览版）

本文说明如何使用 Azure CLI 将 IPv6 地址添加到使用 Azure 虚拟网络中的 IPv4 公共 IP 地址标准负载均衡器的应用程序。 就地升级包括虚拟网络和子网、使用 IPv4 + IPV6 前端配置标准负载均衡器、具有 IPv4 + IPv6 配置、网络安全组和公共 Ip 的 Nic 的 Vm。

> [!Important]
> Azure 虚拟网络的 IPv6 支持当前提供公共预览版。 此预览版在提供时没有附带服务级别协议，不建议用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果决定在本地安装并使用 Azure CLI，本快速入门要求使用 Azure CLI 2.0.28 或更高版本。 若要查找已安装的版本，请运行 `az --version`。 有关安装或升级信息，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="prerequisites"></a>必备组件

### <a name="register-the-service"></a>注册服务

在 Azure 中部署双堆栈应用程序之前，必须使用以下 Azure CLI 来配置此预览功能的订阅：

```azurecli
az feature register --name AllowIPv6VirtualNetwork --namespace Microsoft.Network
az feature register --name AllowIPv6CAOnStandardLB --namespace Microsoft.Network
```
功能注册最多需要 30 分钟才能完成。 可以通过运行以下 Azure CLI 命令来检查注册状态：

```azurelci
az feature show --name AllowIPv6VirtualNetwork --namespace Microsoft.Network
az feature show --name AllowIPv6CAOnStandardLB --namespace Microsoft.Network
```
注册完成后，运行以下命令：

```azurelci
az provider register --namespace Microsoft.Network
```

### <a name="create-a-standard-load-balancer"></a>创建标准负载均衡器
本文假设你按[快速入门：创建标准负载均衡器-Azure CLI](../load-balancer/quickstart-load-balancer-standard-public-cli.md)中所述部署了标准负载均衡器。

## <a name="create-ipv6-addresses"></a>创建 IPv6 地址

为标准负载均衡器创建公共 IPv6 地址，其中包含[az network 公共 ip create](/cli/azure/network/public-ip) 。 以下示例在*myResourceGroupSLB*资源组中创建名为*PublicIP_v6*的 IPv6 公共 IP 地址：

```azurecli
  
az network public-ip create \
--name PublicIP_v6 \
--resource-group MyResourceGroupSLB \
--location EastUS \
--sku Standard \
--allocation-method static \
--version IPv6
```

## <a name="configure-ipv6-load-balancer-frontend"></a>配置 IPv6 负载均衡器前端

使用[az network lb 前端 IP Create](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip?view=azure-cli-latest#az-network-lb-frontend-ip-create)配置具有新 IPv6 ip 地址的负载均衡器，如下所示：

```azurecli
az network lb frontend-ip create \
--lb-name myLoadBalancer \
--name dsLbFrontEnd_v6 \
--resource-group MyResourceGroupSLB \
--public-ip-address PublicIP_v6
```

## <a name="configure-ipv6-load-balancer-backend-pool"></a>配置 IPv6 负载均衡器后端池

使用[az network lb address](https://docs.microsoft.com/cli/azure/network/lb/address-pool?view=azure-cli-latest#az-network-lb-address-pool-create) create 为包含 IPv6 地址的 nic 创建后端池，如下所示：

```azurecli
az network lb address-pool create \
--lb-name myLoadBalancer \
--name dsLbBackEndPool_v6 \
--resource-group MyResourceGroupSLB
```

## <a name="configure-ipv6-load-balancer-rules"></a>配置 IPv6 负载均衡器规则

用[az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create)创建 IPv6 负载均衡器规则。

```azurecli
az network lb rule create \
--lb-name myLoadBalancer \
--name dsLBrule_v6 \
--resource-group MyResourceGroupSLB \
--frontend-ip-name dsLbFrontEnd_v6 \
--protocol Tcp \
--frontend-port 80 \
--backend-port 80 \
--backend-pool-name dsLbBackEndPool_v6
```

## <a name="add-ipv6-address-ranges"></a>添加 IPv6 地址范围

将 IPv6 地址范围添加到托管负载均衡器的虚拟网络和子网，如下所示：

```azurecli
az network vnet update \
--name myVnet  `
--resource-group MyResourceGroupSLB \
--address-prefixes  "10.0.0.0/16"  "ace:cab:deca::/48"

az network vnet subnet update \
--vnet-name myVnet \
--name mySubnet \
--resource-group MyResourceGroupSLB \
--address-prefixes  "10.0.0.0/24"  "ace:cab:deca:deed::/64"  
```

## <a name="add-ipv6-configuration-to-nics"></a>将 IPv6 配置添加到 Nic

使用[az network nic ip-https create](https://docs.microsoft.com/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-create)配置具有 IPv6 地址的 VM nic，如下所示：

```azurecli
az network nic ip-config create \
--name dsIp6Config_NIC1 \
--nic-name myNicVM1 \
--resource-group MyResourceGroupSLB \
--vnet-name myVnet \
--subnet mySubnet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB

az network nic ip-config create \
--name dsIp6Config_NIC2 \
--nic-name myNicVM2 \
--resource-group MyResourceGroupSLB \
--vnet-name myVnet \
--subnet mySubnet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name myLoadBalancer

az network nic ip-config create \
--name dsIp6Config_NIC3 \
--nic-name myNicVM3 \
--resource-group MyResourceGroupSLB \
--vnet-name myVnet \
--subnet mySubnet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name myLoadBalancer

```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>在 Azure 门户中查看 IPv6 双堆栈虚拟网络
可以在 Azure 门户中查看 IPv6 双堆栈虚拟网络，如下所示：
1. 在门户的搜索栏中，输入 " *myVnet*"。
2. 当 " **myVnet** " 出现在搜索结果中时，请选择它。 这将启动名为*myVNet*的双堆栈虚拟网络的 "**概述**" 页。 双堆栈虚拟网络显示三个 Nic，其中包含位于名为*mySubnet*的双堆栈子网中的 IPv4 和 IPv6 配置。

  ![Azure 中的 IPv6 双堆栈虚拟网络](./media/ipv6-add-to-existing-vnet-powershell/ipv6-dual-stack-vnet.png)

> [!NOTE]
> 适用于 Azure 虚拟网络的 IPv6 可在此预览版本的只读 Azure 门户中使用。

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组、VM 和所有相关的资源，可以使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 命令将其删除。

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyAzureResourceGroupSLB
```

## <a name="next-steps"></a>后续步骤

本文介绍如何使用 IPv4 前端 IP 配置将现有标准负载均衡器更新为双堆栈（IPv4 和 IPv6）配置。 还将 IPv6 配置添加到后端池中 Vm 的 Nic。 若要了解有关 Azure 虚拟网络中 IPv6 支持的详细信息，请参阅[什么是适用于 Azure 虚拟网络的 ipv6？](ipv6-overview.md)
