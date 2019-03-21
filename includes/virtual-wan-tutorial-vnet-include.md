---
title: include 文件
description: include 文件
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 02/01/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 660bbf50e1a8ae73bd7bbe1f7c42691ed62d276a
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2019
ms.locfileid: "57552957"
---
如果没有 VNet，可以使用 PowerShell 快速创建一个。 也可以使用 Azure 门户创建虚拟网络。

* 请务必确认创建的 VNet 的地址空间不与要连接到的其他 Vnet 的任何地址范围或者本地网络地址空间重叠。 
* 如果已有一个 VNet，请确认它符合所需的条件，并且不包含虚拟网络网关。

单击本文中的“试用”打开 PowerShell 控制台，即可轻松创建 VNet。 请调整值，然后复制命令并将其粘贴到控制台窗口中。

### <a name="create-a-resource-group"></a>创建资源组

调整 PowerShell 命令，然后创建一个资源组。

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