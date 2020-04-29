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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81680770"
---
PowerShell cmdlet **AzPeeringLocation**返回具有必需参数的对等互连位置的列表，你`Kind`将在后续步骤中使用该参数。

```powershell
Get-AzPeeringLocation -Kind Direct
```

直接对等互连位置包含以下字段：
* PeeringLocation 
* 国家/地区
* PeeringDBFacilityId
* PeeringDBFacilityLink
* BandwidthOffers

请参阅[PeeringDB](https://wwww.peeringdb.com)，验证你是否在所需的对等互连设施上提供。

此示例演示如何使用西雅图作为对等互连位置创建直接对等互连。

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