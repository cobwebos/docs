---
title: 在 Azure 中使用模板创建 Windows VM | Microsoft 文档
description: 将 Resource Manager 模板与 PowerShell 配合使用，轻松创建新的 Windows VM。
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 19129d61-8c04-4aa9-a01f-361a09466805
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/22/2019
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 58c8ca9bb9b4dfd58c801eb4dd1dad95c1969c18
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "62127610"
---
# <a name="create-a-windows-virtual-machine-from-a-resource-manager-template"></a>通过 Resource Manager 模板创建 Windows 虚拟机

了解如何从 Azure Cloud shell 中使用 Azure 资源管理器模板和 Azure PowerShell 创建 Windows 虚拟机。 本文中使用的模板会在包含单个子网的新虚拟网络上部署运行 Windows Server 的单个虚拟机。 若要创建 Linux 虚拟机，请参阅[如何使用 Azure 资源管理器模板创建 Linux 虚拟机](../linux/create-ssh-secured-vm-from-template.md)。

## <a name="create-a-virtual-machine"></a>创建虚拟机

创建 Azure 虚拟机通常包括两个步骤：

- 创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 必须在创建虚拟机前创建资源组。
- 创建虚拟机。

以下示例通过 [Azure 快速入门模板](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json)创建 VM。 下面是该模板的副本：

[!code-json[create-windows-vm](~/quickstart-templates/101-vm-simple-windows/azuredeploy.json)]

若要运行 PowerShell 脚本，请选择**试试**打开 Azure Cloud shell。 若要粘贴脚本，请右键单击 shell，然后选择“粘贴”：

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the administrator username"
$adminPassword = Read-Host -Prompt "Enter the administrator password" -AsSecureString
$dnsLabelPrefix = Read-Host -Prompt "Enter an unique DNS name for the public IP"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json" `
    -adminUsername $adminUsername `
    -adminPassword $adminPassword `
    -dnsLabelPrefix $dnsLabelPrefix

 (Get-AzVm -ResourceGroupName $resourceGroupName).name

```

如果您选择安装并使用从 Azure Cloud shell 中而不是本地 PowerShell，本教程需要 Azure PowerShell 模块。 运行 `Get-Module -ListAvailable Az` 即可查找版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)。 如果在本地运行 PowerShell，则还需运行 `Connect-AzAccount` 以创建与 Azure 的连接。

在前面的示例中，指定了 GitHub 中存储的一个模板。 还可以下载或创建模板并使用 `--template-file` 参数指定本地路径。

下面是一些其他资源：

- 若要了解如何开发资源管理器模板，请参阅 [Azure 资源管理器文档](/azure/azure-resource-manager/)。
- 若要查看 Azure 虚拟机架构，请参阅[Azure 模板引用](/azure/templates/microsoft.compute/allversions)。
- 若要查看更多的虚拟机模板示例，请参阅 [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular)。

## <a name="connect-to-the-virtual-machine"></a>连接到虚拟机

上一脚本中的最后一个 PowerShell 命令显示虚拟机名称。 若要连接到虚拟机，请参阅[如何连接并登录运行 Windows 的 Azure 虚拟机](./connect-logon.md)。

## <a name="next-steps"></a>后续步骤

- 如果部署出现问题，可以参阅[排查使用 Azure 资源管理器时的常见 Azure 部署错误](../../resource-manager-common-deployment-errors.md)。
- 通过查看[使用 Azure PowerShell 模块创建和管理 Windows VM](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)，了解如何创建和管理虚拟机。

若要了解有关创建模板的更多信息，请查看所部署的资源类型的 JSON 语法和属性：

- [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
- [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
- [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
- [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines)
