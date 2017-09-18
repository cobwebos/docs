---
title: "重置 Azure VPN 网关以重建 IPsec 隧道 | Microsoft Docs"
description: "本文逐步讲解如何通过重置 Azure VPN 网关来重建 IPsec 隧道。 本文适用于经典和 Resource Manager 部署模型中的 VPN 网关。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 79d77cb8-d175-4273-93ac-712d7d45b1fe
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/24/2017
ms.author: cherylmc
ms.translationtype: HT
ms.sourcegitcommit: 190ca4b228434a7d1b30348011c39a979c22edbd
ms.openlocfilehash: 604af54d034b6b51a96ff098827e2352a163be18
ms.contentlocale: zh-cn
ms.lasthandoff: 09/09/2017

---
# <a name="reset-a-vpn-gateway"></a>重置 VPN 网关

如果丢失一个或多个站点到站点隧道上的跨界 VPN 连接，重置 VPN 网关可有效解决该情况。 在此情况下，本地 VPN 设备都在正常工作，但却无法与 Azure VPN 网关建立 IPsec 隧道。 本文帮助用户重置 VPN 网关。

### <a name="what-happens-during-a-reset"></a>重置期间会发生什么情况？

VPN 网关由在活动备用配置中运行的两个 VM 实例组成。 重置网关时会重启网关，并对其重新应用跨界配置。 该网关将保持已有的公共 IP 地址。 这意味着不需要使用 Azure VPN 网关的新公共 IP 地址更新 VPN 路由器配置。

发出重置网关命令后，会立即重新启动 Azure VPN 网关的当前活动实例。 从活动实例（正在重新启动）故障转移到备用实例期间会有一个短暂的时间间隔。 该时间间隔应不超过 1 分钟。

如果在首次重新启动后未恢复连接，再次发出同一命令以重新启动第二个 VM 实例（新活动网关）。 如果连续请求两次重新启动，则重新启动这两个 VM 实例（活动和备用）的时间可能会略长一些。 这会导致 VPN 连接出现较长的时间间隔，VM 需要最多 2 到 4 分钟才能完成重新启动。

在两次重新启动之后，如果仍然存在跨界连接问题，请从 Azure 门户提出支持请求。

## <a name="before"></a>准备工作

在重置网关之前，请为每个 IPsec 站点到站点 (S2S) VPN 隧道验证下面列出的重要项目。 如果项目中存在任何不匹配，将导致 S2S VPN 隧道断开连接。 验证并更正本地网关和 Azure VPN 网关的配置能够避免网关上其他正在工作的连接出现不必要的重新启动和中断。

在重置网关之前，请检查以下各项：

* 在 Azure 和本地 VPN 策略中，为 Azure VPN 网关和本地 VPN 网关配置的 Internet IP 地址 (VIP) 正确。
* 在 Azure 和本地 VPN 网关上，预共享的密钥必须相同。
* 如果应用特定的 IPsec/IKE 配置，如加密、哈希算法和 PFS（完全向前保密），请确保 Azure 和本地 VPN 网关具有相同配置。

## <a name="portal"></a>Azure 门户

可使用 Azure 门户重置 Resource Manager VPN 网关。 若要重置经典网关，请参阅 [PowerShell](#resetclassic) 步骤。

### <a name="resource-manager-deployment-model"></a>Resource Manager 部署模型

1. 打开 [Azure 门户](https://portal.azure.com)并导航到要重置的 Resource Manager 虚拟网络网关。
2. 在虚拟网络网关的边栏选项卡上，单击“重置”。

  ![“重置 VPN 网关”边栏选项卡](./media/vpn-gateway-howto-reset-gateway/reset-vpn-gateway-portal.png)
3. 在“重置”边栏选项卡上，单击“重置”按钮。

## <a name="ps"></a>PowerShell

### <a name="resource-manager-deployment-model"></a>Resource Manager 部署模型

用于重置网关的 cmdlet 是 Reset-AzureRmVirtualNetworkGateway。 进行重置前，请确保拥有最新版本的 [Resource Manager PowerShell cmdlet](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.0.0)。 以下示例将重置 TestRG1 资源组中名为 VNet1GW 的虚拟网络网关：

```powershell
$gw = Get-AzureRmVirtualNetworkGateway -Name VNet1GW -ResourceGroup TestRG1
Reset-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw
```

结果：

收到返回结果时，可假定网关重置成功。 但返回结果没有明确指出重置成功。 如要仔细查看历史记录，确定网关重置发生的确切时间，可在 [Azure 门户](https://portal.azure.com)中查看该信息。 在门户中，导航到“GatewayName”->“资源运行状况”。

### <a name="resetclassic"></a>经典部署模型

用于重置网关的 cmdlet 是 Reset-AzureVNetGateway。 进行重置前，请确保拥有最新版本的 [Service Management (SM) PowerShell cmdlet](https://docs.microsoft.com/powershell/azure/install-azure-ps?view=azuresmps-3.7.0)。 下面的示例为名为“ContosoVNet”的虚拟网络重置了网关：

```powershell
Reset-AzureVNetGateway –VnetName “ContosoVNet”
```

结果：

```powershell
Error          :
HttpStatusCode : OK
Id             : f1600632-c819-4b2f-ac0e-f4126bec1ff8
Status         : Successful
RequestId      : 9ca273de2c4d01e986480ce1ffa4d6d9
StatusCode     : OK
```

## <a name="cli"></a>Azure CLI

若要重置网关，请使用 [az network vnet-gateway reset](https://docs.microsoft.com/cli/azure/network/vnet-gateway#az_network_vnet_gateway_reset) 命令。 以下示例将重置 TestRG5 资源组中名为 VNet5GW 的虚拟网络网关：

```azurecli
az network vnet-gateway reset -n VNet5GW -g TestRG5
```

结果：

收到返回结果时，可假定网关重置成功。 但返回结果没有明确指出重置成功。 如要仔细查看历史记录，确定网关重置发生的确切时间，可在 [Azure 门户](https://portal.azure.com)中查看该信息。 在门户中，导航到“GatewayName”->“资源运行状况”。
