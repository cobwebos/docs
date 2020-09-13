---
title: Azure 虚拟机代理概述
description: Azure 虚拟机代理概述
services: virtual-machines-windows
documentationcenter: virtual-machines
author: mimckitt
manager: gwallace
tags: azure-resource-manager
ms.assetid: 0a1f212e-053e-4a39-9910-8d622959f594
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/20/2019
ms.author: akjosh
ms.openlocfilehash: d9939b706eb63e5681ddef438cde92f32786f889
ms.sourcegitcommit: f845ca2f4b626ef9db73b88ca71279ac80538559
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89612831"
---
# <a name="azure-virtual-machine-agent-overview"></a>Azure 虚拟机代理概述
Microsoft Azure 虚拟机代理（VM 代理）是受保护的轻型进程，用于管理虚拟机 (VM) 与 Azure 结构控制器的交互。 VM 代理有一个主要角色，目的是启用和执行 Azure 虚拟机扩展。 VM 扩展可用于对 VM 进行部署后配置，例如安装和配置软件。 VM 扩展还可启用恢复功能，例如重置 VM 的管理密码。 没有 Azure VM 代理，VM 扩展将无法运行。

本文详细介绍如何安装和检测 Azure 虚拟机代理。

## <a name="install-the-vm-agent"></a>安装 VM 代理

### <a name="azure-marketplace-image"></a>Azure 市场映像

Azure VM 代理默认安装在任何通过 Azure 市场映像部署的 Windows VM 上。 通过门户、PowerShell、命令行接口或 Azure 资源管理器模板部署 Azure 市场映像时，也会安装 Azure VM 代理。

Windows 来宾代理包分为两个部分：

- 预配代理 (PA)
- Windows 来宾代理 (WinGA)

若要启动 VM，VM 上必须已安装 PA，但无需安装 WinGA。 部署 VM 时，可以选择不安装 WinGA。 下面的示例演示如何选择 Azure 资源管理器模板的 provisionVmAgent 选项：

```json
"resources": [{
"name": "[parameters('virtualMachineName')]",
"type": "Microsoft.Compute/virtualMachines",
"apiVersion": "2016-04-30-preview",
"location": "[parameters('location')]",
"dependsOn": ["[concat('Microsoft.Network/networkInterfaces/', parameters('networkInterfaceName'))]"],
"properties": {
    "osProfile": {
    "computerName": "[parameters('virtualMachineName')]",
    "adminUsername": "[parameters('adminUsername')]",
    "adminPassword": "[parameters('adminPassword')]",
    "windowsConfiguration": {
        "provisionVmAgent": "false"
}
```

如果没有安装代理，则无法使用某些 Azure 服务，例如 Azure 备份或 Azure 安全。 这些服务需要安装扩展。 如果已在没有 WinGA VM 的情况下部署 VM，稍后可以安装最新版本的代理。

### <a name="manual-installation"></a>手动安装
可以使用 Windows 安装程序包手动安装 Windows VM 代理。 创建部署到 Azure 的自定义 VM 映像时，可能需要手动安装。 若要手动安装 Windows VM 代理，[下载 VM 代理安装程序](https://go.microsoft.com/fwlink/?LinkID=394789)。 VM 代理在 Windows Server 2008 (64 bit) 和更高版本上受支持。

> [!NOTE]
> 未启用 ProvisionVMAgent 的情况下，在从映像部署的 VM 上手动安装 VMAgent 之后，请务必更新 AllowExtensionOperations 选项。

```powershell
$vm.OSProfile.AllowExtensionOperations = $true
$vm | Update-AzVM
```

### <a name="prerequisites"></a>先决条件

- 若要运行 .Net Framework 4.0，Windows VM 代理至少需要 Windows Server 2008 SP2 (64 位) 才能运行。 请参阅 [Azure 中的虚拟机代理的最低版本支持](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)

- 确保 VM 可以访问 IP 地址 168.63.129.16。 有关详细信息，请参阅[什么是 IP 地址 168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md)。

- 确保在来宾 VM 内启用 DHCP。 必须这样做，才能从 DHCP 获取主机或构造地址，以便 IaaS VM 代理和扩展可以正常工作。 如果需要静态专用 IP，则应通过 Azure 门户或 PowerShell 进行配置，同时确保启用 VM 内的 DHCP 选项。 [详细了解](https://docs.microsoft.com/azure/virtual-network/virtual-networks-static-private-ip-arm-ps#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface)如何通过 PowerShell 设置静态 IP 地址。


## <a name="detect-the-vm-agent"></a>检测 VM 代理

### <a name="powershell"></a>PowerShell

可以使用 Azure 资源管理器 PowerShell 模块检索有关 Azure VM 的信息。 若要查看有关 VM 的信息，如 Azure VM 代理的预配状态，请使用 [Get-AzVM](/powershell/module/az.compute/get-azvm)：

```powershell
Get-AzVM
```

以下浓缩版示例输出演示了嵌套在 *OSProfile* 中的 *ProvisionVMAgent* 属性。 此属性可以用来确定 VM 代理是否已部署到 VM：

```powershell
OSProfile                  :
  ComputerName             : myVM
  AdminUsername            : myUserName
  WindowsConfiguration     :
    ProvisionVMAgent       : True
    EnableAutomaticUpdates : True
```

可以使用以下脚本返回一个简明列表，其中包含 VM 名称以及 VM 代理的状态：

```powershell
$vms = Get-AzVM

foreach ($vm in $vms) {
    $agent = $vm | Select -ExpandProperty OSProfile | Select -ExpandProperty Windowsconfiguration | Select ProvisionVMAgent
    Write-Host $vm.Name $agent.ProvisionVMAgent
}
```

### <a name="manual-detection"></a>手动检测

登录到 Windows VM 后，可以使用任务管理器检查正在运行的进程。 要查看 Azure VM 代理，请打开任务管理器 > 单击“详细信息”选项卡，并查找名为 WindowsAzureGuestAgent.exe 的进程。 存在该进程表示 VM 代理已安装。


## <a name="upgrade-the-vm-agent"></a>升级 VM 代理
适用于 Windows 的 Azure VM 代理会在从 Azure 市场部署的映像上自动升级。 新 VM 部署到 Azure 后，会在 VM 预配时获得最新 VM 代理。 如果已手动安装了代理或正在部署自定义 VM 映像，则需要在创建映像时进行手动更新以包括新的 VM 代理。

## <a name="windows-guest-agent-automatic-logs-collection"></a>Windows 来宾代理自动日志收集
Windows 来宾代理具有自动收集一些日志的功能。 此功能由 CollectGuestLogs.exe 进程控制。 它同时适用于 PaaS 云服务和 IaaS 虚拟机，其目标是快速自动地从 VM 收集一些诊断日志 - 以便将它们用于脱机分析。 收集的日志包括事件日志、OS 日志、Azure 日志和一些注册表项。 它会生成一个压缩文件，该文件传输到 VM 的主机。 然后，工程团队和支持专业人员可以查看此 ZIP 文件，以根据拥有 VM 的客户的请求调查问题。

## <a name="next-steps"></a>后续步骤
有关 VM 扩展的详细信息，请参阅 [Azure 虚拟机扩展和功能概述](overview.md)。
