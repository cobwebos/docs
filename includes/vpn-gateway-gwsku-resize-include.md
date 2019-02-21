---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 081352a23e6a0d8f9e2daa77eca1f8ac85172ff6
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/19/2019
ms.locfileid: "56418222"
---
对于当前 SKU（VpnGw1、VpnGw2 和 VPNGW3），如果希望调整网关 SKU 大小以升级到更强大的 SKU，可以使用 `Resize-AzVirtualNetworkGateway` PowerShell cmdlet。 也可以使用此 cmdlet 下调网关 SKU 大小。 如果使用的是基本网关 SKU，[请改用这些说明](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md#resize)来调整网关大小。

以下 PowerShell 示例演示如何将网关 SKU 的大小调整为 VpnGw2。

```powershell
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku VpnGw2
```

还可以通过转到虚拟网关的“配置”页并从下拉列表中选择其他 SKU，在 Azure 门户中调整网关大小。