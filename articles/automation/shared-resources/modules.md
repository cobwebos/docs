---
title: 管理 Azure 自动化中的模块
description: 本文介绍如何使用 PowerShell 模块在 DSC 配置中启用 runbook 和 DSC 资源中的 cmdlet。
services: automation
ms.service: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 82bec23ac35f4f0e6c65720d0c3a36355fa4224d
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83713448"
---
# <a name="manage-modules-in-azure-automation"></a>管理 Azure 自动化中的模块

Azure 自动化使用许多 PowerShell 模块在 runbook DSC 配置中启用 runbook 和 DSC 资源中的 cmdlet。 支持的模块包括：

* [Azure PowerShell Az.Automation](/powershell/azure/new-azureps-module-az?view=azps-1.1.0)。
* [Azure PowerShell AzureRM.Automation](https://docs.microsoft.com/powershell/module/azurerm.automation/?view=azurermps-6.13.0)。
* 其他 PowerShell 模块。
* 内部 `Orchestrator.AssetManagement.Cmdlets` 模块。
* Python 2 模块。
* 你创建的自定义模块。

创建自动化帐户时，Azure 自动化默认导入一些模块。 请参阅[默认模块](#default-modules)。

当自动化执行 runbook 和 DSC 编译作业时，它会将模块加载到沙盒中，其中 runbook 可以运行，并且 DSC 配置可以编译。 自动化还自动将任何 DSC 资源放置到 DSC 拉取服务器上的模块中。 计算机在应用 DSC 配置时可以提取资源。

>[!NOTE]
>请务必仅导入 runbook 和 DSC 配置所需的模块。 不建议导入根 Az 模块。 它包括可能不需要的许多其他模块，这可能导致性能问题。 改为导入单个模块，如 Az.Compute。

## <a name="default-modules"></a>默认模块

下表列出了在创建自动化帐户时，Azure 自动化默认导入的模块。 自动化可以导入这些模块的较新版本。 但是，即使你删除了较新新版本，也不能从自动化帐户中删除原始版本。 请注意，这些默认模块包括多个 AzureRM 模块。 

自动化不会自动将根 Az 模块导入任何新的或现有的自动化帐户。 要详细了解如何使用这些模块，请参阅[迁移到 Az 模块](#migrate-to-az-modules)。

> [!NOTE]
> 我们不建议更改自动化帐户中用于部署[在空闲时间启动/停止 VM](../automation-solution-vm-management.md) 的模块和 runbook。

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

对于 Az.Automation，大多数 cmdlet 的名称与 AzureRM 模块的名称相同，但 `AzureRM` 前缀已更改为 `Az`。 有关不遵循此命名约定的 Az 模块列表，请参阅[例外名称列表](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters)。

## <a name="internal-cmdlets"></a>内部 cmdlet

Azure 自动化支持默认安装的 Windows Log Analytics 代理的内部 `Orchestrator.AssetManagement.Cmdlets` 模块。 下表定义了内部 cmdlet。 这些 cmdlet 旨在用于代替 Azure PowerShell cmdlet 与共享资源进行交互。 它们可以从加密的变量、凭据和加密的连接中检索机密。

>[!NOTE]
>仅当在 Azure 沙盒环境中或对 Windows 混合 Runbook 辅助角色执行 runbook 时，内部 cmdlet 才可用。 

|名称|说明|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Start-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Wait-AutomationJob|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

请注意，内部 cmdlet 的命名与 Az 和 AzureRM cmdlet 的不同。 内部 cmdlet 名称不包含像 `Azure` 或 `Az` 这样的单词，但请使用单词 `Automation`。 建议在 Azure 沙盒中或对 Windows 混合 Runbook 辅助角色运行 runbook 时优先使用内部 cmdlet，其次才是 Az 或 AzureRM cmdlet。 它们需要的参数更少，并且可在已运行的作业上下文中运行。

使用 Az 或 AzureRM cmdlet 在 runbook 上下文之外操作自动化资源。 

## <a name="python-modules"></a>Python 模块

可以在 Azure 自动化中创建 Python 2 runbook。 有关 Python 模块信息，请参阅[在 Azure 自动化中管理 Python 2 包](../python-packages.md)。

## <a name="custom-modules"></a>自定义模块

Azure 自动化支持你创建用于 runbook 和 DSC 配置的自定义 PowerShell 模块。 自定义模块的一种类型是集成模块，该模块可以选择包含元数据文件，以定义模块 cmdlet 的自定义功能。 [添加连接类型](../automation-connections.md#add-a-connection-type)中提供了集成模块的使用的示例。

Azure 自动化可以导入自定义模块以提供其 cmdlet。 它可以在后台存储该模块并在 Azure 沙盒中使用，方式与其他模块别无二致。

## <a name="migrate-to-az-modules"></a>迁移到 Az 模块

本部分介绍如何迁移到自动化中的 Az 模块。 有关详细信息，请参阅[将 Azure PowerShell 从 AzureRM 迁移到 Az](https://docs.microsoft.com/powershell/azure/migrate-from-azurerm-to-az?view=azps-3.7.0)。 

我们不建议在同一自动化帐户中运行 AzureRM 模块和 Az 模块。 当确定要从 AzureRM 迁移到 Az 时，最好完全采用完整迁移。 自动化通常会重复使用自动化帐户中的沙盒，以节省启动时间。 如果不进行完整模块迁移，则可以启动仅使用 AzureRM 模块的作业，然后启动另一个仅使用 Az 模块的作业。 沙盒即将崩溃，你会收到一个错误，指出模块不兼容。 这种情况会导致任何特定 runbook 或配置的随机崩溃。 

>[!NOTE]
>创建新的自动化帐户时，即使在迁移到 Az 模块后，自动化也会默认安装 AzureRM 模块。 你仍可以使用 AzureRM cmdlet 更新教程 runbook。 但是，不应运行这些 runbook。

### <a name="test-your-runbooks-and-dsc-configurations-prior-to-module-migration"></a>在模块迁移之前测试 runbook 和 DSC 配置

在迁移到 Az 模块之前，请务必在单独的自动化帐户中仔细测试所有 runbook 和 DSC 配置。 

### <a name="stop-and-unschedule-all-runbooks-that-use-azurerm-modules"></a>停止并取消计划使用 AzureRM 模块的所有 runbook

要确保不运行任何使用 AzureRM 模块的现有 runbook 或 DSC 配置，必须停止并取消计划所有受影响的 runbook 和配置。 首先，请确保分别查看每个 runbook 或 DSC 配置及其计划，以确保将来可以根据需要重新计划项目。 

准备好删除计划时，可以使用 Azure 门户或 [Remove-AzureRmAutomationSchedule](https://docs.microsoft.com/powershell/module/azurerm.automation/remove-azurermautomationschedule?view=azurermps-6.13.0) cmdlet。 请参阅[删除计划](schedules.md#remove-a-schedule)。

### <a name="remove-azurerm-modules"></a>删除 AzureRM 模块

在导入 Az 模块之前，可以删除 AzureRM 模块。 但如果这样做，则可以中断源代码管理同步并导致任何仍在计划的脚本失败。 如果决定删除模块，请参阅[卸载 AzureRM](https://docs.microsoft.com/powershell/azure/migrate-from-azurerm-to-az?view=azps-3.8.0#uninstall-azurerm)。

### <a name="import-az-modules"></a>导入 Az 模块

在自动化帐户中导入 Az 模块的行为不会在 runbook 使用的 PowerShell 会话中自动导入该模块。 在以下情况中，模块会导入到 PowerShell 会话中：

* runbook 从模块中调用 cmdlet 时。
* runbook 使用 [Import-Module](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/import-module?view=powershell-7) cmdlet 显式导入模块时。
* runbook 导入另一个依赖模块时。

可以在 Azure 门户中导入 Az 模块。 请记住仅导入所需的 Az 模块，而不是导入整个 Az.Automation 模块。 由于 [Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) 是其他 Az 模块的依赖项，因此请确保在其他模块之前先将其导入。

1. 在自动化帐户中的“共享资源”下，选择“模块” 。 
2. 选择“浏览库”。  
3. 在搜索栏中，输入模块名称（例如 `Az.Accounts`）。 
4. 在 PowerShell 模块页，选择“导入”，将模块导入自动化帐户。

    ![将模块导入自动化帐户的屏幕截图](../media/modules/import-module.png)

还可以搜索要导入的模块，通过 [PowerShell 库](https://www.powershellgallery.com)执行此导入。 找到该模块后，选择它，然后选择“Azure 自动化”选项卡。选择“部署到 Azure 自动化”。

![直接从 PowerShell 库导入模块的屏幕截图](../media/modules/import-gallery.png)

### <a name="test-your-runbooks"></a>测试 runbook

将 Az 模块导入自动化帐户后，可以开始编辑 runbook 和 DSC 配置以使用新模块。 测试 runbook 的修改以使用新 cmdlet 的一种方法是在 runbook 的开头使用 `Enable-AzureRmAlias -Scope Process` 命令。 通过将此命令添加到 runbook，脚本无需更改也可运行。 

## <a name="author-modules"></a>创作者模块

当创作用于 Azure 自动化的自定义 PowerShell 模块时，建议遵循本部分中的注意事项。 要准备要导入的模块，必须至少创建一个名称与模块文件夹的名称相同的 psd1、psm1 或 PowerShell 模块 .dll 文件。 然后压缩模块文件夹，以便 Azure 自动化可以将其作为单个文件导入。 .zip 包的名称应与包含的模块文件夹的名称相同。 

要详细了解如何创作 PowerShell 模块，请参阅[如何编写 PowerShell 脚本模块](https://docs.microsoft.com/powershell/scripting/developer/module/how-to-write-a-powershell-script-module?view=powershell-7)。

### <a name="version-folder"></a>版本文件夹

请不要在模块的 .zip 包中包含版本文件夹。 此问题在 runbook 中不太重要，但它确实会导致状态配置 (DSC) 服务出现问题。 当模块分发到状态配置管理的节点时，Azure 自动化会自动创建版本文件夹。 如果存在版本文件夹，则最终会获得两个实例。 下面是 DSC 模块的示例文件夹结构：

```powershell
myModule
  - DSCResources
    - myResourceFolder
      myResourceModule.psm1
      myResourceSchema.mof
  myModuleManifest.psd1
```

### <a name="help-information"></a>帮助信息

在模块中加入每个 cmdlet 的摘要、说明和帮助 URI。 在 PowerShell 中，可以使用 `Get-Help` cmdlet 来定义 cmdlet 的帮助信息。 以下示例显示如何在 .psm1 模块文件中定义摘要和帮助 URI。

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

  提供此信息通过 PowerShell 控制台中的 `Get-Help` cmdlet 显示帮助文本。 此文本也显示在 Azure 门户中。

  ![集成模块帮助的屏幕截图](../media/modules/module-activity-description.png)

### <a name="connection-type"></a>连接类型

如果模块连接到外部服务，请使用[自定义集成模块](#custom-modules)定义连接类型。 模块中的每个 cmdlet 都应接受该连接类型（连接对象）的实例作为参数。 用户可在每次调用 cmdlet 时将连接资产的参数映射到 cmdlet 的相应参数。 

![在 Azure 门户中使用自定义连接](../media/modules/connection-create-new.png)

下面的 runbook 示例使用名为 `ContosoConnection` 的 Contoso 连接资产来访问 Contoso 资源并从外部服务返回数据。 在此示例中，字段映射到 `PSCredential` 对象的 `UserName` 和 `Password` 属性，然后传递给 cmdlet。

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

实现此行为的更轻松且更好的方法是直接将连接对象传递给 cmdlet：

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

要为 cmdlet 启用类似行为，可以允许 cmdlet 以参数的形式直接接受连接对象，而不是只接受连接字段作为参数。 通常情况下，需要为每个 cmdlet 提供一个参数集，这样不使用自动化的用户在调用 cmdlet 时就不需要构造一个哈希表来充当连接对象。 参数集 `UserAccount` 可用于传递连接字段属性。 `ConnectionObject` 允许将连接一直传下去。

### <a name="output-type"></a>输出类型

在模块中定义所有 cmdlet 的输出类型。 定义 cmdlet 的输出类型以后，就可以利用设计时 IntelliSense 在创作期间确定 cmdlet 的输出属性。 这种做法在图形 runbook 的创作过程中特别有用，这种创作过程要求掌握一定程度的设计时知识，以改进用户对模块的使用体验。

添加 `[OutputType([<MyOutputType>])]`，其中 `MyOutputType` 是有效类型。 要详细了解 `OutputType`，请参阅[关于 Functions OutputTypeAttribute](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute)。 下面代码是将 `OutputType` 添加到 cmdlet 的示例：

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

  此行为类似于 PowerShell 集成服务环境中 cmdlet 输出的“预输入”功能，但不需要运行。

  ![POSH IntelliSense 的屏幕截图](../media/modules/automation-posh-ise-intellisense.png)

### <a name="cmdlet-state"></a>Cmdlet 状态

使模块中的所有 cmdlet 以无状态方式呈现。 多个 runbook 作业可在同一 `AppDomain` 和同一进程及沙盒中同时运行。 如果在这些级别上共享了任何状态，则作业可能会互相影响。 此行为会导致间歇性和难以诊断的问题。 下面是应避免的处理方式的示例：

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

确保模块完全包含在可以使用 xcopy 进行复制的包中。 runbook 执行时，自动化模块将分发到自动化沙盒中。 模块必须独立于运行它们的主机工作。 

你应能够压缩并移动模块包，并确保该模块包在导入到其他主机的 PowerShell 环境时可以正常运行。 为此，请确保模块不依赖在模块导入自动化时压缩的模块文件夹外部的任何文件。 

模块不应依赖主机上的任何唯一注册表设置。 例如，安装产品时所做的设置。 

### <a name="module-file-paths"></a>模块文件路径

确保模块中所有文件的路径少于 140 个字符。 长度超过 140 个字符的任何路径都会导致导入 runbook 时出现问题。 自动化无法使用 `Import-Module` 将路径大小超过 140 个字符的文件导入 PowerShell 会话中。

## <a name="import-modules"></a>导入模块

本部分定义了将模块导入自动化帐户的几种方法。 

### <a name="import-modules-in-the-azure-portal"></a>在 Azure 门户中导入 Azure 模块

在 Azure 门户中导入 Azure 模块：

1. 返回到自动化帐户。
2. 在“共享资源”下，选择“模块” 。
3. 选择“添加模块”。 
4. 选择包含模块的 .zip 文件。
5. 选择“确定”以开始导入过程。

### <a name="import-modules-by-using-powershell"></a>使用 PowerShell 导入模块

可以使用 [New-AzAutomationModule](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationmodule?view=azps-3.7.0) cmdlet 将模块导入自动化帐户。 cmdlet 会获取模块 .zip 包的 URL。

```azurepowershell-interactive
New-AzAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

还可以使用相同的 cmdlet 直接从 PowerShell 库导入模块。 请确保从 [PowerShell 库](https://www.powershellgallery.com)获取 `ModuleName` 和 `ModuleVersion`。

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

### <a name="import-modules-from-the-powershell-gallery"></a>从 PowerShell 库导入模块

可以直接从库中或自动化帐户中导入 [PowerShell 库](https://www.powershellgallery.com)模块。

直接从 PowerShell 库导入模块：

1. 转到 https://www.powershellgallery.com ，然后搜索要导入的模块。
2. 在“安装选项”下的“Azure 自动化”选项卡上，选择“部署到 Azure 自动化”  。 此操作会打开 Azure 门户。 
3. 在“导入”页上，选择你的自动化帐户并选择“确定”。

![PowerShell 库导入模块的屏幕截图](../media/modules/powershell-gallery.png)

直接从自动化帐户导入 PowerShell 库模块：

1. 在“共享资源”下，选择“模块” 。 
2. 选择“浏览库”，然后搜索模块的库。 
3. 选择要导入的模块，然后选择“导入”。 
4. 选择“确定”以开始导入过程。

![从 Azure 门户导入 PowerShell 库模块的屏幕截图](../media/modules/gallery-azure-portal.png)

## <a name="delete-modules"></a>删除模块

如果模块出现问题，或者需要回滚到模块的早期版本，则可以将其从自动化帐户中删除。 无法删除[默认模块](#default-modules)的原始版本，这些模块是在创建自动化帐户时导入的。 如果要删除的模块是[默认模块](#default-modules)之一的较新版本，则它会回滚到使用自动化帐户安装的版本。 否则，从自动化帐户中删除的任何模块都将被删除。

### <a name="delete-modules-in-the-azure-portal"></a>在 Azure 门户中删除模块

在 Azure 门户中删除模块：

1. 返回到自动化帐户。 在“共享资源”下，选择“模块” 。 
2. 选择要删除的模块。 
3. 在“模块”页上，选择“删除”。 如果此模块是[默认模块](#default-modules)之一，则它会回滚到创建自动化帐户时存在的版本。

### <a name="delete-modules-by-using-powershell"></a>使用 PowerShell 删除模块

要通过 PowerShell 删除模块，请运行以下命令：

```azurepowershell-interactive
Remove-AzAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="next-steps"></a>后续步骤

* [Azure PowerShell 入门](https://docs.microsoft.com/powershell/azure/get-started-azureps?view=azps-3.7.0)
* [编写 Windows PowerShell 模块](https://docs.microsoft.com/powershell/scripting/developer/module/writing-a-windows-powershell-module?view=powershell-7)
