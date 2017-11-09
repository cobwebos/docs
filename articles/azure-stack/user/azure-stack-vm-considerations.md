---
title: "差异和 Azure 堆栈中的虚拟机的注意事项 |Microsoft 文档"
description: "了解差异和注意事项时使用 Azure 堆栈中的虚拟机。"
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 9/25/2017
ms.author: sngun
ms.openlocfilehash: 7d841dba798c2b706c26dcf51361ce0447710b12
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="considerations-for-virtual-machines-in-azure-stack"></a>Azure 堆栈中的虚拟机的注意事项

*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*

虚拟机是按需，由 Azure 堆栈提供可缩放计算资源。 当你使用虚拟机时，你必须了解有在 Azure 中提供的功能和 Azure 堆栈之间的差异。 本文概述了虚拟机和 Azure 堆栈中的其功能的唯一注意事项。 若要了解有关高级 Azure 堆栈和 Azure 之间的差异信息，请参阅[密钥注意事项](azure-stack-considerations.md)主题。

## <a name="cheat-sheet-virtual-machine-differences"></a>速查表： 虚拟机的差异

| 功能 | Azure （全局） | Azure Stack |
| --- | --- | --- |
| 虚拟机映像 | Azure 应用商店包含可用于创建虚拟机的映像。 请参阅[Azure 应用商店](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/compute?subcategories=virtual-machine-images&page=1)页后，可以查看 Azure 应用商店中可用的映像的列表。 | 默认情况下，不存在的任何映像可用 Azure 堆栈应用商店中。 Azure 堆栈云管理员应该发布或下载到 Azure 堆栈应用商店的映像之后用户可以使用它们。 |
| 虚拟机大小 | Azure 支持各种大小的虚拟机。 若要了解有关可用大小和选项，请参阅[Windows 虚拟机大小](../../virtual-machines/virtual-machines-windows-sizes.md)和[Linux 虚拟机大小](../../virtual-machines/linux/sizes.md)主题。 | Azure 堆栈支持可在 Azure 中的虚拟机大小的子集。 若要查看支持的大小的列表，请参阅[虚拟机大小](#virtual-machine-sizes)部分中的所述。 |
| 虚拟机配额 | [配额限制](../../azure-subscription-service-limits.md#service-specific-limits)由 Microsoft 设置 | Azure 堆栈云管理员必须将分配配额，然后它们向其用户提供虚拟机。 |
| 虚拟机扩展 |Azure 支持各种虚拟机扩展。 若要了解有关可用扩展，请参阅[虚拟机扩展和功能](../../virtual-machines/windows/extensions-features.md)主题。| Azure 堆栈支持在 Azure 中提供的扩展的子集，每个扩展具有特定版本。 Azure 堆栈云管理员可以选择哪些扩展将变得可到为其用户。 若要查看支持的扩展插件的列表，请参见[虚拟机扩展](#virtual-machine-extensions)部分中的所述。 |
| 虚拟机网络 | 分配给租户虚拟机的公共 IP 地址是可访问 Internet。<br><br><br>Azure 虚拟机都具有固定的 DNS 名称 | 分配给租户虚拟机的公共 IP 地址是在仅 Azure 堆栈开发工具包环境中访问。 用户必须有权访问通过 Azure 堆栈开发工具包[RDP](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop)或[VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn)以连接到在 Azure 堆栈中创建的虚拟机。<br><br>特定 Azure 堆栈实例中创建的虚拟机具有 DNS 名称由云管理员配置的值。 |
| 虚拟机存储 | 支持[托管磁盘。](../../virtual-machines/windows/managed-disks-overview.md) | 在 Azure 堆栈中尚不支持被管理的磁盘。 |
| API 版本 | Azure 始终具有最新的 API 版本，所有虚拟机功能。 | Azure 堆栈支持这些服务的特定 Azure 服务和特定的 API 版本。 若要查看支持的 API 版本的列表，请参阅[API 版本](#api-versions)部分中的所述。 |
|虚拟机可用性集|多个容错域 （2 或 3 每个区域）<br>多个更新域<br>管理磁盘支持|单个故障域<br>单个更新域<br>不管理的磁盘支持|
|虚拟机规模集|自动缩放支持|不支持的自动缩放。<br>将多个实例添加到缩放集使用门户、 资源管理器模板或 PowerShell。

## <a name="virtual-machine-sizes"></a>虚拟机大小 

Azure 堆栈开发工具包支持以下大小： 

| 类型 | 大小 | 支持的大小的范围 |
| --- | --- | --- |
|常规用途 |基本 A|A0 A4|
|常规用途 |标准 A|A0-A7|
|常规用途 |标准 D|D1 D4|
|常规用途 |标准 Dv2|D1v2 D5v2|
|内存优化|D 系列|D11 D14|
|内存优化 |Dv2 系列|D11v2 D14v2|

虚拟机大小和其关联的资源数量是 Azure 堆栈与 Azure 之间一致。 例如，这包括内核数量和可以创建的数据磁盘数量/大内存量。 但是，Azure 堆栈中的 VM 大小相同的性能取决于特定的 Azure 堆栈环境的基础特征。

## <a name="virtual-machine-extensions"></a>虚拟机扩展 

 Azure 堆栈开发工具包支持以下虚拟机扩展版本：

![VM 扩展](media/azure-stack-vm-considerations/vm-extensions.png)

使用以下 PowerShell 脚本以获取 Azure 堆栈环境中可用的虚拟机扩展的列表：

```powershell 
Get-AzureRmVmImagePublisher -Location local | `
  Get-AzureRmVMExtensionImageType | `
  Get-AzureRmVMExtensionImage | `
  Select Type, Version | `
  Format-Table -Property * -AutoSize 
```

## <a name="api-versions"></a>API 版本 

Azure 堆栈开发工具包中的虚拟机功能支持以下 API 版本：

![VM 资源类型](media/azure-stack-vm-considerations/vm-resoource-types.png)

以下 PowerShell 脚本可用于获取 Azure 堆栈环境中可用的虚拟机功能的 API 版本：

```powershell 
Get-AzureRmResourceProvider | `
  Select ProviderNamespace -Expand ResourceTypes | `
  Select * -Expand ApiVersions | `
  Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} | `
  where-Object {$_.ProviderNamespace -like “Microsoft.compute”}
```
如果云操作员更新到较新版本的 Azure 堆栈环境，则可能会有所不同的支持的资源类型和 API 版本列表。

## <a name="next-steps"></a>后续步骤

[使用 Azure 堆栈中的 PowerShell 创建 Windows 虚拟机](azure-stack-quick-create-vm-windows-powershell.md)
