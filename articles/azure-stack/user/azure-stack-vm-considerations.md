---
title: Azure Stack 中虚拟机的差异和注意事项 | Microsoft Docs
description: 了解 Azure Stack 中虚拟机的差异和用法注意事项。
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 6613946D-114C-441A-9F74-38E35DF0A7D7
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2018
ms.author: brenduns
ms.openlocfilehash: 8c9fd7d5824e5d315a7dd30e5052fe10802d197e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="considerations-for-virtual-machines-in-azure-stack"></a>Azure Stack 中虚拟机的注意事项

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

虚拟机是 Azure Stack 提供的按需可缩放计算资源。 使用虚拟机时，必须了解 Azure 与 Azure Stack 中所提供的功能之间的差异。 本文概述特定于 Azure Stack 中的虚拟机及其功能的注意事项。 有关 Azure Stack 与 Azure 之间的大致差异的详细信息，请参阅[重要注意事项](azure-stack-considerations.md)一文。

## <a name="cheat-sheet-virtual-machine-differences"></a>速查表：虚拟机的差异

| 特性 | Azure（公有云） | Azure Stack |
| --- | --- | --- |
| 虚拟机映像 | Azure Marketplace 包含可用于创建虚拟机的映像。 若要查看 Azure Marketplace 中的可用映像列表，请参阅 [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/compute?subcategories=virtual-machine-images&page=1) 页。 | Azure Stack Marketplace 中默认不会提供任何映像。 Azure Stack 云管理员应该先将映像发布或下载到 Azure Stack Marketplace，然后用户才能使用这些映像。 |
| 虚拟机大小 | Azure 支持各种不同的虚拟机大小。 若要了解可用的大小和选项，请参阅 [Windows 虚拟机大小](../../virtual-machines/virtual-machines-windows-sizes.md)和 [Linux 虚拟机大小](../../virtual-machines/linux/sizes.md)主题。 | Azure Stack 支持一部分可在 Azure 中使用的虚拟机大小。 若要查看支持的大小列表，请参阅本文的[虚拟机大小](#virtual-machine-sizes)部分。 |
| 虚拟机配额 | [配额限制](../../azure-subscription-service-limits.md#service-specific-limits)由 Microsoft 设置 | Azure Stack 云管理员在将虚拟机提供给用户之前，必须先配置配额。 |
| 虚拟机扩展 |Azure 支持各种不同的虚拟机扩展。 若要了解可用的扩展，请参阅[虚拟机扩展和功能](../../virtual-machines/windows/extensions-features.md)一文。| Azure Stack 支持一部分可在 Azure 中使用的扩展，每个扩展有特定的版本。 Azure Stack 云管理员可以选择要将哪些扩展提供给其用户使用。 若要查看支持的扩展列表，请参阅本文的[虚拟机扩展](#virtual-machine-extensions)部分。 |
| 虚拟机网络 | 分配给租户虚拟机的公共 IP 地址可通过 Internet 访问。<br><br><br>Azure 虚拟机有固定的 DNS 名称 | 只能在 Azure Stack 开发工具包环境中访问分配给租户虚拟机的公共 IP 地址。 用户必须能够通过 [RDP](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop) 或 [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) 访问 Azure Stack 开发工具包，才能连接到在 Azure Stack 中创建的虚拟机。<br><br>在特定 Azure Stack 实例中创建的虚拟机的 DNS 名称基于云管理员配置的值。 |
| 虚拟机存储 | 支持[托管磁盘](../../virtual-machines/windows/managed-disks-overview.md)。 | Azure Stack 尚不支持托管磁盘。 |
| API 版本 | Azure 始终提供所有虚拟机功能的最新 API 版本。 | Azure Stack 支持特定的 Azure 服务以及这些服务的特定 API 版本。 若要查看支持的 API 版本列表，请参阅本文的 [API 版本](#api-versions)部分。 |
|虚拟机可用性集|多个容错域（每个区域 2 个或 3 个）<br>多个更新域<br>支持托管磁盘|多个容错域（每个区域 2 个或 3 个）<br>多个更新域（最多 20 个）<br>不支持托管磁盘|
|虚拟机规模集|支持自动缩放|不支持自动缩放<br>使用门户、资源管理器模板或 PowerShell 将更多实例添加到规模集。

## <a name="virtual-machine-sizes"></a>虚拟机大小
Azure 以多种方式施加资源限制，以避免资源（服务器本地和服务级别）的过度消耗。 如果未对租户施加一些资源消耗限制，当干扰性邻居过度消耗资源时，租户体验就会变差。 
- VM 的网络出口有带宽上限。 Azure Stack 中的上限与 Azure 中的上限匹配。  
- 对于存储资源，Azure Stack 实施存储 IOPs 限制，以避免租户为了访问存储而造成资源过度消耗。 
- 对于附加了多个数据磁盘的 VM，每个数据磁盘的最大吞吐量为 500 IOPS（适用于 HHD）和 2300 IOPS（适用于 SSD）。

下表列出了 Azure Stack 支持的 VM 及其配置：

| 类型           | 大小          | 支持的大小范围 |
| ---------------| ------------- | ------------------------ |
|常规用途 |基本 A        |[A0 - A4](azure-stack-vm-sizes.md#basic-a)                   |
|常规用途 |标准 A     |[A0 - A7](azure-stack-vm-sizes.md#standard-a)              |
|常规用途 |D 系列       |[D1 - D4](azure-stack-vm-sizes.md#d-series)              |
|常规用途 |Dv2 系列     |[D1_v2 - D5_v2](azure-stack-vm-sizes.md#ds-series)        |
|常规用途 |DS 系列      |[DS1 - DS4](azure-stack-vm-sizes.md#dv2-series)            |
|常规用途 |DSv2 系列    |[DS1_v2 - DS5_v2](azure-stack-vm-sizes.md#dsv2-series)      |
|优化内存|D 系列       |[D11 - D14](azure-stack-vm-sizes.md#mo-d)            |
|优化内存|DS 系列      |[DS11 - DS14](azure-stack-vm-sizes.md#mo-ds)|
|优化内存|Dv2 系列     |[D11_v2 - DS14_v2](azure-stack-vm-sizes.md#mo-dv2)     |
|优化内存|DSv2 系列 -  |[DS11_v2 - DS14_v2](azure-stack-vm-sizes.md#mo-dsv2)    |

Azure Stack 与 Azure 之间的虚拟机大小及其关联资源数量是一致的。 这种一致性包括内核数量和可以创建的数据磁盘数量/大内存量。 但是，Azure Stack 中相同 VM 大小的性能取决于特定 Azure Stack 环境的基础特征。

## <a name="virtual-machine-extensions"></a>虚拟机扩展

 Azure Stack 包含少量的扩展。 可以通过 Marketplace 联合来获取更新和其他扩展。

使用以下 PowerShell 脚本可获取 Azure Stack 环境中可用的虚拟机扩展的列表：

```powershell
Get-AzureRmVmImagePublisher -Location local | `
  Get-AzureRmVMExtensionImageType | `
  Get-AzureRmVMExtensionImage | `
  Select Type, Version | `
  Format-Table -Property * -AutoSize
```

## <a name="api-versions"></a>API 版本

Azure Stack 中的虚拟机功能支持以下 API 版本：

![VM 资源类型](media/azure-stack-vm-considerations/vm-resoource-types.png)

可以使用以下 PowerShell 脚本来获取 Azure Stack 环境中可用的虚拟机功能的 API 版本：

```powershell
Get-AzureRmResourceProvider | `
  Select ProviderNamespace -Expand ResourceTypes | `
  Select * -Expand ApiVersions | `
  Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} | `
  where-Object {$_.ProviderNamespace -like “Microsoft.compute”}
```
如果云运营商将 Azure Stack 环境更新为较新版本，则支持的资源类型和 API 版本列表可能有所不同。

## <a name="windows-activation"></a>Windows 激活

必须根据产品使用权和 Microsoft 许可条款使用 Windows 产品。 Azure 堆栈使用[自动 VM 激活](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v%3dws.11))(AVMA) 来激活 Windows Server 虚拟机 (Vm)。 
 - 因为 Azure 堆栈主机激活 AVMA 密钥与 Windows Server 2016，所有虚拟机的运行 Windows Server 2012 或更高版本会自动激活。
 - Vm 的运行的 Windows Server 2008 R2 未自动激活，必须通过使用激活[MAK 激活](https://technet.microsoft.com/library/ff793438.aspx)。 

Microsoft Azure 使用 KMS 激活来激活 Windows Vm。 如果你移动到 Azure 和遇到虚拟机与 Azure 堆栈激活问题，请参阅[解决 Azure Windows 虚拟机激活问题](https://docs.microsoft.com/azure/virtual-machines/windows/troubleshoot-activation-problems)。 处找不到的其他信息[疑难解答的 Windows Azure Vm 上的激活故障](https://blogs.msdn.microsoft.com/mast/2017/06/14/troubleshooting-windows-activation-failures-on-azure-vms/)Azure 支持团队博客文章。




## <a name="next-steps"></a>接下来的步骤

[在 Azure Stack 中使用 PowerShell 创建 Windows 虚拟机](azure-stack-quick-create-vm-windows-powershell.md)
