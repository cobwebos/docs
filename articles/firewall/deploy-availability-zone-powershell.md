---
title: 使用 PowerShell 通过可用性区域部署 Azure 防火墙
description: 本文介绍如何使用 Azure PowerShell 部署具有可用性区域的 Azure 防火墙。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: 33dcebf14f4d534962783a30ec94f7ff6529ae0d
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2019
ms.locfileid: "74195924"
---
# <a name="deploy-an-azure-firewall-with-availability-zones-using-azure-powershell"></a>使用 Azure PowerShell 部署具有可用性区域的 Azure 防火墙

在部署期间，可将 Azure 防火墙配置为跨多个可用性区域，以提高可用性。

此功能支持以下方案：

- 可以将可用性提高到 99.99% 的正常运行时间。 有关详细信息，请参阅 Azure 防火墙的[服务级别协议 (SLA)](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/)。 如果选择了两个或更多个可用性区域，则可以提供 99.99% 的运行时间 SLA。
- 还可以仅仅出于相互更靠近的原因，将 Azure 防火墙关联到特定的区域，并享用服务标准 99.95% SLA。

有关 Azure 防火墙可用性区域的详细信息，请参阅[什么是 Azure 防火墙？](overview.md)

以下 Azure PowerShell 示例显示如何部署具有可用性区域的 Azure 防火墙。

## <a name="create-a-firewall-with-availability-zones"></a>创建具有可用性区域的防火墙

此示例将在区域 1、区域 2 和区域 3 中创建防火墙。

创建标准公共 IP 地址时，未指定特定区域。 默认情况下，这会创建区域冗余 IP 地址。 标准公共 IP 地址可以在所有区域中配置，也可以在单个区域中配置。

知道这一点很重要，因为不能在区域 1 中有防火墙而在区域 2 中有 IP 地址。 但可以在区域 1 中有防火墙并在所有区域中有 IP 地址，或者在同一个区域中有防火墙和 IP 地址以实现邻近目的。

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

New-AzFirewall `
  -Name "azFw" `
  -ResourceGroupName $rgName `
  -Location centralus `
  -VirtualNetwork $vnet `
  -PublicIpAddress @($pip1)
  -Zone 1,2,3
```

## <a name="next-steps"></a>后续步骤

- [教程：监视 Azure 防火墙日志](./tutorial-diagnostics.md)
