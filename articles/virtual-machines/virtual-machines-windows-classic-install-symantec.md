---
title: "在 Azure 中的 Windows VM 上安装 Symantec Endpoint Protection | Microsoft Docs"
description: "了解如何在使用经典部署模型创建的新的或现有的 Azure VM 上安装和配置 Symantec Endpoint Protection 安全扩展。"
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 19dcebc7-da6b-4510-907b-d64088e81fa2
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 829a4f6204b9addbe4f4db233a376b6d5c3b7f30
ms.lasthandoff: 12/08/2016


---

# <a name="how-to-install-and-configure-symantec-endpoint-protection-on-a-windows-vm"></a>如何在 Windows VM 上安装和配置 Symantec Endpoint Protection
> [!IMPORTANT] 
> Azure 提供两个不同的部署模型用于创建和处理资源：[Resource Manager 和经典模型](../azure-resource-manager/resource-manager-deployment-model.md)。 本文介绍如何使用经典部署模型。 Microsoft 建议大多数新部署使用资源管理器模型。

本文介绍如何在运行 Windows Server 的现有虚拟机 (VM) 上安装和配置 Symantec Endpoint Protection 客户端。 这是完整的客户端，其中包括病毒和间谍软件防护、防火墙和入侵防御等服务。 该客户端通过 VM 代理作为安全扩展插件进行安装。

如果已经有针对本地解决方案的 Symantec 现有订阅，则可用它来保护 Azure 虚拟机。 如果还不是客户，则可注册试用订阅。 有关此解决方案的详细信息，请参阅 [Microsoft Azure 平台上的 Symantec Endpoint Protection][Symantec]。 如果你已经是一名 Symantec 客户，则此页还包含指向安装客户端的许可信息和说明的链接。

## <a name="install-symantec-endpoint-protection-on-an-existing-vm"></a>在现有 VM 上安装 Symantec Endpoint Protection
在开始之前，需具备以下先决条件：

* 在工作计算机上安装 Azure PowerShell 模块 0.8.2 版或更高版本。 可以使用 **Get-Module azure | format-table version** 命令查看已安装的 Azure PowerShell 的版本。 有关说明以及指向最新版本的链接，请参阅[如何安装和配置 Azure PowerShell][PS]。 使用 `Add-AzureAccount` 登录到 Azure 订阅。
* 在 Azure 虚拟机上运行的 VM 代理。

首先，请验证虚拟机上是否已安装 VM 代理。 填写云服务名称和虚拟机名称，然后在管理员级别的 Azure PowerShell 命令提示符下运行以下命令。 替换引号内的所有内容，包括 < and > 字符。

> [!TIP]
> 如果不知道云服务名称和虚拟机名称，运行 **Get-AzureVM** 即可列出当前订阅中所有虚拟机的名称。

```powershell
$CSName = "<cloud service name>"
$VMName = "<virtual machine name>"
$vm = Get-AzureVM -ServiceName $CSName -Name $VMName
write-host $vm.VM.ProvisionGuestAgent
```

如果 **write-host** 命令显示 **True**，则已安装 VM 代理。 如果该命令显示 **False**，请参阅 Azure 博客文章 [VM 代理和扩展 - 第 2 部分][Agent]中的说明和下载链接。

如果已安装 VM 代理，则运行以下命令安装 Symantec Endpoint Protection 代理。

```powershell
$Agent = Get-AzureVMAvailableExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection

Set-AzureVMExtension -Publisher Symantec –Version $Agent.Version -ExtensionName SymantecEndpointProtection \
    -VM $vm | Update-AzureVM
```

若要验证 Symantec 安全扩展插件是否已安装并处于最新状态：

1. 登录到虚拟机。 有关说明，请参阅[如何登录到运行 Windows Server 的虚拟机][Logon]。
2. 对于 Windows Server 2008 R2，请单击“开始”>“Symantec Endpoint Protection”。 对于 Windows Server 2012 或 Windows Server 2012 R2，在开始屏幕键入“Symantec”，然后单击“Symantec Endpoint Protection”。
3. 在“状态 - Symantec Endpoint Protection”窗口的“状态”选项卡中，根据需要应用更新或重启。

## <a name="additional-resources"></a>其他资源
[如何登录到运行 Windows Server 的虚拟机][Logon]

[Azure VM 扩展和功能][Ext]

<!--Link references-->
[Symantec]: http://www.symantec.com/connect/blogs/symantec-endpoint-protection-now-microsoft-azure

[Portal]: http://manage.windowsazure.com

[Create]: virtual-machines-windows-classic-tutorial.md

[PS]: /powershell/azureps-cmdlets-docs

[Agent]: http://go.microsoft.com/fwlink/p/?LinkId=403947

[Logon]: virtual-machines-windows-classic-connect-logon.md

[Ext]: http://go.microsoft.com/fwlink/p/?linkid=390493

