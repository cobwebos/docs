---
title: "在 VM 上安装 Trend Micro Deep Security |Microsoft 文档"
description: "本文介绍如何安装和配置 Trend Micro security 使用经典部署模型在 Azure 中创建的 VM 上。"
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
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-install-and-configure-trend-micro-deep-security-as-a-service-on-a-windows-vm"></a>如何安装和配置 Trend Micro Deep Security 即 Windows VM 上服务
> [!IMPORTANT]
> Azure 具有用于创建和使用资源的两个不同的部署模型：[资源管理器和经典](../../../resource-manager-deployment-model.md)。 本文介绍如何使用经典部署模型。 Microsoft 建议大多数新部署使用资源管理器模型。

这篇文章演示了如何安装和配置 Trend Micro Deep Security 即新的或现有虚拟机 (VM) 上运行 Windows Server 服务。 Deep Security 即服务包括反恶意软件防护、 防火墙、 入侵防御系统和完整性监视。

作为安全扩展通过 VM 代理安装客户端。 在新的虚拟机上安装 Deep Security Agent，因为 VM 代理 Azure 门户由自动创建。

使用经典门户中，Azure CLI 或 PowerShell 创建的现有 VM 可能没有 VM 代理。 对于现有虚拟机没有 VM 代理，你需要下载并请先安装它。 本文介绍这两种情况。

如果你有针对本地解决方案的 Trend Micro 当前订阅，可用于帮助保护你的 Azure 虚拟机。 如果你不客户，你可以注册一个试用订阅。 有关此解决方案的详细信息，请参阅 Trend Micro 博客文章[Microsoft Azure VM 代理用于 Deep Security 扩展](http://go.microsoft.com/fwlink/p/?LinkId=403945)。

## <a name="install-the-deep-security-agent-on-a-new-vm"></a>在新的 VM 上安装 Deep Security Agent

[Azure 门户](http://portal.azure.com)可让你在使用中的映像时安装 Trend Micro 安全扩展插件**Marketplace**创建虚拟机。 如果你要创建单个虚拟机，使用门户可轻松添加来自 Trend Micro 的保护。

使用从某个条目**Marketplace**打开一个向导，可帮助你设置虚拟机。 你使用**设置**边栏选项卡，此向导，以安装 Trend Micro 安全扩展插件的第三个面板。  有关常规说明，请参阅[创建在 Azure 门户中运行 Windows 的虚拟机](tutorial.md)。

当你到达**设置**边栏选项卡中的向导中，执行以下步骤：

1. 单击**扩展**，然后单击**将扩展添加**中下一个窗格。

   ![启动添加扩展][1]

2. 选择**Deep Security Agent**中**新资源**窗格。 在 Deep Security Agent 窗格中，单击**创建**。

   ![标识 Deep Security 代理][2]

3. 输入**租户标识符**和**租户激活密码**扩展。 （可选） 你可以输入**安全策略标识符**。 然后，单击**确定**添加客户端。

   ![提供扩展的详细信息][3]

## <a name="install-the-deep-security-agent-on-an-existing-vm"></a>在现有 VM 上安装 Deep Security Agent
若要在现有 VM 上安装代理，你需要以下各项：

* Azure PowerShell 模块、 0.8.2 版或更高版本，本地计算机上已安装。 你可以检查已通过安装 Azure PowerShell 的版本**Get-module azure | 格式表版本**命令。 有关说明以及指向最新版本的链接，请参阅[如何安装和配置 Azure PowerShell](/powershell/azure/overview)。 登录到你的 Azure 订阅使用`Add-AzureAccount`。
* 目标虚拟机上安装 VM 代理。

首先，验证已安装 VM 代理。 填写云服务名称和虚拟机名称，然后在管理员级别的 Azure PowerShell 命令提示符运行以下命令。 将引号，包括内的所有内容 < 和 > 字符。

    $CSName = "<cloud service name>"
    $VMName = "<virtual machine name>"
    $vm = Get-AzureVM -ServiceName $CSName -Name $VMName
    write-host $vm.VM.ProvisionGuestAgent

如果你不知道云服务和虚拟机名称，运行**Get AzureVM**以显示当前订阅中的所有虚拟机的该信息。

如果**写入主机**命令返回**True**，已安装 VM 代理。 如果它返回**False**，请参阅说明以及指向 Azure 博客文章中下载[VM 代理和扩展-第 2 部分](http://go.microsoft.com/fwlink/p/?LinkId=403947)。

如果安装 VM 代理后，运行以下命令。

    $Agent = Get-AzureVMAvailableExtension TrendMicro.DeepSecurity -ExtensionName TrendMicroDSA

    Set-AzureVMExtension -Publisher TrendMicro.DeepSecurity –Version $Agent.Version -ExtensionName TrendMicroDSA -VM $vm | Update-AzureVM

## <a name="next-steps"></a>后续步骤
它需要代理以开始安装时运行的几分钟时间。 之后，你需要在虚拟机上激活 Deep Security，以便它可以管理通过 Deep Security Manager。 请参阅以下文章了解更多说明：

* 有关此解决方案的 trend 文章[Microsoft azure 的 Instant-On Cloud Security](http://go.microsoft.com/fwlink/?LinkId=404101)
* A[示例 Windows PowerShell 脚本](http://go.microsoft.com/fwlink/?LinkId=404100)配置虚拟机
* [说明](http://go.microsoft.com/fwlink/?LinkId=404099)的示例

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
