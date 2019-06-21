---
title: 具有多个公共 IP 地址，使用 Azure PowerShell 部署 Azure 防火墙
description: 在本文中，您将了解如何使用多个公共 IP 地址，使用 Azure PowerShell 部署 Azure 防火墙。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 06/15/2019
ms.author: victorh
ms.openlocfilehash: a7dd35212a573fc3e94dadea4365f150122e1b5a
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276738"
---
# <a name="deploy-an-azure-firewall-with-multiple-public-ip-addresses-using-azure-powershell"></a>具有多个公共 IP 地址，使用 Azure PowerShell 部署 Azure 防火墙

> [!IMPORTANT]
> 具有多个公共 IP 地址的 azure 防火墙当前处于公共预览状态。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

你可以部署 Azure 防火墙使用达 600 个公共 IP 地址。

此功能可实现以下方案：

- **DNAT** -您可以将多个标准端口实例转换到后端服务器。 例如，如果有两个公共 IP 地址，可以将转换为这两个 IP 地址的 TCP 端口 3389 (RDP)。
- **SNAT** -其他端口是可用于出站 SNAT 连接，减少潜在的 SNAT 端口耗尽的情况。 在此期间，Azure 防火墙随机选择的源公共 IP 地址使用的连接。 如果必须在网络上任何下游筛选，您需要允许防火墙与关联的所有公共 IP 地址。

以下 Azure PowerShell 示例显示如何添加、 删除和配置 Azure 防火墙的公共 IP 地址。

> [!NOTE]
> 如果添加或删除公共 IP 地址到正在运行的防火墙时，现有的入站的连接使用 DNAT 规则可能无法正常 40 120 秒。 这是此功能的公共预览版的限制。

## <a name="add-a-public-ip-address-to-an-existing-firewall"></a>将公共 IP 地址添加到现有的防火墙

在此示例中，公共 IP 地址*azFwPublicIp1*为附加到防火墙。

```azurepowershell
$pip = New-AzPublicIpAddress `
  -Name "azFwPublicIp1" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

$azFw = Get-AzFirewall `
  -Name "AzureFirewall" `
  -ResourceGroupName "rg"

$azFw.AddPublicIpAddress($pip)

$azFw | Set-AzFirewall
```

## <a name="remove-a-public-ip-address-from-an-existing-firewall"></a>从现有的防火墙中删除公共 IP 地址

在此示例中，公共 IP 地址*azFwPublicIp1*时从防火墙处于分离状态。

```azurepowershell
$pip = Get-AzPublicIpAddress `
  -Name "azFwPublicIp1" `
  -ResourceGroupName "rg"

$azFw = Get-AzFirewall `
  -Name "AzureFirewall" `
  -ResourceGroupName "rg"

$azFw.RemovePublicIpAddress($pip)

$azFw | Set-AzFirewall
```

## <a name="create-a-firewall-with-two-or-more-public-ip-addresses"></a>有两个或多个公共 IP 地址创建防火墙

此示例创建一个防火墙，附加到虚拟网络*vnet*有两个公共 IP 地址。

```azurepowershell
$rgName = "resourceGroupName"

$vnet = Get-AzVirtualNetwork `
  -Name "vnet" `
  -ResourceGroupName $rgName

$pip1 = New-AzPublicIpAddress `
  -Name "AzFwPublicIp1" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

$pip2 = New-AzPublicIpAddress `
  -Name "AzFwPublicIp2" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

New-AzFirewall `
  -Name "azFw" `
  -ResourceGroupName $rgName `
  -Location centralus `
  -VirtualNetwork $vnet `
  -PublicIpAddress @($pip1, $pip2)
```

## <a name="next-steps"></a>后续步骤

* [教程：监视 Azure 防火墙日志](./tutorial-diagnostics.md)
