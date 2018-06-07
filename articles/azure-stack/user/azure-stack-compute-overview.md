---
title: Azure Stack 虚拟机简介
description: 了解 Azure Stack 虚拟机
services: azure-stack
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.topic: get-started-article
ms.date: 05/21/2018
ms.author: mabrigg
ms.reviewer: kivenkat
ms.openlocfilehash: 967fcb86c1bf0c85517bc13c2066ed32e8fa28d9
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34604125"
---
# <a name="introduction-to-azure-stack-virtual-machines"></a>Azure Stack 虚拟机简介

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

Azure 堆栈提供一种类型的按需、 可缩放的计算资源与虚拟机 (Vm)。 当你需要更好地控制计算环境比其他选项时，你可以选择 VM。 在创建你的 VM 之前，本文提供了详细信息。

Azure Stack VM 可提供虚拟化的灵活性，而无需管理群集或单个计算机。 不过，仍然需要通过执行任务（例如，配置、修补和安装在 VM 上运行的软件）来维护 VM。

可通过多种方式使用 Azure Stack 虚拟机。 例如：

- **开发和测试**  
    Azure 堆栈 Vm 提供一种快速而简单的方法来创建使用特定的配置代码所需的计算机和测试应用程序。

- **云中的应用程序**  
    应用程序需求可以波动情况，因为它可能会使经济意义上，在 Azure 堆栈中的 VM 上运行它。 使用 VM 时，需要支付额外的费用；关闭 VM 时，则无需付费。

- **扩展数据中心**  
    到您的组织网络或 Azure，可以轻松连接 Azure 堆栈虚拟网络中的虚拟机。

可以根据需要，将应用程序使用的 VM 纵向或横向扩展为任意数目。

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>在创建 VM 之前需要考虑哪些因素？

在 Azure Stack 中构建应用程序基础结构时，始终要考虑多种设计注意事项。 在开始创建基础结构之前，必须考虑到 VM 的以下重要方面：

- 应用程序资源的名称。
- VM 的大小。
- 可以创建的 VM 数目上限。
- VM 运行的操作系统。
- VM 在启动后的配置。
- VM 所需的相关资源。

### <a name="naming"></a>命名

虚拟机会被指定名称，也具有在操作系统中所配置的计算机名称。 VM 的名称最多可包含 15 个字符。

如果使用 Azure Stack 创建操作系统磁盘，则计算机名称与虚拟机名称相同。 如果上传并使用自己的映像（该映像包含先前配置的操作系统），并使用它创建虚拟机，则名称可能会不同。 上传自己的映像文件时，操作系统中的计算机名称与虚拟机名称最好相同。

### <a name="vm-size"></a>VM 大小

使用的 VM 大小取决于要运行的工作负荷。 然后，所选大小又会影响多个因素，例如处理能力、内存和存储容量。 Azure Stack 提供各种大小来支持多种类型的用途。

### <a name="vm-limits"></a>VM 限制

订阅设有默认的配额限制，可能会影响如何部署项目的许多 VM。 每个订阅的当前限制是每区域 20 个 VM。

### <a name="operating-system-disks-and-images"></a>操作系统磁盘和映像

虚拟机使用虚拟硬盘 (VHD) 来存储其操作系统 (OS) 和数据。 VHD 还可用于存储映像，可以选择某个映像来安装 OS。
Azure Stack 提供一个 Marketplace，适用于各种版本和类型的操作系统。 Marketplace 映像由映像发布者、产品/服务、SKU 和版本（通常指定为最新版本）标识。

下表显示了查找映像信息的一些方法：

|方法|说明|
|---------|---------|
|Azure Stack 门户|选择要使用的映像时，系统会自动指定值。|
|Azure Stack PowerShell|`Get-AzureRMVMImagePublisher -Location "location"`<br>`Get-AzureRMVMImageOffer -Location "location" -Publisher "publisherName"`<br>`Get-AzureRMVMImageSku -Location "location" -Publisher "publisherName" -Offer "offerName"`|
|REST API     |[列出映像发布者](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publishers)<br>[列出映像产品](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publisher-offers)<br>[列出映像 SKU](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publisher-offer-skus)|

