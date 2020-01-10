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
ms.openlocfilehash: 86d768db7a31c634bdaca6c93f633c7bbaf10a65
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774284"
---
PowerShell cmdlet **AzPeeringLocation**返回具有必需参数 `Kind`的对等互连位置的列表，你将在后面的步骤中使用该参数：

```powershell
Get-AzPeeringLocation -Kind Direct
```

直接对等互连位置包含以下字段：
* PeeringLocation 
* 国家/地区
* PeeringDBFacilityId
* PeeringDBFacilityLink
* BandwidthOffers

请参阅[PeeringDB](https://w www.peeringdb.com)，验证你是否在所需的对等互连设施上提供。

下面的示例演示如何使用西雅图作为对等互连位置创建直接对等互连：

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