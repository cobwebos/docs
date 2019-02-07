---
title: 如何在 Azure VPN 网关上配置 OpenVPN：PowerShell | Microsoft Docs
description: 为 Azure VPN 网关配置 OpenVPN 的步骤
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 01/16/2019
ms.author: cherylmc
ms.openlocfilehash: 07dae60d1d4ab43194f88f44bde498d9fa19ce81
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2019
ms.locfileid: "55508532"
---
# <a name="configure-openvpn-for-azure-point-to-site-vpn-gateway-preview"></a>为 Azure 点到站点 VPN 网关（预览版）配置 OpenVPN

本文可帮助你在 Azure VPN 网关上设置 OpenVPN。 本文假设你已拥有一个可正常工作的点到站点环境。 如果并非如此，请按照步骤 1 中的说明来创建点到站点 VPN。

> [!IMPORTANT]
> 此公共预览版在提供时没有附带服务级别协议，不应该用于生产工作负荷。 某些功能可能不受支持或受到约束，或者不一定在所有 Azure 位置都可用。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="register"></a>注册此功能

在这些步骤中单击“试用”以使用 Azure Cloud Shell 轻松注册此功能。

>[!NOTE]
>如果未注册此功能，则将无法使用它。
>

单击“试用”打开 Azure Cloud Shell后，复制并粘贴以下命令：

```azurepowershell-interactive
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowVnetGatewayOpenVpnProtocol
```
 
```azurepowershell-interactive
Get-AzureRmProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowVnetGatewayOpenVpnProtocol
```

该功能显示为已注册后，请将订阅重新注册到 Microsoft.Network 命名空间。

```azurepowershell-interactive
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```

## <a name="vnet"></a>1.创建点到站点 VPN

如果还没有正常运行的“点到站点”环境，请按照说明创建一个。 请参阅[创建点到站点 VPN](vpn-gateway-howto-point-to-site-resource-manager-portal.md)，以创建和配置使用本机 Azure 证书身份验证的点到站点 VPN 网关。 

> [!IMPORTANT]
> OpenVPN 不支持基本 SKU。

## <a name="cmdlets"></a>2.安装 PowerShell cmdlet

安装最新版本的资源管理器 PowerShell cmdlet。 有关安装 PowerShell cmdlet 的详细信息，请参阅[如何安装和配置 Azure PowerShell](/powershell/azure/overview)。 这很重要，因为早期版本的 cmdlet 不包含本练习所需的最新值。

## <a name="enable"></a>3.在网关上启用 OpenVPN

在网关上启用 OpenVPN。 在运行以下命令之前，请确保已为点到站点（IKEv2 或 SSTP）配置网关：

```powershell
$gw = Get-AzureRmVirtualNetworkGateway -ResourceGroupName $rgname -name $name
Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
```

## <a name="next-steps"></a>后续步骤

若要为 OpenVPN 配置客户端，请参阅[配置 OpenVPN 客户端](vpn-gateway-howto-openvpn-clients.md)。
