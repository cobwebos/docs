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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774284"
---
PowerShell cmdlet **Get-AzPeeringLocation**返回具有强制参数`Kind`的对等位置列表，您将在后面的步骤中使用该参数：

```powershell
Get-AzPeeringLocation -Kind Direct
```

直接对等位置包含以下字段：
* 对等位置 
* 国家/地区
* 对等数据库设施Id
* 对等DB设施链接
* 带宽提供

通过引用[对等互连数据库](https://wwww.peeringdb.com)，验证您是否在所需的对等设施中。

下面是一个示例，演示如何使用西雅图作为对等位置来创建直接对等互连：

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