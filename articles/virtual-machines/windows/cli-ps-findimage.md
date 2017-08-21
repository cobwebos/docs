---
title: "在 Azure 中选择 Windows VM 映像 | Microsoft Docs"
description: "了解如何使用 Azure PowerSHell 来确定发布服务器、产品/服务、SKU 和 Marketplace VM 映像的版本。"
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 188b8974-fabd-4cd3-b7dc-559cbb86b98a
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 07/12/2017
ms.author: danlep
ms.translationtype: HT
ms.sourcegitcommit: 818f7756189ed4ceefdac9114a0b89ef9ee8fb7a
ms.openlocfilehash: 630f555b003b0efc45b372a7009dbf036aa8c737
ms.contentlocale: zh-cn
ms.lasthandoff: 07/14/2017

---
# <a name="how-to-find-windows-vm-images-in-the-azure-marketplace-with-azure-powershell"></a>如何使用 Azure PowerShell 在 Azure Marketplace 中查找 Windows VM 映像

本主题介绍如何使用 Azure PowerShell 在 Azure Marketplace 中查找 VM 映像。 创建 Windows VM 时使用此信息来指定 Marketplace 映像。

确保已安装并配置最新的 [Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。



## <a name="table-of-commonly-used-windows-images"></a>常用 Windows 映像表
| PublisherName | 产品 | SKU |
|:--- |:--- |:--- |:--- |
| MicrosoftWindowsServer |WindowsServer |2016-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2016-Datacenter-Server-Core |
| MicrosoftWindowsServer |WindowsServer |2016-Datacenter-with-Containers |
| MicrosoftWindowsServer |WindowsServer |2016-Nano-Server |
| MicrosoftWindowsServer |WindowsServer |2012-R2-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2008-R2-SP1 |
| MicrosoftDynamicsNAV |DynamicsNAV |2017 |
| MicrosoftSharePoint |MicrosoftSharePointServer |2016 |
| MicrosoftSQLServer |SQL2016-WS2016 |Enterprise |
| MicrosoftSQLServer |SQL2014SP2-WS2012R2 |Enterprise |
| MicrosoftWindowsServerHPCPack |WindowsServerHPCPack |2012R2 |
| MicrosoftWindowsServerEssentials |WindowsServerEssentials |WindowsServerEssentials |

## <a name="find-specific-images"></a>查找特定映像


使用 Azure Resource Manager 创建新的虚拟机时，在某些情况下，你需要使用以下映像属性组合来指定映像：

* 发布者
* 产品
* SKU

例如，将这些值用于 [Set-AzureRMVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage) PowerShell cmdlet 或资源组模板，必须在此资源组模板中指定要创建的 VM 类型。

如果需要确定这些值，可以运行 [Get-AzureRMVMImagePublisher](/powershell/module/azurerm.compute/get-azurermvmimagepublisher)、[Get-AzureRMVMImageOffer](/powershell/module/azurerm.compute/get-azurermvmimageoffer) 和 [Get-AzureRMVMImageSku](/powershell/module/azurerm.compute/get-azurermvmimagesku) cmdlet 来导航映像。 确定这些值：

1. 列出映像发布者。
2. 对于给定的发布者，列出其产品。
3. 对于给定的产品，列出其 SKU。

首先，使用以下命令列出发布者：

```powershell
$locName="<Azure location, such as West US>"
Get-AzureRMVMImagePublisher -Location $locName | Select PublisherName
```

填写选择的发布者名称，然后运行以下命令：

```powershell
$pubName="<publisher>"
Get-AzureRMVMImageOffer -Location $locName -Publisher $pubName | Select Offer
```

填写选择的产品名称，然后运行以下命令：

```powershell
$offerName="<offer>"
Get-AzureRMVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus
```

从 `Get-AzureRMVMImageSku` 命令的输出来看，已获得了为新虚拟机指定映像所需的所有信息。

下面是一个完整示例：

```powershell
$locName="West US"
Get-AzureRMVMImagePublisher -Location $locName | Select PublisherName

```

输出：

```
PublisherName
-------------
a10networks
aiscaler-cache-control-ddos-and-url-rewriting-
alertlogic
AlertLogic.Extension
Barracuda.Azure.ConnectivityAgent
barracudanetworks
basho
boxless
bssw
Canonical
...
```

对于“MicrosoftWindowsServer”发布者：

```powershell
$pubName="MicrosoftWindowsServer"
Get-AzureRMVMImageOffer -Location $locName -Publisher $pubName | Select Offer
```

输出：

```
Offer
-----
Windows-HUB
WindowsServer
WindowsServer-HUB
```

对于“WindowsServer”产品：

```powershell
$offerName="WindowsServer"
Get-AzureRMVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus
```

输出：

```
Skus
----
2008-R2-SP1
2008-R2-SP1-smalldisk
2012-Datacenter
2012-Datacenter-smalldisk
2012-R2-Datacenter
2012-R2-Datacenter-smalldisk
2016-Datacenter
2016-Datacenter-Server-Core
2016-Datacenter-Server-Core-smalldisk
2016-Datacenter-smalldisk
2016-Datacenter-with-Containers
2016-Nano-Server
```

从上面的列表中复制选择的 SKU 名称，你已获得 `Set-AzureRMVMSourceImage` PowerShell cmdlet 或资源组模板的所有信息。

## <a name="next-steps"></a>后续步骤
现在，你可以确切地选择想要使用的映像。 若要使用刚找到的映像信息快速创建虚拟机，请参阅[使用 PowerShell 创建 Windows 虚拟机](quick-create-powershell.md)。

