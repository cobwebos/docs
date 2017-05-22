---
title: "Windows 虚拟机概述 | Microsoft 文档"
description: "了解如何在 Azure 中创建和管理 Windows 虚拟机。"
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: fbae9c8e-2341-4ed0-bb20-fd4debb2f9ca
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/01/2017
ms.author: davidmu
ms.translationtype: Human Translation
ms.sourcegitcommit: c308183ffe6a01f4d4bf6f5817945629cbcedc92
ms.openlocfilehash: 6d87b94df7e03bfe3255a16d30cd82ff6ce08428
ms.contentlocale: zh-cn
ms.lasthandoff: 05/17/2017


---
# <a name="overview-of-windows-virtual-machines-in-azure"></a>Azure 中的 Windows 虚拟机概述
Azure 虚拟机 (VM) 是 Azure 提供的多种[可缩放按需分配计算资源](../../app-service-web/choose-web-site-cloud-service-vm.md)之一。 通常情况下，如果需要对计算环境进行控制，而使用 VM 相对于其他选择来说可以更好地进行控制，则应选择 VM。 本文介绍创建 VM 之前的注意事项，以及 VM 的创建方法和管理方式。

使用 Azure VM 可以灵活进行虚拟化，而无需购买和维护运行 VM 的物理硬件。 不过，仍然需要通过执行任务来维护 VM，例如，配置、修补和安装在 VM 上运行的软件。

可通过多种方式使用 Azure 虚拟机。 下面是一些示例：

* **开发和测试** – 在 Azure VM 上，可以快速轻松地创建具有特定配置的计算机来满足编程和应用程序测试的需要。
* **云中的应用程序** – 由于应用程序的需求会不断变化，在 Azure 中的 VM 上运行应用程序可能会节省成本。 使用 VM 时，需要支付额外的费用；关闭 VM 时，则无需付费。
* **扩展的数据中心** – Azure 虚拟网络中的虚拟机可以轻松连接到组织的网络。

