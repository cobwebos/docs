---
title: 使用 Azure 自动化将服务器配置为所需状态并管理偏移
description: 教程 - 使用 Azure 自动化状态配置管理服务器配置
services: automation
ms.service: automation
ms.component: dsc
author: DCtheGeek
ms.author: dacoulte
manager: carmonm
ms.topic: conceptual
ms.date: 08/08/2018
ms.openlocfilehash: 3b4ecc7596af52312785ea7acaad18a7af8a5087
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2018
ms.locfileid: "40005951"
---
# <a name="configure-servers-to-a-desired-state-and-manage-drift"></a>将服务器配置到所需状态并管理偏移

使用 Azure 自动化状态配置可以指定服务器配置，并确保这些服务器在一段时间后处于指定状态。

> [!div class="checklist"]
> - 登记要由 Azure Automation DSC 管理的 VM
> - 将配置上传到 Azure 自动化
> - 将配置编译为节点配置
> - 将节点配置分配给托管节点
> - 检查托管节点的符合性状态

## <a name="prerequisites"></a>先决条件

要完成本教程，需要：

- 一个 Azure 自动化帐户。 有关如何创建 Azure 自动化运行方式帐户的说明，请参阅 [Azure 运行方式帐户](automation-sec-configure-azure-runas-account.md)。
- 一个运行 Windows Server 2008 R2 或更高版本的 Azure 资源管理器 VM（非经典）。 如需创建 VM 的说明，请参阅[在 Azure 门户中创建第一个 Windows 虚拟机](../virtual-machines/virtual-machines-windows-hero-tutorial.md)
- Azure PowerShell 模块 3.6 版或更高版本。 运行 `Get-Module -ListAvailable AzureRM` 即可查找版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。
- 熟悉所需状态配置 (DSC)。 有关 DSC 文档的信息，请参阅 [Windows PowerShell 所需状态配置概述](https://docs.microsoft.com/powershell/dsc/overview)

## <a name="log-in-to-azure"></a>登录 Azure

使用 `Connect-AzureRmAccount` 命令登录到 Azure 订阅，并按照屏幕上的说明进行操作。

```powershell
Connect-AzureRmAccount
```

## <a name="create-and-upload-a-configuration-to-azure-automation"></a>创建配置并将配置上传到 Azure 自动化

对于本教程，我们将使用简单 DSC 配置，以确保在 VM 上安装 IIS。

有关 DSC 配置的详细信息，请参阅 [DSC 配置](/powershell/dsc/configurations)。

在文本编辑器中，键入以下命令，并在本地将其保存为 `TestConfig.ps1`。

```powershell
configuration TestConfig {
   Node WebServer {
      WindowsFeature IIS {
         Ensure               = 'Present'
         Name                 = 'Web-Server'
         IncludeAllSubFeature = $true
      }
   }
}
```

调用 `Import-AzureRmAutomationDscConfiguration` cmdlet，将配置上传到自动化帐户：

```powershell
 Import-AzureRmAutomationDscConfiguration -SourcePath 'C:\DscConfigs\TestConfig.ps1' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Published
```

## <a name="compile-a-configuration-into-a-node-configuration"></a>将配置编译为节点配置

必须先将 DSC 配置编译为节点配置，然后才能将它分配给节点。

有关编译配置的信息，请参阅 [DSC 配置](/powershell/dsc/configurations)。

调用 `Start-AzureRmAutomationDscCompilationJob` cmdlet，将 `TestConfig` 配置编译为节点配置：

```powershell
Start-AzureRmAutomationDscCompilationJob -ConfigurationName 'TestConfig' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount'
```

这将在自动化帐户中创建一个名为 `TestConfig.WebServer` 的节点配置。

## <a name="register-a-vm-to-be-managed-by-state-configuration"></a>注册要由状态配置管理的 VM

可以使用 Azure 自动化状态配置来管理 Azure VM（包括经典 VM 和资源管理器 VM）、本地 VM、Linux 计算机、AWS VM，以及本地物理机。 在本主题中，我们介绍如何仅注册 Azure 资源管理器 VM。 有关注册其他类型的计算机的信息，请参阅[登记由 Azure 自动化状态配置管理的计算机](automation-dsc-onboarding.md)。

调用 `Register-AzureRmAutomationDscNode` cmdlet 来将 VM 注册到 Azure 自动化状态配置中。

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm'
```

这会将指定的 VM 注册为状态配置中的托管节点。

### <a name="specify-configuration-mode-settings"></a>指定配置模式设置

将 VM 注册为托管节点时，还可以指定配置的属性。 例如，可以通过将 `ApplyOnly` 指定为 **ConfigurationMode** 属性的值来指定仅应用计算机的状态一次（DSC 不会尝试在初始检查后应用配置）：

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationMode 'ApplyOnly'
```

还可以使用 **ConfigurationModeFrequencyMins** 属性指定 DSC 检查配置状态的频率：

```powershell
# Run a DSC check every 60 minutes
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationModeFrequencyMins 60
```

有关设置托管节点的配置属性的详细信息，请参阅 [Register-AzureRmAutomationDscNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode)。

有关 DSC 配置设置的详细信息，请参阅[配置本地配置管理器](/powershell/dsc/metaconfig)。

## <a name="assign-a-node-configuration-to-a-managed-node"></a>将节点配置分配给托管节点

现在我们可以将已编译的节点配置分配给我们想要配置的 VM。

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Assign the node configuration to the DSC node
Set-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeConfigurationName 'TestConfig.WebServer' -Id $node.Id
```

这会将名为 `TestConfig.WebServer` 的节点配置分配到名为 `DscVm` 的已注册 DSC 节点。
默认情况下，每隔 30 分钟会检查一次 DSC 节点是否符合节点配置。
有关如何更改符合性检查间隔的信息，请参阅[配置本地配置管理器](/PowerShell/DSC/metaConfig)。

## <a name="check-the-compliance-status-of-a-managed-node"></a>检查托管节点的符合性状态

可以通过调用 `Get-AzureRmAutomationDscNodeReport` cmdlet 来获取有关托管节点符合性状态的报告：

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Get an array of status reports for the DSC node
$reports = Get-AzureRmAutomationDscNodeReport -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Id $node.Id

# Display the most recent report
$reports[0]
```

## <a name="next-steps"></a>后续步骤

- 若要开始使用，请参阅 [Azure 自动化状态配置入门](automation-dsc-getting-started.md)
- 要了解如何登记节点，请参阅[登记由 Azure 自动化状态配置管理的计算机](automation-dsc-onboarding.md)
- 若要了解如何编译 DSC 配置，以便将它们分配给目标节点，请参阅[在 Azure 自动化状态配置中编译配置](automation-dsc-compile.md)
- 有关 PowerShell cmdlet 参考，请参阅 [Azure 自动化状态配置 cmdlet](/powershell/module/azurerm.automation/#automation)
- 有关定价信息，请参阅 [Azure 自动化状态配置定价](https://azure.microsoft.com/pricing/details/automation/)
- 若要查看在持续部署管道中使用 Azure 自动化状态配置的示例，请参阅[使用 Azure 自动化状态配置和 Chocolatey 进行持续部署](automation-dsc-cd-chocolatey.md)