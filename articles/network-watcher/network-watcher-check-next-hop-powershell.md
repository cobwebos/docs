---
title: "使用 Azure 网络观察程序“下一跃点”功能查找下一跃点 - PowerShell | Microsoft 文档"
description: "本文将介绍如何使用 PowerShell 通过“下一跃点”功能查找下一跃点类型和 ip 地址。"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 6a656c55-17bd-40f1-905d-90659087639c
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 95ecd8d4ef3dd7a956206e7c3e07c793db0111f7
ms.openlocfilehash: 9f53c824b6368dc2a6251fd880f1cabefef884b8
ms.lasthandoff: 03/31/2017


---

# <a name="find-out-what-the-next-hop-type-is-using-the-next-hop-capability-in-azure-network-watcher-using-powershell"></a>使用 PowerShell 通过 Azure 网络观察程序中的“下一跃点”功能找到下一跃点类型

> [!div class="op_single_selector"]
> - [Azure 门户](network-watcher-check-next-hop-portal.md)
> - [PowerShell](network-watcher-check-next-hop-powershell.md)
> - [CLI](network-watcher-check-next-hop-cli.md)
> - [Azure REST API](network-watcher-check-next-hop-rest.md)

“下一跃点”是网络观察程序的一项功能，提供基于指定的虚拟机获取下一跃点类型和 IP 地址的功能。 此功能对于确定离开虚拟机的流量是否通过网关、Internet 或虚拟网络到达其目标很有用。

## <a name="before-you-begin"></a>开始之前

在此方案中，将使用 Azure 门户来查找下一跃点类型和 IP 地址。

此方案假定你已按照[创建网络观察程序](network-watcher-create.md)中的步骤创建网络观察程序。 此方案还假定要使用的是存在有效虚拟机的资源组。

## <a name="scenario"></a>方案

本文中介绍的方案使用“下一跃点”（网络观察程序的一项功能）找出资源的下一跃点类型和 IP 地址。 若要了解有关下一跃点的详细信息，请访问[下一跃点概述](network-watcher-next-hop-overview.md)。

## <a name="retrieve-network-watcher"></a>检索网络观察程序

第一步是检索网络观察程序实例。 将 `$networkWatcher` 变量传递给下一跃点验证 cmdlet。

```powershell
$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" } 
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName 
```

## <a name="get-a-virtual-machine"></a>获取虚拟机

“下一跃点”功能将从虚拟机返回下一跃点和下一跃点的 IP 地址。 cmdlet 需要虚拟机的 Id。 如果已知道要使用的虚拟机的 ID，则可以跳过此步骤。

```powershell
$VM = Get-AzurermVM -ResourceGroupName "testrg" -Name "testvm1"
```

> [!NOTE]
> “下一跃点”功能要求 VM 资源已分配，从而可以运行。

## <a name="get-the-network-interfaces"></a>获取网络接口

需要虚拟机上的 NIC 的 IP 地址，在此示例中我们将在虚拟机上检索 NIC。 如果已知道要在虚拟机上测试的 IP 地址，则可以跳过此步骤。

```powershell
$Nics = Get-AzureRmNetworkInterface | Where {$_.Id -eq $vm.NetworkInterfaceIDs.ForEach({$_})}
```

## <a name="get-next-hop"></a>获取下一跃点

现在我们调用 `Get-AzureRmNetworkWatcherNextHop` cmdlet。 我们向该 cmdlet 传递网络观察程序、虚拟机 ID、源 IP 地址和目标 IP 地址。 在此示例中，目标 IP 地址属于另一个虚拟网络中的 VM。 在两个虚拟网络之间有一个虚拟网络网关。

```powershell
Get-AzureRmNetworkWatcherNextHop -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id -SourceIPAddress $nics[0].IpConfigurations[0].PrivateIpAddress  -DestinationIPAddress 10.0.2.4 
```

## <a name="review-results"></a>查看结果

完成后，将提供结果。 将返回下一跃点 IP 地址和它所属的资源类型。 在此方案中，它是虚拟网络网关的公共 IP 地址。

```
NextHopIpAddress NextHopType           RouteTableId 
---------------- -----------           ------------ 
13.78.238.92     VirtualNetworkGateway Gateway Route
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


















