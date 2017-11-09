---
title: "在 VM 上安装 Trend Micro Deep Security | Microsoft Docs"
description: "本文介绍如何在 Azure 中使用经典部署模型创建的 VM 上安装和配置 Trend Micro Deep Security。"
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: e991b635-f1e2-483f-b7ca-9d53e7c22e2a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: iainfou
ms.openlocfilehash: 911b8f12472dcbda3e6bfeb8c97bf1d04a63e1dd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-install-and-configure-trend-micro-deep-security-as-a-service-on-a-windows-vm"></a>如何在 Windows VM 上安装和配置 Trend Micro Deep Security 即服务
> [!IMPORTANT]
> Azure 提供两个不同的部署模型用于创建和处理资源：[Resource Manager 和经典模型](../../../resource-manager-deployment-model.md)。 本文介绍如何使用经典部署模型。 Microsoft 建议大多数新部署使用 Resource Manager 模型。

本文演示了如何在运行 Windows Server 的新的或现有虚拟机 (VM) 上安装和配置 Trend Micro Deep Security 即服务。 Deep Security 即服务包括反恶意软件保护、防火墙、入侵防御系统和完整性监视。

该客户端通过 VM 代理作为安全扩展插件进行安装。 在新虚拟机上安装 Deep Security Agent，因为 VM 代理由 Azure 门户自动创建。

使用经典门户 Azure CLI 或 PowerShell 创建的现有 VM 可能不具有 VM 代理。 对于未安装 VM 代理的现有虚拟机，需要先下载并安装 VM 代理。 本文介绍这两种情况。

如果已经有针对本地解决方案的 Trend Micro 当前订阅，则可用它来帮助保护 Azure 虚拟机。 如果还不是客户，则可注册试用订阅。 有关此解决方案的详细信息，请参阅 Trend Micro 博客文章[用于 Deep Security 的 Microsoft Azure VM 代理扩展](http://go.microsoft.com/fwlink/p/?LinkId=403945)。

## <a name="install-the-deep-security-agent-on-a-new-vm"></a>在新 VM 上安装 Deep Security Agent

使用**应用商店**中的映像创建虚拟机时，[Azure 门户](http://portal.azure.com)允许安装 Trend Micro 安全扩展。 如果要创建的是单个虚拟机，则可通过此门户轻松地添加来自 Trend Micro 的保护。

使用**应用商店**中的某个条目会打开一个向导，帮助用户设置虚拟机。 使用该向导的第三个面板“设置”边栏选项卡安装 Trend Micro 安全扩展。  有关常规说明，请参阅[在 Azure 门户中创建运行 Windows 的虚拟机](tutorial.md)。

进入向导的“设置”边栏选项卡时，请执行以下步骤：

1. 单击“扩展”，并在下一个窗格中单击“添加扩展”。

   ![开始添加扩展][1]

2. 在“新建资源”窗格中选择“Deep Security Agent”。 在 Deep Security Agent 窗格中，单击“创建”。

   ![标识 Deep Security Agent][2]

3. 为扩展输入“租户标识符”和“租户激活密码”。 （可选）可输入“安全策略标识符”。 然后，单击“确定”添加客户端。

   ![提供扩展详细信息][3]

## <a name="install-the-deep-security-agent-on-an-existing-vm"></a>在现有 VM 上安装 Deep Security Agent
若要在现有 VM 上安装代理，需要以下各项：

* 在本地计算机上安装 Azure PowerShell 模块 0.8.2 版或更高版本。 可以使用 **Get-Module azure | format-table version** 命令查看已安装的 Azure PowerShell 的版本。 有关说明以及指向最新版本的链接，请参阅[如何安装和配置 Azure PowerShell](/powershell/azure/overview)。 使用 `Add-AzureAccount` 登录到 Azure 订阅。
* 在目标虚拟机上安装 VM 代理。

首先，请验证是否已安装 VM 代理。 填写云服务名称和虚拟机名称，并在管理员级别的 Azure PowerShell 命令提示符下运行以下命令。 替换引号内的所有内容，包括 < and > 字符。

    $CSName = "<cloud service name>"
    $VMName = "<virtual machine name>"
    $vm = Get-AzureVM -ServiceName $CSName -Name $VMName
    write-host $vm.VM.ProvisionGuestAgent

如果不知道云服务名称和虚拟机名称，运行 **Get-AzureVM** 即可显示当前订阅中所有虚拟机的该信息。

如果 **write-host** 命令返回 **True**，则已安装 VM 代理。 如果该命令返回 **False**，请参阅 Azure 博客文章 [VM 代理和扩展 - 第 2 部分](http://go.microsoft.com/fwlink/p/?LinkId=403947)中的说明和下载链接。

如果已安装 VM 代理，请运行以下命令。

    $Agent = Get-AzureVMAvailableExtension TrendMicro.DeepSecurity -ExtensionName TrendMicroDSA

    Set-AzureVMExtension -Publisher TrendMicro.DeepSecurity –Version $Agent.Version -ExtensionName TrendMicroDSA -VM $vm | Update-AzureVM

## <a name="next-steps"></a>后续步骤
安装后，代理需要几分钟时间才会开始运行。 之后需在虚拟机上激活 Deep Security，才能通过 Deep Security Manager 进行管理。 有关其他说明，请参阅以下文章：

* 有关此解决方案的 Trend 文章：[用于 Microsoft Azure 的 Instant-On Cloud Security](http://go.microsoft.com/fwlink/?LinkId=404101)
* 用于配置虚拟机的 [Windows PowerShell 脚本示例](http://go.microsoft.com/fwlink/?LinkId=404100)
* 示例的[说明](http://go.microsoft.com/fwlink/?LinkId=404099)

## <a name="additional-resources"></a>其他资源
[如何登录到运行 Windows Server 的虚拟机]

[Azure VM 扩展和功能]

<!-- Image references -->
[1]: ./media/install-trend/new_vm_Blade3.png
[2]: ./media/install-trend/find_SecurityAgent.png
[3]: ./media/install-trend/SecurityAgentDetails.png

<!-- Link references -->
[如何登录到运行 Windows Server 的虚拟机]:connect-logon.md
[Azure VM 扩展和功能]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409
