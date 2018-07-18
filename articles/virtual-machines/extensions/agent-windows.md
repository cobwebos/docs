---
title: Azure 虚拟机代理概述 | Microsoft 文档
description: Azure 虚拟机代理概述
services: virtual-machines-windows
documentationcenter: virtual-machines
author: danielsollondon
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 0a1f212e-053e-4a39-9910-8d622959f594
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/30/2018
ms.author: danis
ms.openlocfilehash: 51271a875d19d08d84dcc102c85c947c05599d85
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/06/2018
ms.locfileid: "37868511"
---
# <a name="azure-virtual-machine-agent-overview"></a>Azure 虚拟机代理概述
Microsoft Azure 虚拟机代理（VM 代理）是受保护的轻型进程，用于管理虚拟机 (VM) 与 Azure 结构控制器的交互。 VM 代理有一个主要角色，目的是启用和执行 Azure 虚拟机扩展。 VM 扩展可用于对 VM 进行部署后配置，例如安装和配置软件。 VM 扩展还可启用恢复功能，例如重置 VM 的管理密码。 没有 Azure VM 代理，VM 扩展将无法运行。

本文详细介绍如何安装、检测和删除 Azure 虚拟机代理。

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
可以使用 Windows 安装程序包手动安装 Windows VM 代理。 创建部署到 Azure 的自定义 VM 映像时，可能需要手动安装。 若要手动安装 Windows VM 代理，[下载 VM 代理安装程序](http://go.microsoft.com/fwlink/?LinkID=394789)。

双击 Windows 安装程序文件即可安装 VM 代理。 若要以自动或无人参与方式安装 VM 代理，请运行以下命令：

```cmd
msiexec.exe /i WindowsAzureVmAgent.2.7.1198.778.rd_art_stable.160617-1120.fre /quiet
```

## <a name="detect-the-vm-agent"></a>检测 VM 代理

### <a name="powershell"></a>PowerShell

可以使用 Azure 资源管理器 PowerShell 模块检索有关 Azure VM 的信息。 若要查看有关 VM 的信息，如 Azure VM 代理的预配状态，请使用 [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm)：

```powershell` Get-AzureRmVM
```

The following condensed example output shows the *ProvisionVMAgent* property nested inside *OSProfile*. This property can be used to determine if the VM agent has been deployed to the VM:

```PowerShell
OSProfile                  :
  ComputerName             : myVM
  AdminUsername            : myUserName
  WindowsConfiguration     :
    ProvisionVMAgent       : True
    EnableAutomaticUpdates : True
```

可以使用以下脚本返回一个简明列表，其中包含 VM 名称以及 VM 代理的状态：

```PowerShell
$vms = Get-AzureRmVM

foreach ($vm in $vms) {
    $agent = $vm | Select -ExpandProperty OSProfile | Select -ExpandProperty Windowsconfiguration | Select ProvisionVMAgent
    Write-Host $vm.Name $agent.ProvisionVMAgent
}
```

### <a name="manual-detection"></a>手动检测
登录到 Microsoft Azure VM 后，可以使用任务管理器检查正在运行的进程。 要查看 Azure VM 代理，请打开任务管理器 > 单击“详细信息”选项卡，并查找名为 WindowsAzureGuestAgent.exe 的进程。 存在该进程表示 VM 代理已安装。


## <a name="upgrade-the-vm-agent"></a>升级 VM 代理
适用于 Windows 的 Azure VM 代理会自动升级。 新 VM 部署到 Azure 后，会在 VM 预配时获得最新 VM 代理。 应手动更新自定义 VM 映像，以便在创建映像时添加新的 VM 代理。


## <a name="next-steps"></a>后续步骤
有关 VM 扩展的详细信息，请参阅 [Azure 虚拟机扩展和功能概述](overview.md)。