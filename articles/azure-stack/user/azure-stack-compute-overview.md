---
title: "介绍 Azure 堆栈的虚拟机"
description: "了解 Azure 堆栈虚拟机"
services: azure-stack
author: anjayajodha
ms.service: azure-stack
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: victorh
ms.openlocfilehash: c37ad8ac5b6c37261e22237e843dd97e2bbd09f9
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2017
---
# <a name="introduction-to-azure-stack-virtual-machines"></a>介绍 Azure 堆栈的虚拟机

*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*

## <a name="overview"></a>概述
Azure 堆栈虚拟机 (VM) 是一种类型的 Azure 堆栈提供的按需、 可缩放计算资源。 通常情况下，如果需要对计算环境进行控制，而使用 VM 相对于其他选择来说可以更好地进行控制，则应选择 VM。 本文介绍创建 VM 之前的注意事项，以及 VM 的创建方法和管理方式。

Azure 堆栈 VM 可而无需管理单个的群集或多台虚拟化的灵活性。 不过，仍然需要通过执行任务来维护 VM，例如，配置、修补和安装在 VM 上运行的软件。

可以通过多种方式使用 azure 堆栈的虚拟机。 例如：

* **开发和测试**– Azure 堆栈 Vm 提供快速并轻松地完成与特定配置创建一台计算机所需的代码和测试应用程序。

* **云中的应用程序**– 应用程序需求可以波动情况，因为它可能会使经济意义上，在 Azure 堆栈中的 VM 上运行它。 使用 VM 时，需要支付额外的费用；关闭 VM 时，则无需付费。

* **扩展数据中心**– Azure 堆栈虚拟网络中的虚拟机可以轻松地连接到你的组织网络或 Azure。

可以根据需要，将应用程序使用的 VM 数纵向扩展和横向扩展为任意数目。

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>在创建 VM 之前需要考虑哪些因素？

当你将构建出 Azure 堆栈中的应用程序基础结构时，始终是有多种设计注意事项。 在开始之前，必须考虑到 VM 的以下重要方面：

- 应用程序资源的名称
- VM 的大小
- 可以创建的 VM 数目上限
- VM 运行的操作系统
- VM 在启动后的配置 
- VM 所需的相关资源

### <a name="naming"></a>命名

虚拟机都具有分配给它的名称，它具有作为操作系统的一部分配置的计算机名称。 VM 的名称最多可包含 15 个字符。

如果你使用 Azure 堆栈创建操作系统磁盘，计算机名称和虚拟机名称都相同。 如果将上载并使用你自己包含以前配置的操作系统的映像，使用它来创建虚拟机，则名称可能不同。 当你上载图像文件，使计算机名称在操作系统中，虚拟机名称作为最佳做法相同。

### <a name="vm-size"></a>VM 大小

你使用 VM 的大小由你想要运行的工作负载确定。 然后，所选大小又会影响多个因素，例如处理能力、内存和存储容量。 Azure 堆栈提供各种大小来支持多种类型的用途。

### <a name="vm-limits"></a>VM 限制

你的订阅已有可能影响你的项目的许多虚拟机的部署默认配额限制。 每个订阅的当前限制是每区域 20 个 VM。

### <a name="operating-system-disks-and-images"></a>操作系统磁盘和映像

虚拟机使用虚拟硬盘 (VHD) 来存储其操作系统 (OS) 和数据。 VHD 还可用于存储映像，可以选择某个映像来安装 OS。
Azure 堆栈提供应用商店以用于各种版本和类型的操作系统。 Marketplace 映像由映像发布者、产品、SKU 和版本（通常指定为最新版本）标识。

下表显示了你可以查找映像的信息的一些方法：


|方法|说明|
|---------|---------|
|Azure 堆栈门户|选择要使用的映像时，系统会自动指定值。|
|Azure Stack PowerShell|`Get-AzureRMVMImagePublisher -Location "location"`<br>`Get-AzureRMVMImageOffer -Location "location" -Publisher "publisherName"`<br>`Get-AzureRMVMImageSku -Location "location" -Publisher "publisherName" -Offer "offerName"`|
|REST API     |[列出映像发布者](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publishers)<br>[列出映像产品](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publisher-offers)<br>[列出映像的 Sku](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publisher-offer-skus)|

