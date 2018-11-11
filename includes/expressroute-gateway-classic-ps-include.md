---
title: include 文件
description: include 文件
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 11/05/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: fab00e5281bb91bce10228b3bc2e9cfd503d5d5b
ms.sourcegitcommit: 1b186301dacfe6ad4aa028cfcd2975f35566d756
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2018
ms.locfileid: "51219811"
---
必须先创建 VNet 和网关子网，再处理以下任务。

> [!NOTE]
> 这些示例不适用于 S2S/ExpressRoute 共存配置。
> 有关使用共存配置中的网关的详细信息，请参阅[配置共存连接](../articles/expressroute/expressroute-howto-coexist-classic.md#gw)

## <a name="add-a-gateway"></a>添加网关

使用以下命令可创建网关。 请务必将所有值替换成自己的值。

```powershell
New-AzureVNetGateway -VNetName "MyAzureVNET" -GatewayType DynamicRouting -GatewaySKU  Standard
```

## <a name="verify-the-gateway-was-created"></a>验证是否已创建网关

使用以下命令来验证是否已创建网关。 此命令还将检索执行其他操作所需的网关 ID。

```powershell
Get-AzureVNetGateway
```

## <a name="resize-a-gateway"></a>重设网关大小

有许多[网关 SKU](../articles/expressroute/expressroute-about-virtual-network-gateways.md)。 可以使用以下命令随时更改网关 SKU。

> [!IMPORTANT]
> 此命令对 UltraPerformance 网关不起作用。 要将网关更改为 UltraPerformance 网关，首先要删除现有的 ExpressRoute 网关，然后创建新的 UltraPerformance 网关。 要将网关从 UltraPerformance 网关降级，首先要删除 UltraPerformance 网关，然后创建新网关。 
>
>

```powershell
Resize-AzureVNetGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

## <a name="remove-a-gateway"></a>删除网关

使用以下命令可删除网关

```powershell
Remove-AzureVnetGateway -GatewayId <Gateway ID>
```
