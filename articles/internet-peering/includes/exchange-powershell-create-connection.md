---
title: include 文件
titleSuffix: Azure
description: include 文件
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 0014b67443797f45de51ec1bc459f71bde55cdc9
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774427"
---
下面的示例演示如何在西雅图的 Equinix Internet Exchange 上创建 Exchange 连接。 如果使用的是其他提供商和其他设置，请在发出请求时替换该信息。

使用 PowerShell cmdlet **AzPeeringExchangeConnectionObject**创建将用于生成新的对等互连请求的 Powershell 连接对象：

下面是创建 Exchange 连接的示例：

```powershell
$connection1 = New-AzPeeringExchangeConnectionObject `
    -PeeringDBFacilityId $exchangeLocation[1].PeeringDBFacilityId `
    -PeerSessionIPv4Address 198.32.134.22 `
    -PeerSessionIPv6Address  2001:504:12::22 `
    -MaxPrefixesAdvertisedIPv4 2000 `
    -MaxPrefixesAdvertisedIPv6 2000 `
```

如果需要在给定对等位置进行冗余，请创建另一个连接：

```powershell
$connection2 = New-AzPeeringExchangeConnectionObject `
    -PeeringDBFacilityId $exchangeLocation[1].PeeringDBFacilityId `
    -PeerSessionIPv4Address 198.32.134.23 `
    -PeerSessionIPv6Address  2001:504:12::23 `
    -MaxPrefixesAdvertisedIPv4 2000 `
    -MaxPrefixesAdvertisedIPv6 2000 `
```

PowerShell cmdlet **AzPeering**可用于创建新的 Exchange 对等互连：

```powershell
$asn = Get-AzPeerAsn
New-AzPeering `
    -Name "SeattleExchangePeering" `
    -ResourceGroupName "PeeringResourceGroup" `
    -PeerAsnResourceId $asn.Id `
    -PeeringLocation  $exchangeLocation[1].PeeringLocation `
    -ExchangeConnection $connection1[, $connection2]
```
&nbsp;

下面是使用一个连接执行请求时的示例响应：

```powershell

Name              : SeattleExchangePeering
Sku.Name          : Basic_Exchange_Free
Kind              : Exchange
Connections       : {11}
PeerAsn.Id        : /subscriptions/{subscriptionId}/providers/Microsoft.Peering/peerAsns/{peerAsnName}
PeeringLocation   : Seattle
ProvisioningState : Succeeded
Location          : West US 2
Id                : /subscriptions/{subscriptionId}/resourceGroups/PeeringResourceGroup/providers/Microsoft.Peering/peerings/SeattleExchangePeering
Type              : Microsoft.Peering/peerings
Tags              : {}

```

> [!IMPORTANT]
> Microsoft 将开始预配请求的对等互连，`ConnectionState` 将反映进度。
> 有关设置相关步骤，请参阅[Exchange 对等互连演练](../walkthrough-exchange-all.md)文档。

可以按如下所示检查 ConnectionState：

```powershell

$peering = Get-AzPeering -Name "SeattleExchangePeering" -ResourceGroupName "PeeringResourceGroup"
$peering.Connections

PeeringDBFacilityId         : 11
PeerSessionIPv4Address      : 198.32.134.22
PeerSessionIPv6Address      : 2001:504:12::22
SessionStateV4              : PendingAdd
SessionStateV6              : PendingAdd
MaxPrefixesAdvertisedV4     : 2000
MaxPrefixesAdvertisedV6     : 2000
MicrosoftSessionIPv4Address : 198.32.134.152
MicrosoftSessionIPv4Address : 2001:504:12::15
Md5AuthenticationKey        :

```