可以根据需要，将应用程序使用的 VM 纵向和横向扩展为任意数目。

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>在创建 VM 之前需要考虑哪些因素？
在 Azure 中构建应用程序基础结构时，始终要考虑多种[设计注意事项](infrastructure-virtual-machine-guidelines.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 在开始之前，必须考虑到 VM 的以下重要方面：

* 应用程序资源的名称
* 资源的存储位置
* VM 的大小
* 可以创建的 VM 数目上限
* VM 运行的操作系统
* VM 在启动后的配置
* VM 所需的相关资源

### <a name="naming"></a>命名
虚拟机有一个分配的[名称](infrastructure-naming-guidelines.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)，另外，它还配置有一个在操作系统中使用的计算机名称。 VM 的名称最多可包含 15 个字符。

如果使用 Azure 创建操作系统磁盘，计算机名称与虚拟机名称相同。 如果[上载包含以前配置的操作系统的自有映像](upload-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)并使用它来创建虚拟机，则名称可以不同。 建议在上载自己的映像文件时，使操作系统中的计算机名称与虚拟机名称保持相同。

### <a name="locations"></a>位置
在 Azure 中创建的所有资源分布在世界各地的多个[地理区域](https://azure.microsoft.com/regions/)。 创建 VM 时，区域通常称为**位置**。 位置指定 VM 虚拟硬盘的存储位置。

下表显示了获取可用位置列表的一些方法。

| 方法 | 说明 |
| --- | --- |
| Azure 门户 |创建 VM 时，可从列表中选择位置。 |
| Azure PowerShell |使用 [Get-AzureRmLocation](/powershell/module/azurerm.resources/get-azurermlocation) 命令。 |
| REST API |使用 [列出位置](https://docs.microsoft.com/rest/api/resources/subscriptions#Subscriptions_ListLocations) 操作。 |

### <a name="vm-size"></a>VM 大小
VM 的[大小](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)由所要运行的工作负荷决定。 然后，选择的大小决定了处理能力、内存和存储容量等因素。 Azure 提供各种大小来支持多种类型的用途。

Azure 根据 VM 的大小和操作系统按[小时价格](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)计费。 对于不足一小时的部分，Azure 仅根据使用的分钟数计费。 存储将另行定价和收费。

### <a name="vm-limits"></a>VM 限制
订阅附带默认的[配额限制](../../azure-subscription-service-limits.md)，在为项目部署大量 VM 时，这些限制可能会造成影响。 每个订阅的当前限制是每区域 20 个 VM。 可以开具支持票证来请求提高限制。

### <a name="operating-system-disks-and-images"></a>操作系统磁盘和映像
虚拟机使用[虚拟硬盘 (VHD)](../../storage/storage-about-disks-and-vhds-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 来存储其操作系统 (OS) 和数据。 VHD 还可用于存储映像，你可以选择某个映像来安装 OS。 

Azure 提供许多[应用商店映像](https://azure.microsoft.com/marketplace/virtual-machines/)来配合各种版本和类型的 Windows Server 操作系统使用。 应用商店映像由映像发布者、产品、SKU 和版本（通常指定为最新版本）标识。 

下表显示了查找映像信息的一些方法。

| 方法 | 说明 |
| --- | --- |
| Azure 门户 |选择要使用的映像时，系统会自动指定值。 |
| Azure PowerShell |[Get-AzureRMVMImagePublisher](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.5.0/get-azurermvmimagepublisher) -Location "location"<BR>[Get-AzureRMVMImageOffer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.5.0/get-azurermvmimageoffer) -Location "location" -Publisher "publisherName"<BR>[Get-AzureRMVMImageSku](/powershell/module/azurerm.compute/get-azurermvmimagesku) -Location "location" -Publisher "publisherName" -Offer "offerName" |
| REST API |[列出映像发布者](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publishers)<BR>[列出映像产品](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publisher-offers)<BR>[列出映像 SKU](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publisher-offer-skus) |

可以选择[上载并使用自己的映像](upload-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)，在这种情况下，无需使用发布者名称、产品和 SKU。

### <a name="extensions"></a>扩展
VM [扩展](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)通过部署后的配置和自动化任务来增加 VM 的功能。

可以使用扩展完成以下常见任务：

* **运行自定义脚本** – 预配 VM 时，[自定义脚本扩展](extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)可以通过运行脚本，帮助在 VM 上配置工作负荷。
* **部署和管理配置** – 可以借助 [PowerShell Desired State Configuration (DSC) 扩展](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)在 VM 上设置用于管理配置和环境的 DSC。
* **收集诊断数据** – 可以借助 [Azure 诊断扩展](extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)将 VM 配置为收集诊断数据，然后，可以使用这些数据监视应用程序的运行状况。

### <a name="related-resources"></a>相关资源
VM 使用下表中的资源，创建 VM 时，这些资源必须存在，否则要予以创建。

| 资源 | 必选 | 说明 |
| --- | --- | --- |
| [资源组](../../azure-resource-manager/resource-group-overview.md) |是 |VM 必须包含在资源组中。 |
| [存储帐户](../../storage/storage-create-storage-account.md) |是 |VM 需要使用存储帐户来存储其虚拟硬盘。 |
| [虚拟网络](../../virtual-network/virtual-networks-overview.md) |是 |VM 必须是虚拟网络的成员。 |
| [公共 IP 地址](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) |否 |可以向 VM 分配一个公共 IP 地址，以便远程访问它。 |
| [网络接口](../../virtual-network/virtual-network-network-interface.md) |是 |VM 需要使用网络接口在网络中通信。 |
| [数据磁盘](attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |否 |VM 可以包含数据磁盘，以便扩展存储功能。 |

## <a name="how-do-i-create-my-first-vm"></a>如何创建第一个 VM？
可以选择多种方法创建 VM。 选择哪种方法取决于所在的环境。 

下表提供的信息可帮助你开始创建 VM。

| 方法 | 文章 |
| --- | --- |
| Azure 门户 |[使用门户创建运行 Windows 的虚拟机](../virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| 模板 |[使用 Resource Manager 模板创建 Windows 虚拟机](ps-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Azure PowerShell |[使用 PowerShell 创建 Windows VM](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| 客户端 SDK |[使用 C 部署 Azure 资源](csharp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| REST API |[创建或更新 VM](https://docs.microsoft.com/rest/api/compute/virtualmachines/virtualmachines-create-or-update) |

问题偶尔会不期而至。 如果遇到了问题，请查看 [Troubleshoot Resource Manager deployment issues with creating a Windows virtual machine in Azure](troubleshoot-deployment-new-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)（排查在 Azure 中创建 Windows 虚拟机时遇到的 Resource Manager 部署问题）。

## <a name="how-do-i-manage-the-vm-that-i-created"></a>如何管理创建的 VM？
可以使用基于浏览器的门户、支持脚本的命令行工具或直接通过 API 管理 VM。 可能要执行的一些常见管理任务包括获取有关 VM 的信息、登录到 VM、管理可用性以及执行备份。

### <a name="get-information-about-a-vm"></a>获取有关 VM 的信息
下表显示了获取有关 VM 的信息的一些方法。

| 方法 | 说明 |
| --- | --- |
| Azure 门户 |在中心菜单中，单击“虚拟机”，然后从列表中选择 VM。 在 VM 的边栏选项卡中，可以访问概述信息、设置值以及监视指标。 |
| Azure PowerShell |若要了解如何使用 PowerShell 来管理 VM，请参阅[使用 Azure PowerShell 模块创建和管理 Windows VM](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 |
| REST API |使用 [获取 VM 信息](https://docs.microsoft.com/rest/api/compute/virtualmachines/virtualmachines-get) 操作获取有关 VM 的信息。 |
| 客户端 SDK |有关使用 C# 管理 VM 的信息，请参阅 [Manage Azure Virtual Machines using Azure Resource Manager and C#](csharp-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)（使用 Azure Resource Manager 与 C# 来管理 Azure 虚拟机）。 |

### <a name="log-on-to-the-vm"></a>登录到 VM
使用 Azure 门户中的“连接”按钮[启动远程桌面 (RDP) 会话](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 尝试使用远程连接时，有时可能会出错。 如果遇到这种情况，请查看 [Troubleshoot Remote Desktop connections to an Azure virtual machine running Windows](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)（对运行 Windows 的 Azure 虚拟机的远程桌面连接进行故障排除）中的帮助信息。

### <a name="manage-availability"></a>管理可用性
必须知道如何[确保应用程序的高可用性](manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 此配置涉及到创建多个 VM，确保至少有一个 VM 在运行。

为了使部署符合 VM 运行时间达到 99.95% 的服务级别协议，必须在[可用性集](infrastructure-availability-sets-guidelines.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)中部署两个或更多个运行工作负荷的 VM。 此配置可确保 VM 分布到多个容错域，并使用不同的维护时段部署到主机上。 完整 [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_0/) 说明了 Azure 作为整体的保证可用性。

### <a name="back-up-the-vm"></a>备份 VM
[恢复服务保管库](../../backup/backup-introduction-to-azure-backup.md)用于保护 Azure 备份和 Azure Site Recovery 服务中的数据与资产。 可以使用恢复服务保管库，[通过 PowerShell 来部署和管理采用 Resource Manager 部署模型的 VM 的备份](../../backup/backup-azure-vms-automation.md)。 

## <a name="next-steps"></a>后续步骤
* 如果有意使用 Linux VM，请查看 [Azure and Linux](../linux/overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)（Azure 和 Linux）。
* 在 [Example Azure infrastructure walkthrough](infrastructure-example.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)（示例 Azure 基础结构演练）中查看有关设置基础结构的指导。
* 请务必遵循 [Best Practices for running a Windows VM on Azure](guidance-compute-single-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)（在 Azure 上运行 Windows VM 的最佳实践）。


