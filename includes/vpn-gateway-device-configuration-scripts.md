---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 01/09/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 78dfd57fba6365f9c8937b30b5cf96b840749c68
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/23/2019
ms.locfileid: "66157539"
---
| **供应商** | **设备系列** | **固件版本** |
| --- | --- | --- |
|Cisco | ISR| IOS 15.1（预览版）|
|Cisco | ASA | ASA ( * ) RouteBased（IKEv2- 无 BGP），对于低于 9.8 版的 ASA |
|Cisco | ASA | ASA RouteBased（IKEv2 - 无 BGP），对于 ASA 9.8+ |
|Juniper | SRX_GA | 12.x|
|Juniper | SSG_GA | ScreenOS 6.2.x|
|Juniper | JSeries_GA | JunOS 12.x|
|Juniper | SRX | JunOS 12.x RouteBased BGP |
|Ubiquiti| EdgeRouter| EdgeOS v1.10x RouteBased VTI|
|Ubiquiti| EdgeRouter| EdgeOS v1.10x RouteBased BGP|

> [!NOTE]
> (*) 必需：NarrowAzureTrafficSelectors（启用 UsePolicyBasedTrafficSelectors 选项）和 CustomAzurePolicies (IKE/IPsec)
>