你可以选择上载并使用你自己的映像。 如果这样做，则不使用发布者名称、 产品和 sku。

### <a name="extensions"></a>扩展

VM 扩展提供你 VM 通过 post 部署配置和自动化的任务的其他功能。
可以使用扩展完成以下常见任务：

* 运行自定义脚本 – 自定义脚本扩展可帮助你通过运行你的脚本设置 VM 在 VM 上配置工作负荷。
* 部署和管理配置 – PowerShell Desired State Configuration (DSC) 扩展，帮助你设置虚拟机上的 DSC 以管理配置和环境。
* 收集诊断数据 – Azure 诊断扩展可帮助你配置 VM 以收集用于监视你的应用程序的运行状况的诊断数据。

### <a name="related-resources"></a>相关资源

下表中的资源使用的 VM，需要存在，或者创建 VM 时创建。


|资源|必选|说明|
|---------|---------|---------|
|资源组|是|VM 必须包含在资源组中。|
|存储帐户|是|VM 需要使用存储帐户来存储其虚拟硬盘。|
|虚拟网络|是|VM 必须是虚拟网络的成员。|
|公共 IP 地址|否|可以向 VM 分配一个公共 IP 地址，以便远程访问它。|
|网络接口|是|VM 需要使用网络接口在网络中通信。|
|数据磁盘数|否|VM 可以包含数据磁盘，以便扩展存储功能。|

## <a name="how-do-i-create-my-first-vm"></a>如何创建第一个 VM？

你有若干种选择，以创建 VM。 你的选择取决于你的环境。
下表提供了信息可让你可以开始创建你的 VM。


|方法|文章|
|---------|---------|
|Azure 堆栈门户|使用 Azure 堆栈门户创建 Windows 虚拟机<br>[创建 Linux 虚拟机使用 Azure 堆栈门户](azure-stack-quick-linux-portal.md)|
|模板|Azure 堆栈快速入门模板位于以下位置：<br> [https://github.com/Azure/AzureStack-QuickStart-Templates](https://github.com/Azure/AzureStack-QuickStart-Templates)|
|PowerShell|[在 Azure 堆栈使用 PowerShell 创建 Windows 虚拟机](azure-stack-quick-create-vm-windows-powershell.md)<br>[在 Azure 堆栈中使用 PowerShell 创建 Linux 虚拟机](azure-stack-quick-create-vm-linux-powershell.md)|
|CLI|[在 Azure 堆栈中使用 CLI 创建 Windows 虚拟机](azure-stack-quick-create-vm-windows-cli.md)<br>[在 Azure 堆栈中使用 CLI 创建 Linux 虚拟机](azure-stack-quick-create-vm-linux-cli.md)|

## <a name="how-do-i-manage-the-vm-that-i-created"></a>如何管理创建的 VM？

可以使用基于浏览器的门户、支持脚本的命令行工具或直接通过 API 管理 VM。 可能要执行的一些常见管理任务包括获取有关 VM 的信息、登录到 VM、管理可用性以及执行备份。

### <a name="get-information-about-a-vm"></a>获取有关 VM 的信息

下表显示了一些你可以获取有关 VM 的信息的方式。


|方法|说明|
|---------|---------|
|Azure 堆栈门户|在中心菜单上，单击虚拟机，然后从列表中选择 VM。 在虚拟机页上，可以访问的概述信息，设置值，以及监视度量值。|
|Azure PowerShell|管理 Vm 是在 Azure 和 Azure 堆栈类似。 有关使用 PowerShell 的详细信息，请参阅以下 Azure 主题：<br>[创建和管理的 Azure PowerShell 模块的 Windows 虚拟机](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-manage-vm#understand-vm-sizes)|
|客户端 SDK|使用 C# 来管理虚拟机是在 Azure 和 Azure 堆栈类似。 有关详细信息，请参阅以下 Azure 主题：<br>[创建和管理在 Azure 中使用 C# 中的 Windows Vm](https://docs.microsoft.com/azure/virtual-machines/windows/csharp)|

### <a name="connect-to-the-vm"></a>连接到 VM

你可以使用**连接**Azure 堆栈门户连接到你的 VM 中的按钮。

## <a name="next-steps"></a>后续步骤
* [Azure 堆栈中的虚拟机的注意事项](azure-stack-vm-considerations.md)

