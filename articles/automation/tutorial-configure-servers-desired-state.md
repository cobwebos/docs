---
title: 在 Azure 自动化中将计算机配置为所需状态
description: 本文介绍如何使用 Azure Automation State Configuration 将计算机配置为所需状态。
services: automation
ms.subservice: dsc
ms.topic: conceptual
ms.date: 08/08/2018
ms.openlocfilehash: 55c7522ad1dc6c7f91fae608a777dab3cd67d2ed
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86183164"
---
# <a name="configure-machines-to-a-desired-state"></a>将计算机配置为所需状态

使用 Azure 自动化状态配置可以指定服务器配置，并确保这些服务器在一段时间后处于指定状态。

> [!div class="checklist"]
> - 登记要由 Azure Automation DSC 管理的 VM
> - 将配置上传到 Azure 自动化
> - 将配置编译为节点配置
> - 将节点配置分配给托管节点
> - 检查托管节点的符合性状态

在本教程中，我们使用简单 [DSC 配置](/powershell/scripting/dsc/configurations/configurations)，以确保在 VM 上安装 IIS。

## <a name="prerequisites"></a>先决条件

- 一个 Azure 自动化帐户。 有关如何创建 Azure 自动化运行方式帐户的说明，请参阅 [Azure 运行方式帐户](./manage-runas-account.md)。
- 一个运行 Windows Server 2008 R2 或更高版本的 Azure 资源管理器 VM（非经典）。 如需创建 VM 的说明，请参阅[在 Azure 门户中创建第一个 Windows 虚拟机](../virtual-machines/windows/quick-create-portal.md)。
- Azure PowerShell 模块 3.6 版或更高版本。 运行 `Get-Module -ListAvailable Az` 即可查找版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/azurerm/install-azurerm-ps)。
- 熟悉所需状态配置 (DSC)。 有关 DSC 文档的信息，请参阅 [Windows PowerShell Desired State Configuration 概述](/powershell/scripting/dsc/overview/overview)。

## <a name="support-for-partial-configurations"></a>对部分配置的支持

Azure Automation State Configuration 支持使用[部分配置](/powershell/scripting/dsc/pull-server/partialconfigs)。 在这种情况下，DSC 配置为独立管理多个配置，并从 Azure 自动化检索每个配置。 但是，每个自动化帐户只能向节点分配一个配置。 这意味着，如果对一个节点使用两个配置，则需要两个自动化帐户。

