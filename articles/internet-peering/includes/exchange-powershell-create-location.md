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
ms.openlocfilehash: 2ebf5d574b4d185953ab0f7984648c440d6f107e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678630"
---
PowerShell cmdlet **Get-AzPeeringLocation**返回具有强制参数`Kind`的对等位置列表，您将在后面的步骤中使用该参数。

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

此示例演示如何使用西雅图作为对等位置来创建对等互连。

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