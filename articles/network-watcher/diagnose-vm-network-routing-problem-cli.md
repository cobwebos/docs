---
title: 诊断虚拟机网络路由问题 - Azure CLI | Microsoft Docs
description: 本文介绍如何使用 Azure 网络观察程序的“下一个跃点”功能来诊断虚拟机网络路由问题。
services: network-watcher
documentationcenter: network-watcher
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I need to diagnose virtual machine (VM) network routing problem that prevents communication to different destinations.
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: network-watcher
ms.workload: infrastructure
ms.date: 04/20/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: fcb7ec2e40b5c0e8794d2f4d70395dcbecca019c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
ms.locfileid: "32182157"
---
# <a name="diagnose-a-virtual-machine-network-routing-problem---azure-cli"></a>诊断虚拟机网络路由问题 - Azure CLI

本文首先部署虚拟机 (VM)，然后检查其与 IP 地址和 URL 的通信。 请确定通信失败的原因以及解决方法。

如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本文要求运行 Azure CLI 2.0.28 或更高版本。 要查找已安装的版本，请运行 `az --version`。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0](/cli/azure/install-azure-cli)。 验证 CLI 版本以后，请运行 `az login`，以便创建与 Azure 的连接。 本文中的 CLI 命令已格式化，适合在 Bash Shell 中运行。

## <a name="create-a-vm"></a>创建 VM

在创建 VM 之前，必须创建该 VM 所属的资源组。 使用 [az group create](/cli/azure/group#az_group_create) 创建资源组。 以下示例在 eastus 位置创建名为 myResourceGroup 的资源组：

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

使用 [az vm create](/cli/azure/vm#az_vm_create) 创建 VM。 如果默认密钥位置中尚不存在 SSH 密钥，该命令会创建它们。 若要使用特定的一组密钥，请使用 `--ssh-key-value` 选项。 以下示例创建名为 myVm 的 VM：

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image UbuntuLTS \
  --generate-ssh-keys
```

创建 VM 需要几分钟时间。 在创建好 VM 且 CLI 返回输出之前，请勿继续执行剩余的步骤。

## <a name="test-network-communication"></a>测试网络通信

若要通过网络观察程序测试网络通信，必须先在要测试的 VM 所在的区域中启用网络观察程序，然后使用网络观察程序的“下一个跃点”功能来测试通信。

### <a name="enable-network-watcher"></a>启用网络观察程序

如果已在“美国东部”区域中启用网络观察程序，请跳到[使用下一跃点](#use-next-hop)。 使用 [az network watcher configure](/cli/azure/network/watcher#az-network-watcher-configure) 命令在“美国东部”区域中创建网络观察程序：

```azurecli-interactive
az network watcher configure \
  --resource-group NetworkWatcherRG \
  --locations eastus \
  --enabled
```

### <a name="use-next-hop"></a>使用下一个跃点

Azure 自动创建到默认目标的路由。 可以创建自定义路由来覆盖默认路由。 有时，自定义路由可能会导致通信故障。 要测试来自 VM 的路由，请使用 [az network watcher show-next-hop](/cli/azure/network/watcher?view=azure-cli-latest#az-network-watcher-show-next-hop) 确定流量发送到特定地址时的下一个路由跃点。

测试从 VM 发往 www.bing.com 的某个 IP 地址的出站通信：

```azurecli-interactive
az network watcher show-next-hop \
  --dest-ip 13.107.21.200 \
  --resource-group myResourceGroup \
  --source-ip 10.0.0.4 \
  --vm myVm \
  --nic myVmVMNic \
  --out table
```

数秒钟后，输出结果指示 nextHopType 为“Internet”，routeTableId 为“系统路由”。 此结果指示存在通往目标的有效路由。

测试从 VM 发往 172.31.0.100 的出站通信：

```azurecli-interactive
az network watcher show-next-hop \
  --dest-ip 172.31.0.100 \
  --resource-group myResourceGroup \
  --source-ip 10.0.0.4 \
  --vm myVm \
  --nic myVmVMNic \
  --out table
```

输出结果指示“nextHopType”为“无”，“routeTableId”仍为“系统路由”。 此结果指示，虽然存在有效的通往目标的系统路由，但是没有将流量路由到目标的下一跃点。

## <a name="view-details-of-a-route"></a>查看路由详细信息

若要进一步分析路由情况，请使用 [az network nic show-effective-route-table](/cli/azure/network/nic#az-network-nic-show-effective-route-table) 命令查看网络接口的有效路由：

```azurecli-interactive
az network nic show-effective-route-table \
  --resource-group myResourceGroup \
  --name myVmVMNic
```

返回的输出中包含以下文本：

```azurecli
{
  "additionalProperties": {
    "disableBgpRoutePropagation": false
  },
  "addressPrefix": [
    "0.0.0.0/0"
  ],
  "name": null,
  "nextHopIpAddress": [],
  "nextHopType": "Internet",
  "source": "Default",
  "state": "Active"
},
```

使用 `az network watcher show-next-hop` 命令测试在[使用下一跃点](#use-next-hop)中发送到的 13.107.21.200 的出站通信时，地址前缀为 0.0.0.0/0** 的路由用于将流量路由到该地址，因为该输出中没有其他路由包含该地址。 默认情况下，未在另一路由的地址前缀中指定的所有地址都会路由到 Internet。

但是，使用 `az network watcher show-next-hop` 命令测试发送到 172.31.0.100 的出站通信时，结果显示没有下一跃点类型。 返回的输出中包含以下文本：

```azurecli
{
  "additionalProperties": {
    "disableBgpRoutePropagation": false
      },
  "addressPrefix": [
    "172.16.0.0/12"
  ],
  "name": null,
  "nextHopIpAddress": [],
  "nextHopType": "None",
  "source": "Default",
  "state": "Active"
},
```

从 `az network watcher nic show-effective-route-table` 命令的输出结果中可以看到，虽然有一个到 172.16.0.0/12 前缀的默认路由（其中包括地址 172.31.0.100），但“nextHopType”为“无”。 Azure 会创建到 172.16.0.0/12 的默认路由，但不会无缘无故地指定下一跃点类型。 在特定情况下，例如在已将 172.16.0.0/12 地址范围添加到虚拟网络的地址空间的情况下，Azure 会将路由的“nextHopType”更改为“虚拟网络”。 此时进行检查会将“nextHopType”显示为“虚拟网络”。

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组及其包含的所有资源，可以使用 [az group delete](/cli/azure/group#az_group_delete) 将其删除：

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>后续步骤

本文介绍了如何创建 VM 并根据该 VM 诊断网络路由问题。 同时说明了 Azure 可以创建多个默认路由，并且还测试了到两个不同目标的路由。 详细了解 [Azure 中的路由](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)以及如何[创建自定义路由](../virtual-network/manage-route-table.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-route)。

对于出站 VM 连接，还可以使用网络观察程序的[连接故障排除](network-watcher-connectivity-cli.md)功能来确定延迟、VM 和终结点之间获得允许的和被拒绝的网络流量。 可以使用网络观察程序的连接监视器功能监视 VM 和终结点（例如 IP 地址或 URL）之间在某段时间的通信情况。 如需了解如何操作，请参阅[监视网络连接](connection-monitor.md)。