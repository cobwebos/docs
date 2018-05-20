---
title: Azure Stack 虚拟机简介
description: 了解 Azure Stack 虚拟机
services: azure-stack
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.topic: get-started-article
ms.date: 02/28/2018
ms.author: mabrigg
ms.openlocfilehash: 41e75a6806cc5ff13fad64fd415344376e0d6e88
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/11/2018
---
# <a name="introduction-to-azure-stack-virtual-machines"></a>Azure Stack 虚拟机简介

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

## <a name="overview"></a>概述
Azure 堆栈虚拟机 (VM) 是一种类型的一个按需、 可缩放的计算资源，它提供了 Azure 堆栈。 通常情况下，如果需要对计算环境进行控制，而使用 VM 相对于其他选择来说可以更好地进行控制，则应选择 VM。 本文介绍创建 VM 之前的注意事项，以及 VM 的创建方法和管理方式。

Azure 堆栈 VM 可而无需管理群集或单独的计算机的虚拟化的灵活性。 但是，你仍需要通过执行任务，例如配置、 修补和安装在其运行的软件维护 VM。

你可以通过多种方式来使用 Azure 堆栈虚拟机。 例如：

* **开发和测试**– Azure 堆栈 Vm 提供快速并轻松地完成与特定配置创建一台计算机所需的代码和测试应用程序。

* **云中的应用程序**– 应用程序需求可以波动情况，因为它可能会使经济意义上，在 Azure 堆栈中的 VM 上运行它。 使用 VM 时，需要支付额外的费用；关闭 VM 时，则无需付费。

* **扩展数据中心**– Azure 堆栈虚拟网络中的虚拟机轻松地到您的组织网络或 Azure 连接。

你的应用程序使用可以向上扩展或向外扩展到任何是必需的以满足你需求的 Vm。

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>在创建 VM 之前需要考虑哪些因素？

当你将构建出 Azure 堆栈中的应用程序基础结构时，始终是有大量的设计注意事项。 VM 的这些方面是重要开始创建你的基础结构之前需要考虑：

* 你的应用程序资源的名称。
* VM 的大小。
* 可以创建的 Vm 的最大数目。
* VM 运行的操作系统。
* VM 在其开始后的配置。
* VM 需要相关的资源。

### <a name="naming"></a>命名

虚拟机会被指定名称，也具有在操作系统中所配置的计算机名称。 VM 的名称最多可包含 15 个字符。

如果使用 Azure Stack 创建操作系统磁盘，则计算机名称与虚拟机名称相同。 如果上传并使用自己的映像（该映像包含先前配置的操作系统），并使用它创建虚拟机，则名称可能会不同。 上传自己的映像文件时，操作系统中的计算机名称与虚拟机名称最好相同。

### <a name="vm-size"></a>VM 大小

使用的 VM 大小取决于要运行的工作负荷。 然后，所选大小又会影响多个因素，例如处理能力、内存和存储容量。 Azure Stack 提供各种大小来支持多种类型的用途。

### <a name="vm-limits"></a>VM 限制

订阅设有默认的配额限制，可能会影响如何部署项目的许多 VM。 每个订阅的当前限制是每区域 20 个 VM。

### <a name="operating-system-disks-and-images"></a>操作系统磁盘和映像

虚拟机使用虚拟硬盘 (VHD) 来存储其操作系统 (OS) 和数据。 VHD 还可用于存储映像，可以选择某个映像来安装 OS。
Azure Stack 提供一个 Marketplace，适用于各种版本和类型的操作系统。 应用商店映像标识通过映像发布者、 产品/服务、 sku、 和版本 （通常版本被指定为最新。）

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

* 运行自定义脚本 – 自定义脚本扩展可帮助你通过运行你的脚本设置 VM 在 VM 上配置工作负荷。
* 部署和管理配置 – PowerShell Desired State Configuration (DSC) 扩展，帮助你设置虚拟机上的 DSC 以管理配置和环境。
* 收集诊断数据 – Azure 诊断扩展可帮助你配置 VM 以收集用于监视你的应用程序的运行状况的诊断数据。

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

## <a name="how-do-i-create-my-first-vm"></a>如何创建第一个 VM？

创建 VM 有多种选择。 你的选择取决于环境。
下表提供信息来帮助你开始创建 VM。


|方法|文章|
|---------|---------|
|Azure Stack 门户|使用 Azure Stack 门户创建 Windows 虚拟机<br>[使用 Azure Stack 门户创建 Linux 虚拟机](azure-stack-quick-linux-portal.md)|
|模板|Azure Stack 快速入门模板位于以下位置：<br> [https://github.com/Azure/AzureStack-QuickStart-Templates](https://github.com/Azure/AzureStack-QuickStart-Templates)|
|PowerShell|[在 Azure Stack 中使用 PowerShell 创建 Windows 虚拟机](azure-stack-quick-create-vm-windows-powershell.md)<br>[在 Azure Stack 中使用 PowerShell 创建 Linux 虚拟机](azure-stack-quick-create-vm-linux-powershell.md)|
|CLI|[在 Azure Stack 中使用 CLI 创建 Windows 虚拟机](azure-stack-quick-create-vm-windows-cli.md)<br>[在 Azure Stack 中使用 CLI 创建 Linux 虚拟机](azure-stack-quick-create-vm-linux-cli.md)|

## <a name="how-do-i-manage-the-vm-that-i-created"></a>如何管理创建的 VM？

你可以管理使用命令行工具与用于脚本编写，或直接通过 Api 支持的基于浏览器的门户中，虚拟机。 你可能要执行一些常见的管理任务包括：

* 获取有关 VM 的信息
* 连接到该 VM
* 管理可用性
* 进行备份

### <a name="get-information-about-a-vm"></a>获取有关 VM 的信息

下表显示了获取有关 VM 的信息的一些方法。


|方法|说明|
|---------|---------|
|Azure Stack 门户|在中心菜单中，单击“虚拟机”，然后从列表中选择 VM。 在 VM 的页面上，可以访问概述信息、设置值以及监视指标。|
|Azure PowerShell|在 Azure 和 Azure Stack 中，管理 VM 的方法很相似。 有关使用 PowerShell 的详细信息，请参阅以下 Azure 主题：<br>[使用 Azure PowerShell 模块创建和管理 Windows VM](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-manage-vm#understand-vm-sizes)|
|客户端 SDK|在 Azure 和 Azure Stack 中，使用 C# 管理 VM 的方法很相似。 有关详细信息，请参阅以下 Azure 主题：<br>[在 Azure 中使用 C# 创建和管理 Windows VM](https://docs.microsoft.com/azure/virtual-machines/windows/csharp)|

### <a name="connect-to-the-vm"></a>连接到 VM

在 Azure Stack 门户中，可以使用“连接”按钮连接到 VM。

## <a name="next-steps"></a>后续步骤

* [Azure Stack 中虚拟机的注意事项](azure-stack-vm-considerations.md)
