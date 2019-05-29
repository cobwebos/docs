---
title: include 文件
description: include 文件
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 04/23/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 40c8cb41ad3bcd46e9973a5f96134ff1bfd02fd2
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/23/2019
ms.locfileid: "66150831"
---
若要快速创建 VNet，可单击本文中的“试一试”，打开 PowerShell 控制台。 请调整值，然后复制命令并将其粘贴到控制台窗口中。 若要详细了解新的 Az 模块和 AzureRM 兼容性，请参阅[新 Azure Powershell Az 模块简介](/powershell/azure/new-azureps-module-az)。 有关 Az 模块安装说明，请参阅[安装 Azure PowerShell](/powershell/azure/install-az-ps)。

请务必确认创建的 VNet 的地址空间不与要连接到的其他 Vnet 的任何地址范围或者本地网络地址空间重叠。

### <a name="create-a-resource-group"></a>创建资源组

如果还没有想要使用的资源组，请创建一个新的资源组。 调整 PowerShell 命令以反映要使用的资源组名称，然后运行以下 cmdlet：

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName WANTestRG -Location WestUS
```

### <a name="create-a-vnet"></a>创建 VNet

调整 PowerShell 命令以创建与环境兼容的 VNet。

```azurepowershell-interactive
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name FrontEnd -AddressPrefix "10.1.0.0/24"
$vnet   = New-AzVirtualNetwork `
            -Name WANVNet1 `
            -ResourceGroupName WANTestRG `
            -Location WestUS `
            -AddressPrefix "10.1.0.0/16" `
            -Subnet $fesub1
```