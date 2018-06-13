---
title: 旧式 Azure 虚拟网络 VPN 网关 SKU | Microsoft Docs
description: 如何使用旧式虚拟网络网关 SKU；“基本”、“标准”和“高性能”。
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2018
ms.author: cherylmc
ms.openlocfilehash: 4feecb9c1e91e1bc6c66a610c092e7bf894886e5
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
ms.locfileid: "30190222"
---
# <a name="working-with-virtual-network-gateway-skus-legacy-skus"></a>使用虚拟网络网关 SKU（旧式 SKU）

本文包含有关旧式虚拟网络网关 SKU 的信息。 旧式 SKU 仍可用于已创建的 VPN 网关的两种部署模型。 经典 VPN 网关继续使用旧式 SKU，不管是对于现有网关还是对于新网关。 新建资源管理器 VPN 网关时，使用新的网关 SKU。 有关新 SKU 的信息，请参阅[关于 VPN 网关](vpn-gateway-about-vpngateways.md)。

## <a name="gwsku"></a>网关 SKU

[!INCLUDE [Legacy gateway SKUs](../../includes/vpn-gateway-gwsku-legacy-include.md)]

## <a name="agg"></a>按 SKU 列出的估计聚合吞吐量

[!INCLUDE [Aggregated throughput by legacy SKU](../../includes/vpn-gateway-table-gwtype-legacy-aggtput-include.md)]

## <a name="config"></a>按 SKU 和 VPN 类型划分的受支持配置

[!INCLUDE [Table requirements for old SKUs](../../includes/vpn-gateway-table-requirements-legacy-sku-include.md)]

## <a name="resize"></a>重设网关大小

可以在同一 SKU 系列内将网关大小重设到某个网关 SKU。 例如，如果具有标准 SKU，则可重设大小为高性能 SKU。 但不能在旧式 SKU 和新式 SKU 系列之间重设 VPN 网关大小。 例如，不能从标准 SKU 调整为 VpnGw2 SKU 或从标准 SKU 调整为 VpnGw1。

若要重设经典部署模型的网关大小，请使用以下命令：

```powershell
Resize-AzureVirtualNetworkGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

若要使用 PowerShell 重设资源管理器部署模型的网关大小，请使用以下命令：

```powershell
$gw = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```
还可以在 Azure 门户中重设网关大小。

## <a name="change"></a>更改到新式网关 SKU

[!INCLUDE [Change to the new SKUs](../../includes/vpn-gateway-gwsku-change-legacy-sku-include.md)]

## <a name="next-steps"></a>后续步骤

有关新式网关 SKU 的详细信息，请参阅[网关 SKU](vpn-gateway-about-vpngateways.md#gwsku)。

有关配置设置的详细信息，请参阅[关于 VPN 网关配置设置](vpn-gateway-about-vpn-gateway-settings.md)。