可以选择上传并使用自己的映像。 如果这样做，则不会使用发布者名称、产品/服务和 SKU。

### <a name="extensions"></a>扩展

VM 扩展通过部署后配置和自动化任务来增加 VM 的功能。
可以使用扩展完成以下常见任务：

- **运行自定义脚本**  
    自定义脚本扩展可帮助你通过运行你的脚本设置 VM 在 VM 上配置工作负荷。

- **部署和管理配置**  
    PowerShell Desired State Configuration (DSC) 扩展名可帮助你设置虚拟机上的 DSC 以便管理配置和环境。

- **收集诊断数据**  
    Azure 诊断扩展可帮助你配置 VM 以收集用于监视你的应用程序的运行状况的诊断数据。

### <a name="related-resources"></a>相关资源

下表中的资源由 VM 使用，在创建 VM 时必须存在或已创建。


|资源|需要|说明|
|---------|---------|---------|
|资源组|是|VM 必须包含在资源组中。|
|存储帐户|是|VM 需要使用存储帐户来存储其虚拟硬盘。|
|虚拟网络|是|VM 必须是虚拟网络的成员。|
|公共 IP 地址|否|可以向 VM 分配一个公共 IP 地址，以便远程访问它。|
|网络接口|是|VM 需要使用网络接口在网络中通信。|
|数据磁盘数|否|VM 可以包含数据磁盘，以便扩展存储功能。|

## <a name="create-your-first-vm"></a>创建第一个 VM

创建 VM 有多种选择。 你的选择取决于环境。
下表提供信息来帮助你开始创建 VM。


|方法|文章|
|---------|---------|
|Azure Stack 门户|使用 Azure Stack 门户创建 Windows 虚拟机<br>[使用 Azure Stack 门户创建 Linux 虚拟机](azure-stack-quick-linux-portal.md)|
|模板|Azure Stack 快速入门模板位于以下位置：<br> [https://github.com/Azure/AzureStack-QuickStart-Templates](https://github.com/Azure/AzureStack-QuickStart-Templates)|
|PowerShell|[在 Azure Stack 中使用 PowerShell 创建 Windows 虚拟机](azure-stack-quick-create-vm-windows-powershell.md)<br>[在 Azure Stack 中使用 PowerShell 创建 Linux 虚拟机](azure-stack-quick-create-vm-linux-powershell.md)|
|CLI|[在 Azure Stack 中使用 CLI 创建 Windows 虚拟机](azure-stack-quick-create-vm-windows-cli.md)<br>[在 Azure Stack 中使用 CLI 创建 Linux 虚拟机](azure-stack-quick-create-vm-linux-cli.md)|

## <a name="manage-your-vm"></a>管理 VM

可以使用基于浏览器的门户、支持脚本的命令行工具或直接通过 API 管理 VM。 可能执行的一些典型管理任务包括：

- 获取有关 VM 的信息
- 连接到 VM
- 管理可用性
- 进行备份

### <a name="get-information-about-your-vm"></a>获取有关你的 VM 的信息

下表显示了获取有关 VM 的信息的一些方法。


|方法|说明|
|---------|---------|
|Azure Stack 门户|在中心菜单中，单击“虚拟机”，然后从列表中选择 VM。 在 VM 的页面上，可以访问概述信息、设置值以及监视指标。|
|Azure PowerShell|在 Azure 和 Azure Stack 中，管理 VM 的方法很相似。 有关使用 PowerShell 的详细信息，请参阅以下 Azure 主题：<br>[使用 Azure PowerShell 模块创建和管理 Windows VM](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-manage-vm#understand-vm-sizes)|
|客户端 SDK|在 Azure 和 Azure Stack 中，使用 C# 管理 VM 的方法很相似。 有关详细信息，请参阅以下 Azure 主题：<br>[在 Azure 中使用 C# 创建和管理 Windows VM](https://docs.microsoft.com/azure/virtual-machines/windows/csharp)|

### <a name="connect-to-your-vm"></a>连接到 VM

在 Azure Stack 门户中，可以使用“连接”按钮连接到 VM。

## <a name="next-steps"></a>后续步骤

- [Azure Stack 中虚拟机的注意事项](azure-stack-vm-considerations.md)