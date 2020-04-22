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
ms.openlocfilehash: dbaa0b5fc87cb5393b323b8a9b7a38b72efe9518
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680770"
---
PowerShell cmdlet **Get-AzPeeringLocation**返回具有强制参数`Kind`的对等位置列表，您将在后面的步骤中使用该参数。

```powershell
Get-AzPeeringLocation -Kind Direct
```

直接对等位置包含以下字段：
* 对等位置 
* 国家/地区
* 对等数据库设施Id
* 对等DB设施链接
* 带宽提供

通过引用[对等互连](https://wwww.peeringdb.com)数据库，验证您是否在所需的对等互连设施中。

此示例演示如何使用西雅图作为对等位置来创建直接对等互连。

```powershell
$peeringLocations = Get-AzPeeringLocation -Kind Direct
$peeringLocation = $peeringLocations | where {$_.PeeringLocation -contains "Seattle"}
$peeringLocation

PeeringLocation       : Seattle
Address               : 2001 Sixth Avenue
Country               : US
PeeringDBFacilityId   : 71
PeeringDBFacilityLink : https://www.peeringdb.com/fac/71
BandwidthOffers       : {10Gbps, 100Gbps}
```