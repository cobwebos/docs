---
title: "使用 PowerShell 创建分区的公共 IP 地址 | Microsoft Docs"
description: "使用 PowerShell 在可用性区域中创建公共 IP。"
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 09/25/2017
ms.author: jdial
ms.custom: 
ms.openlocfilehash: bfeba57036338b4e325d2f122443f2cde0373eed
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-public-ip-address-in-an-availability-zone-with-powershell"></a>使用 PowerShell 在可用性区域中创建公共 IP 地址

可以在 Azure 可用性区域（预览）中部署公共 IP 地址。 可用性区域是 Azure 区域中物理上独立的区域。 了解如何：

> * 在可用性区域中创建公共 IP 地址
> * 标识在可用性区域中创建的相关资源
  

如果还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

本文需要已安装 4.4.0 版或更高版本的 AzureRM 模块。 若要查找版本，请运行 `Get-Module -ListAvailable AzureRM`。 如果需要进行安装或升级，请从 [PowerShell 库](https://www.powershellgallery.com/packages/AzureRM)安装最新版本的 AzureRM 模块。

> [!NOTE]
> 可用性区域处于预览状态，已准备好在开发和测试方案中使用。 可为选择 Azure 资源、区域和 VM 大小系列提供支持。 有关如何开始使用以及可以尝试将可用性区域用于哪些 Azure 资源、区域和 VM 大小系列的详细信息，请参阅[可用性区域概述](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)。 若需支持，可以在 [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) 上寻求帮助或者 [open an Azure support ticket](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json)（创建 Azure 支持票证）。

## <a name="log-in-to-azure"></a>登录 Azure

使用 `Login-AzureRmAccount` 命令登录到 Azure 订阅，并按照屏幕上的说明进行操作。

```powershell
Login-AzureRmAccount
```
## <a name="create-resource-group"></a>创建资源组

使用 [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) 创建 Azure 资源组。 资源组是在其中部署和管理 Azure 资源的逻辑容器。 在此示例中，在“westeurope”区域中创建了名为“myResourceGroup”的资源组。 *westeurope* 是预览版中支持可用性区域的 Azure 区域之一。

```powershell
New-AzureRmResourceGroup -Name AzTest -Location westeurope
```

## <a name="create-a-zonal-public-ip-address"></a>创建分区的公共 IP 地址

使用以下命令在可用性区域中创建公共 IP 地址：

```powershell
    New-AzureRmPublicIpAddress `
        -ResourceGroupName myResourceGroup `
        -Name myPublicIp `
        -Location westeurope `
        -AllocationMethod Static `
        -Zone 2
```

> [!NOTE]
> 将标准 SKU 公共 IP 地址分配到虚拟机的网络接口时，必须使用[网络安全组](security-overview.md#network-security-groups)显式允许预期流量。 创建并关联网络安全组且显式允许所需流量之后，才可与资源通信。

## <a name="get-zone-information-about-a-public-ip-address"></a>获取有关公共 IP 地址的区域信息

使用以下命令获取公共 IP 地址的区域信息：

```powershell
Get-AzureRmPublicIpAddress ` 
    -ResourceGroup myResourceGroup `
    -Name myPublicIp
```

## <a name="next-steps"></a>后续步骤

- 了解有关[可用性区域](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)的详细信息
- 了解有关[公共 IP 地址](virtual-network-public-ip-address.md?toc=%2fazure%2fvirtual-network%2ftoc.json)的详细信息 