有关如何从请求服务注册部分配置的详细信息，请参阅[部分配置](/powershell/scripting/dsc/pull-server/partialconfigs#partial-configurations-in-pull-mode)的文档。

如需深入了解团队如何使用配置即代码协作管理服务器，请参阅[了解 DSC 在 CI/CD 管道中的角色](/powershell/scripting/dsc/overview/authoringadvanced)。

## <a name="log-in-to-azure"></a>登录 Azure

使用 [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0) cmdlet 登录到 Azure 订阅，然后按屏幕说明操作。

```powershell
Connect-AzAccount
```

## <a name="create-and-upload-a-configuration-to-azure-automation"></a>创建配置并将配置上传到 Azure 自动化


在文本编辑器中键入以下内容，并在本地将文件保存为 TestConfig.ps1。

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

> [!NOTE]
> 在需要导入多个提供 DSC 资源的模块的更高级方案中，请确保每个模块在配置中具有唯一 `Import-DscResource` 行。

调用 [Import-AzAutomationDscConfiguration](/powershell/module/Az.Automation/Import-AzAutomationDscConfiguration?view=azps-3.7.0) cmdlet，将配置上传到自动化帐户。

```powershell
 Import-AzAutomationDscConfiguration -SourcePath 'C:\DscConfigs\TestConfig.ps1' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Published
```

## <a name="compile-a-configuration-into-a-node-configuration"></a>将配置编译为节点配置

必须先将 DSC 配置编译为节点配置，然后才能将它分配给节点。 参阅 [DSC 配置](/powershell/scripting/dsc/configurations/configurations)。

调用 [Start-AzAutomationDscCompilationJob](/powershell/module/Az.Automation/Start-AzAutomationDscCompilationJob?view=azps-3.7.0) cmdlet，将 `TestConfig` 配置编译为自动化帐户中名为 `TestConfig.WebServer` 的节点配置。

```powershell
Start-AzAutomationDscCompilationJob -ConfigurationName 'TestConfig' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount'
```

## <a name="register-a-vm-to-be-managed-by-state-configuration"></a>注册要由状态配置管理的 VM

可以使用 Azure 自动化状态配置来管理 Azure VM（包括经典 VM 和资源管理器 VM）、本地 VM、Linux 计算机、AWS VM，以及本地物理机。 在本主题中，我们介绍如何仅注册 Azure 资源管理器 VM。 有关注册其他类型的计算机的信息，请参阅[登记由 Azure 自动化状态配置管理的计算机](automation-dsc-onboarding.md)。

调用 [Register-AzAutomationDscNode](/powershell/module/Az.Automation/Register-AzAutomationDscNode?view=azps-3.7.0) cmdlet，将 VM 作为托管节点注册到 Azure Automation State Configuration。 

```powershell
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm'
```

### <a name="specify-configuration-mode-settings"></a>指定配置模式设置

使用 [Register-AzAutomationDscNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode) cmdlet，将 VM 注册为托管节点并指定配置属性。 例如，可以通过指定 `ApplyOnly` 作为 `ConfigurationMode` 属性的值，指定计算机的状态仅应用一次。 State Configuration 不会尝试在初始检查后应用配置。

```powershell
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationMode 'ApplyOnly'
```

还可使用 `ConfigurationModeFrequencyMins` 属性指定 DSC 检查配置状态的频率。 有关 DSC 配置设置的详细信息，请参阅[配置本地配置管理器](/powershell/scripting/dsc/managing-nodes/metaConfig)。

```powershell
# Run a DSC check every 60 minutes
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationModeFrequencyMins 60
```

## <a name="assign-a-node-configuration-to-a-managed-node"></a>将节点配置分配给托管节点

现在我们可以将已编译的节点配置分配给我们想要配置的 VM。

```powershell
# Get the ID of the DSC node
$node = Get-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Assign the node configuration to the DSC node
Set-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeConfigurationName 'TestConfig.WebServer' -NodeId $node.Id
```

这会将名为 `TestConfig.WebServer` 的节点配置分配到已注册 DSC 节点 `DscVm`。 默认情况下，每隔 30 分钟会检查一次 DSC 节点是否符合节点配置。 有关如何更改符合性检查间隔的信息，请参阅[配置本地配置管理器](/powershell/scripting/dsc/managing-nodes/metaConfig)。

## <a name="check-the-compliance-status-of-a-managed-node"></a>检查托管节点的符合性状态

可通过使用 [Get-AzAutomationDscNodeReport](/powershell/module/Az.Automation/Get-AzAutomationDscNodeReport?view=azps-3.7.0) cmdlet 来获取有关托管节点符合性状态的报告。

```powershell
# Get the ID of the DSC node
$node = Get-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Get an array of status reports for the DSC node
$reports = Get-AzAutomationDscNodeReport -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeId $node.Id

# Display the most recent report
$reports[0]
```

## <a name="remove-nodes-from-service"></a>从服务中删除节点

将节点添加到 Azure Automation State Configuration 时，本地 Configuration Manager 中的设置会设置为向服务注册，并提取配置以及用于配置计算机的必需模块。
如果选择从服务中删除节点，则可以使用 Azure 门户或 Az cmdlet 执行此操作。

> [!NOTE]
> 要从服务中注销节点，只需设置本地 Configuration Manager 设置，以便节点不再连接到服务。
> 这不会影响当前应用于节点的配置。
> 要删除当前配置，请使用 [PowerShell](/powershell/module/psdesiredstateconfiguration/remove-dscconfigurationdocument?view=powershell-5.1) 或删除本地配置文件（这是 Linux 节点的唯一选项）。

### <a name="azure-portal"></a>Azure 门户

在 Azure 自动化中，单击目录中的“State Configuration (DSC)”。
接下来，单击“节点”，查看在服务中注册的节点的列表。
单击要删除的节点的名称。
在打开的节点视图中，单击“注销”。

### <a name="powershell"></a>PowerShell

要使用 PowerShell 从 Azure Automation State Configuration 服务中注销节点，请按照 cmdlet [Unregister-AzAutomationDscNode](/powershell/module/az.automation/unregister-azautomationdscnode?view=azps-2.0.0) 的文档进行操作。

## <a name="next-steps"></a>后续步骤

- 有关入门信息，请参阅 [ 入门](automation-dsc-getting-started.md)。
- 要了解如何启用节点，请参阅[启用 Azure Automation State Configuration](automation-dsc-onboarding.md)。
- 若要了解如何编译 DSC 配置，以便将它们分配给目标节点，请参阅[在 Azure Automation State Configuration 中编译 DSC 配置](automation-dsc-compile.md)。
- 若要查看在持续部署管道中使用 Azure Automation State Configuration 的示例，请参阅[使用 Chocolatey 设置持续部署](automation-dsc-cd-chocolatey.md)。
- 有关定价信息，请参阅 [Azure Automation State Configuration 定价](https://azure.microsoft.com/pricing/details/automation/)。
- 有关 PowerShell cmdlet 参考，请参阅 [Az.Automation](/powershell/module/az.automation/?view=azps-3.7.0#automation)。
