---
title: 部署使用 Azure PowerShell 使用可用性区域的 Azure 防火墙
description: 在本文中，您将了解如何部署使用 Azure PowerShell 可用性区域 Azure 防火墙。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 06/20/2019
ms.author: victorh
ms.openlocfilehash: 2fa6a62a28a1536da83994cb07b7c5fa5d7bda9f
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276907"
---
# <a name="deploy-an-azure-firewall-with-availability-zones-using-azure-powershell"></a>部署使用 Azure PowerShell 可用性区域 Azure 防火墙

> [!IMPORTANT]
> 使用可用性区域的 azure 防火墙当前处于公共预览状态。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

可以在跨多个可用性区域，用于提高可用性的部署过程中配置 azure 防火墙。

此功能可实现以下方案：

- 您可以增加可用性为 99.99%运行时间。 有关详细信息，请参阅 Azure 防火墙[服务级别协议 (SLA)](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/)。 99.99%运行时间时选择了两个或多个可用性区域提供 SLA。
- 您还可以关联 Azure 防火墙到邻近原因的特定区域使用的服务标准 99.95 %sla。

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
