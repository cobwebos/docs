---
title: Azure 快速入门 - 使用资源管理器模板备份 VM
description: 了解如何使用 Azure 资源管理器模板备份虚拟机
author: dcurwin
manager: carmonm
ms.service: backup
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 05/14/2019
ms.author: dacurwin
ms.custom: mvc
ms.openlocfilehash: 5f027926a7b90ac99b04456ad3635466a6e27655
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/27/2019
ms.locfileid: "72968793"
---
# <a name="back-up-a-virtual-machine-in-azure-with-resource-manager-template"></a>使用资源管理器模板在 Azure 中备份虚拟机

[Azure 备份](backup-overview.md)可备份本地计算机和应用以及 Azure VM。 本文介绍如何使用资源管理器模板和 Azure PowerShell 备份 Azure VM。 本快速入门重点介绍部署资源管理器模板以创建恢复服务保管库的过程。 有关开发资源管理器模板的详细信息，请参阅[资源管理器文档](/azure/azure-resource-manager/)和[模板参考](/azure/templates/microsoft.recoveryservices/allversions)。

或者，可以使用 [Azure PowerShell](./quick-backup-vm-powershell.md)、[Azure CLI](quick-backup-vm-cli.md) 或在 [Azure 门户](quick-backup-vm-portal.md)中备份 VM。

## <a name="create-a-vm-and-recovery-services-vault"></a>创建 VM 和恢复服务保管库

[恢复服务保管库](backup-azure-recovery-services-vault-overview.md)是一个逻辑容器，用于存储受保护资源（例如 Azure VM）的备份数据。 运行备份作业时，该作业会在恢复服务保管库中创建一个恢复点。 然后，可以使用其中一个恢复点将数据还原到给定的时间点。

本快速入门中使用的模板来自 [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/)。 此模板允许你部署使用 DefaultPolicy for Protection 配置的简单 Windows VM 和恢复服务保管库。

若要部署模板，请选择“试一试”，打开 Azure Cloud shell，然后将以下 PowerShell 脚本粘贴到 shell 窗口中  。 若要粘贴代码，请右键单击 shell 窗口并选择“粘贴”  。

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name (limited to eight characters) that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the administrator username for the virtual machine"
$adminPassword = Read-Host -Prompt "Enter the administrator password for the virtual machine" -AsSecureString
$dnsPrefix = Read-Host -Prompt "Enter the unique DNS Name for the Public IP used to access the virtual machine"

$resourceGroupName = "${projectName}rg"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-recovery-services-create-vm-and-configure-backup/azuredeploy.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName -adminUsername $adminUsername -adminPassword $adminPassword -dnsLabelPrefix $dnsPrefix
```

在本快速入门中，使用 Azure PowerShell 来部署资源管理器模板。 [Azure 门户](../azure-resource-manager/resource-group-template-deploy-portal.md)、[Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md) 和 [Rest API](../azure-resource-manager/resource-group-template-deploy-rest.md) 也可用于部署模板。

## <a name="start-a-backup-job"></a>启动备份作业

模板创建一个 VM，并在该 VM 上启用备份。 部署模板后，需要启动备份作业。 有关详细信息，请参阅[启动备份作业](./quick-backup-vm-powershell.md#start-a-backup-job)。

## <a name="monitor-the-backup-job"></a>监视备份作业

若要监视备份作业，请参阅[监视备份作业](./quick-backup-vm-powershell.md#monitor-the-backup-job)。

## <a name="clean-up-the-deployment"></a>清理部署

如果不再需要备份 VM，则可以清理它。

- 如果要尝试还原 VM，请跳过清理。
- 如果使用了现有的 VM，可以跳过最后一个 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) cmdlet，以保留资源组和 VM。

禁用保护，删除还原点和保管库。 然后删除资源组和关联 VM 资源，如下所示：

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $item -RemoveRecoveryPoints
$vault = Get-AzRecoveryServicesVault -Name "myRecoveryServicesVault"
Remove-AzRecoveryServicesVault -Vault $vault
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何创建恢复服务保管库，如何在 VM 上启用保护，以及如何创建初始恢复点。

- [了解如何](tutorial-backup-vm-at-scale.md)在 Azure 门户中备份 VM。
- [了解如何](tutorial-restore-disk.md)快速还原 VM
