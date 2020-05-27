---
title: 为 VM 配置路由首选项 - Azure CLI
description: 了解如何在 Azure 命令行界面 (CLI) 中通过选择路由首选项来创建具有公共 IP 地址的 VM。
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2020
ms.author: mnayak
ms.openlocfilehash: 59583d6d9e643c7b3f4c86198da04bde6b2db324
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83595756"
---
# <a name="configure-routing-preference-for-a-vm-using-azure-cli"></a>使用 Azure CLI 为 VM 配置路由首选项

本文介绍如何为虚拟机配置路由首选项。 选择“Internet”作为路由首选项时，从 VM 发往 Internet 的流量会通过 ISP 网络进行路由。 默认路由是使用 Microsoft 全球网络。

本文介绍如何使用 Azure CLI 创建一个具有公共 IP 且设置为通过公共 Internet 路由流量的虚拟机。

> [!IMPORTANT]
> 路由首选项目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="register-the-feature-for-your-subscription"></a>为订阅注册此功能
路由首选项功能目前为预览版。 为订阅注册此功能，如下所示：
```azurecli
az feature register --namespace Microsoft.Network --name AllowRoutingPreferenceFeature
```
## <a name="create-a-resource-group"></a>创建资源组
1. 如果使用 Cloud Shell，请跳到步骤 2。 打开命令会话并使用 `az login` 登录到 Azure。
2. 使用“[az group create](/cli/azure/group#az-group-create)”命令创建资源组。 以下示例在美国东部 Azure 区域创建一个资源组：

    ```azurecli
    az group create --name myResourceGroup --location eastus
    ```

## <a name="create-a-public-ip-address"></a>创建公共 IP 地址
若要从 Internet 访问虚拟机，需要创建公共 IP 地址。 使用 [az network public-ip create](/cli/azure/network/public-ip) 创建公共 IP 地址。 以下示例在“美国东部”区域中创建路由首选项类型为“Internet”的公共 IP：

```azurecli
az network public-ip create \
--name MyRoutingPrefIP \
--resource-group MyResourceGroup \
--location eastus \
--ip-tags 'RoutingPreference=Internet' \
--sku STANDARD \
--allocation-method static \
--version IPv4
```

## <a name="create-network-resources"></a>创建网络资源

在部署 VM 之前，必须创建支持网络资源（网络安全组、虚拟网络和虚拟 NIC）。

### <a name="create-a-network-security-group"></a>创建网络安全组

使用 [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create) 为将要控制 VNet 中的入站和出站通信的规则创建网络安全组

```azurecli
az network nsg create \
--name myNSG  \
--resource-group MyResourceGroup \
--location eastus
```

### <a name="create-a-virtual-network"></a>创建虚拟网络

使用 [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-create) 创建虚拟网络。 以下示例创建一个子网为“mySubNet”且名称为“myVNET”的虚拟网络：

```azurecli
# Create a virtual network
az network vnet create \
--name myVNET \
--resource-group MyResourceGroup \
--location eastus

# Create a subnet
az network vnet subnet create \
--name mySubNet \
--resource-group MyResourceGroup \
--vnet-name myVNET \
--address-prefixes "10.0.0.0/24" \
--network-security-group myNSG
```

### <a name="create-a-nic"></a>创建 NIC

使用 [az network nic create](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) 为 VM 创建虚拟 NIC。 以下示例创建一个虚拟 NIC（将附加到 VM）。

```azurecli-interactive
# Create a NIC
az network nic create \
--name mynic  \
--resource-group MyResourceGroup \
--network-security-group myNSG  \
--vnet-name myVNET \
--subnet mySubNet  \
--private-ip-address-version IPv4 \
--public-ip-address MyRoutingPrefIP
```

## <a name="create-a-virtual-machine"></a>创建虚拟机

使用 [az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create) 创建 VM。 以下示例创建 Windows Server 2019 VM 和所需的虚拟网络组件（如果它们尚不存在）。

```azurecli
az vm create \
--name myVM \
--resource-group MyResourceGroup \
--nics mynic \
--size Standard_A2 \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest \
--admin-username myUserName
```

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组及其包含的所有资源，可以使用 [az group delete](/cli/azure/group#az-group-delete) 将其删除：

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>后续步骤

- 详细了解[公共 IP 地址中的路由首选项](routing-preference-overview.md)。
- 详细了解 Azure 中的[公共 IP 地址](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)。
- 详细了解[公共 IP 地址设置](virtual-network-public-ip-address.md#create-a-public-ip-address)。
