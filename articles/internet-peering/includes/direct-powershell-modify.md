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
ms.openlocfilehash: b2609a069872ec55ac9068fadcbb3f312d68a630
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680777"
---
本节介绍如何为 Direct 对等互连执行以下修改操作：

* 添加直接对等互连连接。
* 删除直接对等互连连接。
* 升级或降级活动连接上的带宽。
* 在活动连接上添加 IPv4 或 IPv6 会话。
* 在活动连接上删除 IPv4 或 IPv6 会话。

### <a name="add-direct-peering-connections"></a>添加直接对等互连连接

此示例介绍如何向现有 Direct 对等互连添加连接。

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"

$connection = New-AzPeeringDirectConnection `
    -PeeringDBFacilityId $peeringLocation.PeeringDBFacilityId `
    -SessionPrefixV4 "10.22.31.0/31" `
    -SessionPrefixV6 "fe02::3e:0/127" `
    -MaxPrefixesAdvertisedIPv4 1000 `
    -MaxPrefixesAdvertisedIPv6 100 `
    -BandwidthInMbps 10000

$directPeering.Connections.Add($connection)

$directPeering | Update-AzPeering
```

### <a name="remove-direct-peering-connections"></a>删除直接对等互连连接

PowerShell 当前不支持删除连接。 有关详细信息，请与[微软对等互连](mailto:peeringexperience@microsoft.com)。

<!--
```powershell
$directPeering.Connections.Remove($directPeering.Connections[0])

$directPeering | Update-AzPeering
```
-->

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>在活动连接上升级或降级带宽

此示例介绍如何向现有 Direct 连接添加 10 Gbps。

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BandwidthInMbps  = 20000
$directPeering | Update-AzPeering

```

### <a name="add-ipv4-or-ipv6-sessions-on-active-connections"></a>在活动连接上添加 IPv4 或 IPv6 会话

此示例介绍如何在仅具有 IPv4 会话的现有 Direct 连接上添加 IPv6 会话。 

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BGPSession.SessionPrefixv6 = "fe01::3e:0/127"
$directPeering | Update-AzPeering

```

### <a name="remove-ipv4-or-ipv6-sessions-on-active-connections"></a>在活动连接上删除 IPv4 或 IPv6 会话

PowerShell 目前不支持从现有连接中删除 IPv4 或 IPv6 会话。 有关详细信息，请与[微软对等互连](mailto:peeringexperience@microsoft.com)。