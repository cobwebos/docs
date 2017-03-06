---
title: "使用 Azure 网络观察程序“IP 流验证”功能验证流量 - Azure CLI | Microsoft 文档"
description: "本文介绍如何使用 Azure CLI 检查是允许还是拒绝进出虚拟机的流量"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 92b857ed-c834-4c1b-8ee9-538e7ae7391d
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 1d0136b044f6049e59fa09d824cf244cac703c45
ms.openlocfilehash: 3c0287783e3f2b7acaeefd87acbda30885f93c22
ms.lasthandoff: 02/23/2017


---
# <a name="check-if-traffic-is-allowed-or-denied-to-or-from-a-vm-with-ip-flow-verify-a-component-of-azure-network-watcher"></a>使用 Azure 网络观察程序的组件（即“IP 流验证”功能）检查是允许还是拒绝进出 VM 的流量

> [!div class="op_single_selector"]
> - [Azure 门户](network-watcher-check-ip-flow-verify-portal.md)
> - [PowerShell](network-watcher-check-ip-flow-verify-powershell.md)
> - [CLI](network-watcher-check-ip-flow-verify-cli.md)
> - [Azure REST API](network-watcher-check-ip-flow-verify-rest.md)

“IP 流验证”是网络观察程序的一项功能，用于验证是否允许流量进出虚拟机。 此方案对于获取虚拟机是否可以与外部资源或后端通信的当前状态很有用。 可以使用“IP 流验证”功能验证网络安全组 (NSG) 规则是否正确配置，以及对 NSG 规则所阻止的流进行故障排除。 使用“IP 流验证”功能的另一个原因是确保要阻止的流量已由 NSG 正确阻止。

## <a name="before-you-begin"></a>开始之前

此方案假定你已按照[创建网络观察程序](network-watcher-create.md)中的步骤创建网络观察程序，或者已有网络观察程序的现有实例。 此方案还假定要使用的是存在有效虚拟机的资源组。

[!INCLUDE [network-watcher-preview](../../includes/network-watcher-public-preview-notice.md)]

## <a name="scenario"></a>方案

此方案使用“IP 流验证”功能来验证虚拟机是否可以与已知 Bing IP 地址通信。 如果拒绝了流量，它将返回拒绝该流量的安全规则。 若要了解有关 IP 流验证的详细信息，请访问 [IP 流验证概述](network-watcher-ip-flow-verify-overview.md)


## <a name="get-a-vm"></a>获取 VM

“IP 流验证”功能测试从虚拟机上的 IP 地址发往远程目标或从远程目标发往虚拟机上的 IP 地址的流量。 cmdlet 需要虚拟机的 Id。 如果已知道要使用的虚拟机的 ID，则可以跳过此步骤。

```
azure vm show -g resourceGroupName -n virtualMachineName
```

## <a name="get-the-nics"></a>获取 NIC

需要虚拟机上的 NIC 的 IP 地址，在此示例中我们将在虚拟机上检索 NIC。 如果已知道要在虚拟机上测试的 IP 地址，则可以跳过此步骤。

```
azure network nic show -g resourceGroupName -n nicName
```

## <a name="run-ip-flow-verify"></a>运行“IP 流验证”

现在，我们已掌握运行 cmdlet 所需的信息，我们将运行 `network watcher ip-flow-verify` cmdlet 以测试流量。 在此示例中，我们将使用第一个 NIC 上的第一个 IP 地址。

```
azure network watcher ip-flow-verify -g resourceGroupName -n networkWatcherName -t targetResourceId -d directionInboundorOutbound -p protocolTCPorUDP -o localPort -m remotePort -l localIpAddr -r remoteIpAddr
```

> [!NOTE]
> “IP 流验证”功能要求 VM 资源已分配，从而可以运行。

## <a name="review-results"></a>查看结果

运行 `network watcher ip-flow-verify` 之后将返回结果，以下示例是从前一步骤返回的结果。

```
data:    Access                          : Deny
data:    Rule Name                       : defaultSecurityRules/DefaultInboundDenyAll
info:    network watcher ip-flow-verify command OK
```

## <a name="next-steps"></a>后续步骤

如果流量被阻止且不应被阻止，请参阅[管理网络安全组](../virtual-network/virtual-network-manage-nsg-arm-portal.md)找到定义的网络安全组和安全规则。

访问[使用网络观察程序审核网络安全组 (NSG)](network-watcher-nsg-auditing-powershell.md)，了解如何审核 NSG 设置。

[1]: ./media/network-watcher-check-ip-flow-verify-portal/figure1.png
[2]: ./media/network-watcher-check-ip-flow-verify-portal/figure2.png

