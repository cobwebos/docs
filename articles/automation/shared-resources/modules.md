---
title: 管理 Azure 自动化中的模块
description: Azure 自动化允许导入 PowerShell 模块，以便在 runbook 和 dsc 配置中的 DSC 资源中启用 cmdlet。
services: automation
ms.service: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 84fdb5a9cf3c22048473cd00ee6f8e7ac36c9097
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864294"
---
# <a name="manage-modules-in-azure-automation"></a>管理 Azure 自动化中的模块

Azure 自动化允许导入 PowerShell 模块，以便在 runbook 和 dsc 配置中的 DSC 资源中启用 cmdlet。 Azure 自动化中使用的模块包括：

* [Azure PowerShell Az. Automation](/powershell/azure/new-azureps-module-az?view=azps-1.1.0)。
* [Azure PowerShell AzureRM](https://docs.microsoft.com/powershell/module/azurerm.automation/?view=azurermps-6.13.0)。
* 其他 PowerShell 模块。
* 内部`Orchestrator.AssetManagement.Cmdlets`模块。
* Python 2 模块。
* 你创建的自定义模块。

创建自动化帐户时，Azure 自动化会默认导入某些模块。 请参阅[默认模块](#default-modules)。

当自动化执行 runbook 和 DSC 编译作业时，它会将模块加载到沙盒中，其中 runbook 可以运行，而 DSC 配置可进行编译。 自动化还会自动将所有 DSC 资源置于 DSC 请求服务器上的模块中。 当应用 DSC 配置时，计算机可以提取资源。

>[!NOTE]
>请确保只导入您的 runbook 和 DSC 配置实际需要的模块。 建议不要导入根 Az 模块。 它包含可能不需要的许多其他模块，这可能会导致性能问题。 改为导入单个模块，如 Az. Compute。

>[!NOTE]
>本文使用 Azure PowerShell Az 模块。 你仍可以使用 AzureRM 模块。 若要了解有关 Az module 和 AzureRM 兼容性的详细信息，请参阅[新增 Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有关混合 Runbook 辅助角色上的 Az module 安装说明，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 对于你的自动化帐户，你可以通过使用[如何在 Azure 自动化中更新 Azure PowerShell 模块](../automation-update-azure-modules.md)，将模块更新到最新版本。

## <a name="default-modules"></a>默认模块

下表列出了在创建自动化帐户时，Azure 自动化默认情况下导入的模块。 自动化可以导入这些模块的更新版本。 但是，即使删除了较新版本，也无法从自动化帐户中删除原始版本。 请注意，这些默认模块包括多个 AzureRM 模块。 

自动化不会将根 Az 模块自动导入到任何新的或现有的自动化帐户中。 有关使用这些模块的详细信息，请参阅[迁移到 Az 模块](#migrating-to-az-modules)。

> [!NOTE]
> [在 Azure 自动化中包含在空闲时间启动/停止 VM 解决方案](../automation-solution-vm-management.md)的自动化帐户中，我们不建议更改模块和 runbook。

|模块名称|版本|
|---|---|
| AuditPolicyDsc | 1.1.0.0 |
| Azure | 1.0.3 |
| Azure.存储 | 1.0.3 |
| AzureRM.Automation | 1.0.3 |
| AzureRM.Compute | 1.2.1 |
| AzureRM.Profile | 1.0.3 |
| AzureRM.Resources | 1.0.3 |
| AzureRM.Sql | 1.0.3 |
| AzureRM.Storage | 1.0.3 |
| ComputerManagementDsc | 5.0.0.0 |
| GPRegistryPolicyParser | 0.2 |
| Microsoft.PowerShell.Core | 0 |
| Microsoft.PowerShell.Diagnostics |  |
| Microsoft.PowerShell.Management |  |
| Microsoft.PowerShell.Security |  |
| Microsoft.PowerShell.Utility |  |
| Microsoft.WSMan.Management |  |
| Orchestrator.AssetManagement.Cmdlets | 1 |
| PSDscResources | 2.9.0.0 |
| SecurityPolicyDsc | 2.1.0.0 |
| StateConfigCompositeResources | 1 |
| xDSCDomainjoin | 1.1 |
| xPowerShellExecutionPolicy | 1.1.0.0 |
| xRemoteDesktopAdmin | 1.1.0.0 |

## <a name="az-modules"></a>Az 模块

对于 Az. Automation，大多数 cmdlet 与用于 AzureRM 模块的名称相同，不同之处在于*AzureRM*前缀已更改为*Az*。 有关不遵循此命名约定的 Az 模块列表，请参阅[异常列表](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters)。

## <a name="internal-cmdlets"></a>内部 cmdlet

默认情况下，Azure `Orchestrator.AssetManagement.Cmdlets`自动化支持适用于 Windows 的 Log Analytics agent 的内部模块。 下表定义了内部 cmdlet。 这些 cmdlet 旨在用于代替 Azure PowerShell cmdlet 来与共享资源交互。 他们可以从加密的变量、凭据和加密的连接检索机密。

>[!NOTE]
>仅当你在 Azure 沙箱环境中或在 Windows 混合 Runbook 辅助角色上执行 runbook 时，内部 cmdlet 才可用。 

|名称|说明|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Start-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Wait-AutomationJob|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

请注意，内部 cmdlet 的命名不同于 Az 和 AzureRM cmdlet。 内部 cmdlet 名称不包含名词中的 "Azure" 或 "Az" 等词，但请使用 word *Automation*。 建议在 Azure 沙盒中或在 Windows 混合 Runbook 辅助角色上执行 runbook 时，使用 Az 或 AzureRM cmdlet。 它们需要较少的参数，并在已运行的作业的上下文中运行。

使用 Az 或 AzureRM cmdlet 操作 runbook 上下文外部的自动化资源。 

## <a name="python-modules"></a>Python 模块

可以在 Azure 自动化中创建 Python 2 runbook。 有关 Python 模块信息，请参阅[在 Azure 自动化中管理 Python 2 包](../python-packages.md)。

## <a name="custom-modules"></a>自定义模块

Azure 自动化支持创建的、用于 runbook 和 DSC 配置的自定义 PowerShell 模块。 一种类型的自定义模块是一个集成模块，可选择包含元数据的一个文件来定义 module cmdlet 的自定义功能。 [添加连接类型](../automation-connections.md#add-a-connection-type)中提供了使用集成模块的示例。

Azure Automation 可以导入自定义模块，使其 cmdlet 可用。 在幕后，它存储模块并在 Azure 沙箱中使用它，就像它执行其他模块一样。

## <a name="migrating-to-az-modules"></a>迁移到 Az 模块

### <a name="migration-considerations"></a>迁移注意事项

本部分包括在自动化中迁移到 Az 模块时要考虑的注意事项。 有关详细信息，请参阅[将 Azure PowerShell 从 AzureRM 迁移到 Az](https://docs.microsoft.com/powershell/azure/migrate-from-azurerm-to-az?view=azps-3.7.0)。 

#### <a name="use-of-azurerm-modules-and-az-modules-in-the-same-automation-account"></a>在同一 Automation 帐户中使用 AzureRM 模块和 Az 模块

 建议不要在同一 Automation 帐户中运行 AzureRM 模块和 Az 模块。 当你确定要从 AzureRM 迁移到 Az 时，最好完全提交完整的迁移。 自动化通常会在自动化帐户中重用沙盒，以节省启动时间。 如果未进行完整的模块迁移，则可以启动仅使用 AzureRM 模块的作业，然后启动另一个只使用 Az 模块的作业。 沙盒即将崩溃，你会收到一条错误消息，指出模块不兼容。 这种情况会导致任何特定的 runbook 或配置随机发生崩溃。 

#### <a name="importing-az-modules-into-the-powershell-session"></a>将 Az 模块导入 PowerShell 会话

将 Az 模块导入到自动化帐户不会自动将模块导入 runbook 使用的 PowerShell 会话中。 在以下情况中，模块会导入到 PowerShell 会话中：

* Runbook 从模块调用 cmdlet。
* Runbook 使用[import-module](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/import-module?view=powershell-7) cmdlet 显式导入模块。
* Runbook 导入另一个从属模块时。

#### <a name="testing-your-runbooks-and-dsc-configurations-prior-to-module-migration"></a>在模块迁移之前测试 runbook 和 DSC 配置

在迁移到 Az 模块之前，请务必谨慎地在单独的自动化帐户中测试所有 runbook 和 DSC 配置。 

#### <a name="updates-for-tutorial-runbooks"></a>教程 runbook 更新 

创建新的自动化帐户时，即使在迁移到 Az 模块之后，默认情况下，自动安装 AzureRM 模块。 你仍可以通过 AzureRM cmdlet 更新教程 runbook。 但是，不应运行这些 runbook。

### <a name="stop-and-unschedule-all-runbooks-that-use-azurerm-modules"></a>停止并取消使用 AzureRM 模块的所有 runbook

若要确保不运行任何使用 AzureRM 模块的现有 runbook 或 DSC 配置，必须停止并取消所有受影响的 runbook 和配置。 首先，请确保单独查看每个 runbook 或 DSC 配置及其计划，以确保将来可以根据需要重新计划项。 

准备好删除计划时，可以使用 Azure 门户或[或者使用 set-azurermautomationschedule](https://docs.microsoft.com/powershell/module/azurerm.automation/remove-azurermautomationschedule?view=azurermps-6.13.0) cmdlet。 请参阅[删除计划](schedules.md#remove-a-schedule)。

### <a name="remove-the-azurerm-modules"></a>删除 AzureRM 模块

在导入 Az 模块之前，可以删除 AzureRM 模块。 但是，如果您这样做，您可以中断源控制同步，并使仍然计划的任何脚本失败。 如果决定删除模块，请参阅[卸载 AzureRM](https://docs.microsoft.com/powershell/azure/migrate-from-azurerm-to-az?view=azps-3.8.0#uninstall-azurerm)。

### <a name="import-the-az-modules"></a>导入 Az 模块

可以在 Azure 门户中导入 Az 模块。 请记住只导入所需的 Az 模块，而不是整个 Az 模块。 因为[az](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0)是其他 az 模块的依赖项，所以请确保在所有其他模块之前导入此模块。

1. 在自动化帐户的 "**共享资源**" 下，选择 "**模块**"。 
2. 选择 "**浏览库**"。  
3. 在搜索栏中输入模块名称（例如`Az.Accounts`）。 
4. 在**PowerShell 模块**页上，选择 "**导入**"，将模块导入自动化帐户。

    ![将模块导入自动化帐户的屏幕截图](../media/modules/import-module.png)

还可以通过搜索要导入的模块，通过[PowerShell 库](https://www.powershellgallery.com)执行此导入。 找到该模块后，选择它，然后选择 " **Azure 自动化**" 选项卡。选择 "**部署到 azure 自动化**"。

![直接从 PowerShell 库导入模块的屏幕截图](../media/modules/import-gallery.png)

### <a name="test-your-runbooks"></a>测试 runbook

将 Az 模块导入自动化帐户后，可以开始编辑 runbook 和 DSC 配置，以使用新的模块。 测试 runbook 修改以使用新 cmdlet 的一种方法是使用 runbook 开头的`Enable-AzureRmAlias -Scope Process`命令。 通过将此命令添加到 runbook，脚本可以无需更改即可运行。 

## <a name="author-modules"></a>创作模块

建议你在创作自定义 PowerShell 模块时遵循此部分中的注意事项，以便在 Azure 自动化中使用。 若要准备要导入的模块，必须至少创建一个与 module 文件夹同名的 psd1、hbase-runner.psm1 或 PowerShell 模块 **.dll**文件。 然后，在模块文件夹中进行压缩，使 Azure 自动化可以将其作为单个文件导入。 **.Zip**包的名称应与包含的模块文件夹的名称相同。 

若要详细了解如何创建 PowerShell 模块，请参阅[如何编写 Powershell 脚本模块](https://docs.microsoft.com/powershell/scripting/developer/module/how-to-write-a-powershell-script-module?view=powershell-7)。

### <a name="version-folder"></a>版本文件夹

不要在模块的 **.zip**包中包含版本文件夹。 此问题不太关心 runbook，但会导致状态配置（DSC）服务出现问题。 当模块分发到由状态配置管理的节点时，Azure 自动化会自动创建版本文件夹。 如果版本文件夹存在，则最终会有两个实例。 下面是 DSC 模块的示例文件夹结构：

```powershell
myModule
  - DSCResources
    - myResourceFolder
      myResourceModule.psm1
      myResourceSchema.mof
  myModuleManifest.psd1
```

### <a name="help-information"></a>帮助信息

为模块中的每个 cmdlet 提供摘要、说明和帮助 URI。 在 PowerShell 中，可以使用`Get-Help` cmdlet 定义 cmdlet 的帮助信息。 下面的示例演示如何在**hbase-runner.psm1**模块文件中定义概要和 help URI。

  ```powershell
  <#
       .SYNOPSIS
        Gets a Contoso User account
  #>
  function Get-ContosoUser {
  [CmdletBinding](DefaultParameterSetName='UseConnectionObject', `
  HelpUri='https://www.contoso.com/docs/information')]
  [OutputType([String])]
  param(
     [Parameter(ParameterSetName='UserAccount', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [string]
     $UserName,

     [Parameter(ParameterSetName='UserAccount', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [string]
     $Password,

     [Parameter(ParameterSetName='ConnectionObject', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [Hashtable]
     $Connection
  )

  switch ($PSCmdlet.ParameterSetName) {
     "UserAccount" {
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $UserName, $Password
        Connect-Contoso -Credential $cred
     }
     "ConnectionObject" {
        Connect-Contoso -Connection $Connection
    }
  }
  }
  ```

  提供此信息将通过 PowerShell 控制台中`Get-Help`的 cmdlet 显示帮助文本。 此文本还会显示在 Azure 门户中。

  ![集成模块帮助的屏幕截图](../media/modules/module-activity-description.png)

### <a name="connection-type"></a>连接类型

如果模块连接到外部服务，请使用[自定义集成模块](#custom-modules)来定义连接类型。 模块中的每个 cmdlet 都应接受该连接类型（连接对象）的实例作为参数。 每次调用 cmdlet 时，用户都将连接资产的参数映射到 cmdlet 的相应参数。 

![在 Azure 门户中使用自定义连接](../media/modules/connection-create-new.png)

以下 runbook 示例使用名`ContosoConnection`为的 contoso 连接资产来访问 contoso 资源并从外部服务返回数据。 在此示例中，字段映射到`UserName` `Password` `PSCredential`对象的和属性，然后传递给 cmdlet。

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

若要实现此行为，一种更简单且更好的方法是直接将连接对象传递给 cmdlet：

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

你可以为 cmdlet 启用类似行为，方法是允许它们直接接受连接对象作为参数，而不是只接受参数的连接字段。 通常需要为每个参数设置参数，以便不使用自动化的用户可以调用 cmdlet，而无需构造哈希表来充当连接对象。 参数集`UserAccount`用于传递连接字段属性。 `ConnectionObject` 用于一直传递连接。

### <a name="output-type"></a>输出类型

在模块中定义所有 cmdlet 的输出类型。 定义 cmdlet 的输出类型允许设计时 IntelliSense 帮助在创作期间确定 cmdlet 的输出属性。 在图形 runbook 创作过程中，这种做法特别有用，在这种情况下，设计时知识非常适合使用模块。

Add `[OutputType([<MyOutputType>])]`，其中`MyOutputType`是有效类型。 若要了解有关`OutputType`的详细信息，请参阅[关于函数 OutputTypeAttribute](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute)。 以下代码示例演示如何将 `OutputType` 添加到 cmdlet：

  ```powershell
  function Get-ContosoUser {
  [OutputType([String])]
  param(
     [string]
     $Parameter1
  )
  # <script location here>
  }
  ```

  ![图形 runbook 输出类型的屏幕截图](../media/modules/runbook-graphical-module-output-type.png)

  此行为类似于 PowerShell integration service 环境中 cmdlet 输出的 "向前键入" 功能，而无需运行该功能。

  ![POSH IntelliSense 的屏幕截图](../media/modules/automation-posh-ise-intellisense.png)

### <a name="cmdlet-state"></a>Cmdlet 状态

使模块中的所有 cmdlet 无状态。 多个 runbook 作业可以同时在同一`AppDomain`进程和沙盒中运行。 如果在这两些级别共享了任何状态，则作业可能会相互影响。 此行为可能会导致间歇且难以诊断的问题。 下面是不需要执行的操作的示例：

  ```powershell
  $globalNum = 0
  function Set-GlobalNum {
     param(
         [int] $num
     )

     $globalNum = $num
  }
  function Get-GlobalNumTimesTwo {
     $output = $globalNum * 2

     $output
  }
  ```

### <a name="module-dependency"></a>模块依赖项

确保模块完全包含在可以使用 xcopy 复制的包中。 执行 runbook 时，自动化模块会分发到自动化沙盒。 模块必须独立于运行它们的主机。 

你应该能够在将模块包导入到另一个主机的 PowerShell 环境中时对其进行压缩和移动，并使其正常运行。 为此，请确保模块不依赖于模块在将模块导入自动化时所压缩的模块文件夹之外的任何文件。 

模块不应依赖于主机上的任何唯一注册表设置。 例如，在安装产品时进行的设置。 

### <a name="module-file-paths"></a>模块文件路径

请确保该模块中的所有文件的路径都少于140个字符。 长度超过140个字符的任何路径都会导致导入 runbook 时出现问题。 自动化无法将路径大小超过140个字符的文件导入到 PowerShell 会话`Import-Module`中。

## <a name="import-modules"></a>导入模块

本部分定义可将模块导入到自动化帐户中的几种方法。 

### <a name="import-modules-in-the-azure-portal"></a>导入 Azure 门户中的模块

导入 Azure 门户中的模块：

1. 返回到自动化帐户。
2. 在 "**共享资源**" 下选择 "**模块**"。
3. 选择 "**添加模块**"。 
4. 选择包含模块的 **.zip**文件。
5. 选择 **"确定"** 以开始导入进程。

### <a name="import-modules-by-using-powershell"></a>使用 PowerShell 导入模块

可以使用[AzAutomationModule](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationmodule?view=azps-3.7.0) cmdlet 将模块导入到自动化帐户中。 该 cmdlet 将获取模块 .zip 包的 URL。

```azurepowershell-interactive
New-AzAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

你还可以使用同一 cmdlet 直接从 PowerShell 库导入模块。 请确保从`ModuleName` [PowerShell 库](https://www.powershellgallery.com)获取`ModuleVersion`和。

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

### <a name="import-modules-from-the-powershell-gallery"></a>从 PowerShell 库导入模块

可以直接从库中或从自动化帐户导入[PowerShell 库](https://www.powershellgallery.com)模块。

直接从 PowerShell 库导入模块：

1. 找到https://www.powershellgallery.com并搜索要导入的模块。
2. 在 "**安装选项**" 下的 " **azure 自动化**" 选项卡上，选择 "**部署到 Azure 自动化**"。 此操作将打开 Azure 门户。 
3. 在 "**导入**" 页上，选择自动化帐户，然后选择 **"确定"**。

![PowerShell 库导入模块的屏幕截图](../media/modules/powershell-gallery.png)

从自动化帐户直接导入 PowerShell 库模块：

1. 在 "**共享资源**" 下选择 "**模块**"。 
2. 选择 "**浏览库**"，然后在库中搜索模块。 
3. 选择要导入的模块，然后选择 "**导入**"。 
4. 选择 **"确定"** 以开始导入过程。

![从 Azure 门户导入 PowerShell 库模块的屏幕截图](../media/modules/gallery-azure-portal.png)

## <a name="delete-modules"></a>删除模块

如果某个模块存在问题，或者需要回滚到以前版本的模块，则可以将其从自动化帐户中删除。 你无法删除在创建自动化帐户时导入的[默认模块](#default-modules)的原始版本。 如果要删除的模块是[默认模块](#default-modules)之一的较新版本，则它将回滚到随你的自动化帐户一起安装的版本。 否则，将删除从自动化帐户中删除的任何模块。

### <a name="delete-modules-in-the-azure-portal"></a>删除 Azure 门户中的模块

删除 Azure 门户中的模块：

1. 返回到自动化帐户。 在 "**共享资源**" 下选择 "**模块**"。 
2. 选择要删除的模块。 
3. 在 "**模块**" 页上，选择 "**删除**"。 如果此模块是[默认模块](#default-modules)之一，则会回滚到创建自动化帐户时存在的版本。

### <a name="delete-modules-by-using-powershell"></a>使用 PowerShell 删除模块

若要通过 PowerShell 删除模块，请运行以下命令：

```azurepowershell-interactive
Remove-AzAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="next-steps"></a>后续步骤

* 有关使用 Azure PowerShell 模块的详细信息，请参阅[Azure PowerShell 入门](https://docs.microsoft.com/powershell/azure/get-started-azureps?view=azps-3.7.0)。
* 若要详细了解如何创建 PowerShell 模块，请参阅[编写 Windows PowerShell 模块](https://docs.microsoft.com/powershell/scripting/developer/module/writing-a-windows-powershell-module?view=powershell-7)。
