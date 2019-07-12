---
title: 部署使用 Azure PowerShell 使用可用性区域的 Azure 防火墙
description: 在本文中，您将了解如何部署使用 Azure PowerShell 可用性区域 Azure 防火墙。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 7/10/2019
ms.author: victorh
ms.openlocfilehash: 56958eedceeb4602589d65d5e0eb7b10e8a9ff2d
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704000"
---
# <a name="deploy-an-azure-firewall-with-availability-zones-using-azure-powershell"></a>部署使用 Azure PowerShell 可用性区域 Azure 防火墙

在部署期间，可将 Azure 防火墙配置为跨多个可用性区域，以提高可用性。

此功能可实现以下方案：

- 您可以增加可用性为 99.99%运行时间。 有关详细信息，请参阅 Azure 防火墙的[服务级别协议 (SLA)](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/)。 如果选择了两个或更多个可用性区域，则可以提供 99.99% 的运行时间 SLA。
- 还可以仅仅出于相互更靠近的原因，将 Azure 防火墙关联到特定的区域，并享用服务标准 99.95% SLA。

有关 Azure 防火墙可用性区域的详细信息，请参阅[什么是 Azure 防火墙？](overview.md)

以下 Azure PowerShell 示例演示如何部署包含可用性区域 Azure 防火墙。

## <a name="create-a-firewall-with-availability-zones"></a>使用可用性区域中创建防火墙

此示例在区域 1、 2 和 3 中创建一个防火墙。

创建标准公共 IP 地址时，不指定任何特定的区域。 这将默认情况下创建区域冗余的 IP 地址。 在所有区域或单个区域中，可以配置标准公共 IP 地址。

务必知道，因为不能在区域 2 中包含区域 1 中的防火墙和 IP 地址。 但出于邻近目的相同的单个区域中，可以有在区域 1 中的防火墙和 IP 地址在所有区域中的或防火墙和 IP 地址。

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
