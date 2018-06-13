---
title: 使用 Azure 网络观察程序“下一跃点”功能查找下一跃点 - Azure CLI 2.0 | Microsoft Docs
description: 本文介绍如何使用 Azure CLI 通过“下一跃点”功能查找下一跃点类型和 ip 地址。
services: network-watcher
documentationcenter: na
author: georgewallace
manager: jimdial
editor: ''
ms.assetid: 0700c274-3e0d-4dca-acfa-3ceac8990613
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 873bd0f8c98cfa67c77841df8aa53eb2b895cd54
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/24/2018
ms.locfileid: "31809755"
---
# <a name="find-out-what-the-next-hop-type-is-using-the-next-hop-capability-in-azure-network-watcher-using-azure-cli-20"></a>使用 Azure CLI 2.0 通过 Azure 网络观察程序中的“下一跃点”功能找到下一跃点类型

> [!div class="op_single_selector"]
> - [Azure 门户](network-watcher-check-next-hop-portal.md)
> - [PowerShell](network-watcher-check-next-hop-powershell.md)
> - [CLI 1.0](network-watcher-check-next-hop-cli-nodejs.md)
> - [CLI 2.0](network-watcher-check-next-hop-cli.md)
> - [Azure REST API](network-watcher-check-next-hop-rest.md)

“下一跃点”是网络观察程序的一项功能，提供基于指定的虚拟机获取下一跃点类型和 IP 地址的功能。 此功能对于确定离开虚拟机的流量是否通过网关、Internet 或虚拟网络到达其目标很有用。

本文使用资源管理部署模型的新一代 CLI (Azure CLI 2.0)，其适用于 Windows、Mac 和 Linux。

若要执行本文中的步骤，需要[安装适用于 Mac、Linux 和 Windows 的 Azure 命令行接口 (Azure CLI)](https://docs.microsoft.com/cli/azure/install-az-cli2)。

## <a name="before-you-begin"></a>开始之前

在此方案中，将使用 Azure CLI 来查找下一跃点类型和 IP 地址。

此方案假定已按照[创建网络观察程序](network-watcher-create.md)中的步骤创建网络观察程序。 此外，本方案假设要使用一个包含有效虚拟机的资源组。

## <a name="scenario"></a>场景

本文中介绍的方案使用“下一跃点”（网络观察程序的一项功能）找出资源的下一跃点类型和 IP 地址。 若要了解有关下一跃点的详细信息，请访问[下一跃点概述](network-watcher-next-hop-overview.md)。


## <a name="get-next-hop"></a>获取下一跃点

为了获取下一跃点，我们将调用 `az network watcher show-next-hop` cmdlet。 我们向该 cmdlet 传递网络观察程序资源组 NetworkWatcher、虚拟机 ID、源 IP 地址和目标 IP 地址。 在此示例中，目标 IP 地址属于另一个虚拟网络中的 VM。 在两个虚拟网络之间有一个虚拟网络网关。

如果尚未这样做，请安装并配置最新的 [Azure CLI 2.0](/cli/azure/install-az-cli2)，并使用 [az login](/cli/azure/reference-index#az_login) 登录 Azure 帐户。 然后，运行以下命令：

```azurecli
az network watcher show-next-hop --resource-group <resourcegroupName> --vm <vmNameorID> --source-ip <source-ip> --dest-ip <destination-ip>

```

> [!NOTE]
如果 VM 具有多个 NIC 并且在任何 NIC 上启用了 IP 转发，则必须指定 NIC 参数 (-i nic-id)。 否则其则是可选项。

## <a name="review-results"></a>查看结果

完成后，将提供结果。 将返回下一跃点 IP 地址和它所属的资源类型。

```azurecli
{
    "nextHopIpAddress": null,
    "nextHopType": "Internet",
    "routeTableId": "System Route"
}
```

以下列表显示当前可用的 NextHopType 值：

**下一跃点类型**

* Internet
* VirtualAppliance
* VirtualNetworkGateway
* VnetLocal
* HyperNetGateway
* VnetPeering
* 无

## <a name="next-steps"></a>后续步骤

通过访问[使用网络观察程序进行 NSG 审核](network-watcher-nsg-auditing-powershell.md)，了解如何以编程方式查看网络安全组设置
