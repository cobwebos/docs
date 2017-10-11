---
title: "Azure CLI 脚本示例 - 通过网络虚拟设备路由流量 | Microsoft Docs"
description: "Azure CLI 脚本示例 - 通过防火墙网络虚拟设备路由流量。"
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: timlt
editor: tysonn
tags: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 07/07/2017
ms.author: jdial
ms.openlocfilehash: 78091b515c00591a4af8d807945475b6be50188a
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="route-traffic-through-a-network-virtual-appliance"></a>通过网络虚拟设备路由流量

该脚本示例创建了包含前端和后端子网的虚拟网络。 它还会创建一个 VM，并启用 IP 转发，在两个子网之间路由流量。 运行脚本后，可将网络软件（例如防火墙应用程序）部署到 VM。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="sample-script"></a>示例脚本


[!code-azurecli-interactive[主要](../../../cli_scripts/virtual-network/route-traffic-through-nva/route-traffic-through-nva.sh "通过网络虚拟设备路由流量")]

## <a name="clean-up-deployment"></a>清理部署 

运行以下命令来删除资源组、VM 和所有相关资源。

```azurecli
az group delete --name MyResourceGroup --yes
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建资源组、虚拟网络和网络安全组。 表中的每条命令链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az group create](/cli/azure/group#create) | 创建用于存储所有资源的资源组。 |
| [az network vnet create](/cli/azure/network/vnet#create) | 创建 Azure 虚拟网络和前端子网。 |
| [az network subnet create](/cli/azure/network/vnet/subnet#create) | 创建后端子网和 DMZ 子网。 |
| [az network public-ip create](/cli/azure/network/public-ip#create) | 创建用于从 Internet 访问 VM 的公共 IP 地址。 |
| [az network nic create](/cli/azure/network/nic#create) | 创建虚拟网络接口，并对它启用 IP 转发。 |
| [az network nsg create](/cli/azure/network/nsg#create) | 创建网络安全组 (NSG)。 |
| [az network nsg rule create](/cli/azure/network/nsg/rule#create) | 创建允许 HTTP 和 HTTPS 端口入站到 VM 的 NSG 规则。 |
| [az network vnet subnet update](/cli/azure/network/vnet/subnet#update)| 将 NSG 和路由表关联到子网。 |
| [az network route-table create](/cli/azure/network/route-table#create)| 为所有路由创建路由表。 |
| [az network route-table route create](/cli/azure/network/route-table/route#create)| 创建路由，通过 VM 在子网和 Internet 之间路由流量。 |
| [az vm create](/cli/azure/vm#create) | 创建虚拟机并向其附加 NIC。 此命令还指定要使用的虚拟机映像和管理凭据。 |
| [az group delete](/cli/azure/group#delete) | 删除资源组及其包含的所有资源。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure/overview)。

可在 [Azure 网络概述文档](../cli-samples.md)中找到其他网络 CLI 脚本示例