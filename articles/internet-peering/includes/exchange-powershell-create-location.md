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
ms.openlocfilehash: 6f5d2dc30ac0f6316587fa0836b87cbd4efc0a8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774388"
---
PowerShell cmdlet **Get-AzPeeringLocation**返回具有强制参数`Kind`的对等位置列表，您将在后面的步骤中使用该参数：

```powershell
Get-AzPeeringLocation -Kind "Exchange"
```

交换对等位置包含以下字段：
* 交换名称
* 对等位置
* 国家/地区
* 对等数据库设施Id
* 对等DB设施链接
* 微软IPv4地址
* 微软IPv6地址

通过引用[对等互连数据库](https://wwww.peeringdb.com)，验证您是否在所需的对等设施中。

下面是一个示例，演示如何使用西雅图作为对等位置来创建对等互连：

```powershell
$exchangeLocations = Get-AzPeeringLocation -Kind Exchange
$exchangeLocation = $exchangeLocations | where {$_.PeeringLocation -eq "Seattle"}

#check the location metadata
$exchangeLocation

ExchangeName          : Columbia IX
PeeringLocation       : Seattle
Country               : US
PeeringDBFacilityId   : 99999
PeeringDBFacilityLink : https://www.peeringdb.com/ix/99999
MicrosoftIPv4Address  : 10.12.97.129
MicrosoftIPv6Address  :

ExchangeName          : Equinix Seattle
PeeringLocation       : Seattle
Country               : US
PeeringDBFacilityId   : 11
PeeringDBFacilityLink : https://www.peeringdb.com/ix/11
MicrosoftIPv4Address  : 198.32.134.152
MicrosoftIPv6Address  : 2001:504:12::15

...

```