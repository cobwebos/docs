---
title: 管理 Azure 自动化中的模块
description: 本文介绍如何在 Azure 自动化中管理模块。
services: automation
ms.service: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2a567b156c3a7e0c48e2c2f8db2d268e0eb3307c
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770949"
---
# <a name="manage-modules-in-azure-automation"></a>管理 Azure 自动化中的模块

Azure 自动化允许您导入 PowerShell 模块，以便在 DSC 配置中启用 Runbook 和 DSC 资源中的 cmdlet。 Azure 自动化中使用的模块包括：

* [Azure 电源外壳 Az.自动化](/powershell/azure/new-azureps-module-az?view=azps-1.1.0)
* [Azure 电源外壳 AzureRM.自动化](https://docs.microsoft.com/powershell/module/azurerm.automation/?view=azurermps-6.13.0)
* 适用于`Orchestrator.AssetManagement.Cmdlets`Windows 的日志分析代理的内部模块
* [Azure 自动化创作工具包](https://www.powershellgallery.com/packages/AzureAutomationAuthoringToolkit/0.2.3.9)
* 其他 PowerShell 模块
* 您创建的自定义模块 

创建自动化帐户时，Azure 自动化默认导入一些模块。 请参阅[默认模块](#default-modules)。

当 Azure 自动化执行 Runbook 和 DSC 编译作业时，它会将模块加载到可以运行 Runbook 和 DSC 配置的沙盒中。 自动化还会自动将任何 DSC 资源放在 DSC 拉取服务器上的模块中。 计算机在应用 DSC 配置时可以提取资源。

>[!NOTE]
>请务必仅导入 Runbook 和 DSC 配置实际需要的模块。 无论如何，不要导入汇总模块，例如，Az.自动化。

>[!NOTE]
>本文进行了更新，以便使用新的 Azure PowerShell Az 模块。 你仍然可以使用 AzureRM 模块，至少在 2020 年 12 月之前，它将继续接收 bug 修补程序。 若要详细了解新的 Az 模块和 AzureRM 兼容性，请参阅[新 Azure Powershell Az 模块简介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有关混合 Runbook 辅助角色上的 Az 模块安装说明，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 对于自动化帐户，可以使用["如何更新 Azure 自动化 中的 Azure PowerShell"模块](../automation-update-azure-modules.md)将模块更新到最新版本。

## <a name="default-modules"></a>默认模块

下表列出了 Azure 自动化在创建自动化帐户时默认导入的模块。 自动化可以导入这些模块的较新版本。 但是，即使您删除了较新版本，也不能从自动化帐户中删除原始版本。 请注意，这些默认模块包括多个 AzureRM 模块。 

> [!NOTE]
> 我们不建议在包含任何解决方案的自动化帐户中更改模块和 Runbook。 

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


在 Runbook 和 DSC 配置中首选 Az.自动化模块。 但是，Azure 自动化不会自动将汇总 Az 模块导入任何新的或现有的自动化帐户。 有关使用这些模块的更多，请参阅[迁移到 Az 模块](#migrating-to-az-modules)。

## <a name="internal-cmdlets"></a>内部 cmdlet

下表定义了`Orchestrator.AssetManagement.Cmdlets`模块支持的内部 cmdlet。 在 Runbook 和 DSC 配置中使用这些配置与自动化帐户中的自动化资产进行交互。 cmdlet 旨在从加密的变量、凭据和加密连接中检索机密。 

> [!NOTE]
> Azure PowerShell cmdlet 无法获取内部 cmdlet 可以检索的资产机密。 

|名称|说明|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Start-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Wait-AutomationJob|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

这些内部 cmdlet 在 Windows 混合 Runbook 辅助角色上可用，但在 Linux 混合 Runbook 辅助功能程序上不可用。 它们从协调器沙盒中执行，该沙盒由混合工作人员使用。  它们的使用不需要与 Azure 的隐式连接，就像使用"运行方式"帐户进行身份验证时一样。

使用 Az 或 AzureRM cmdlet 用于直接在计算机上运行的 Runbook 和配置，或者针对环境中的资源，而不是使用内部 cmdlet。 在这些情况下，在使用 cmdlet 时必须隐式连接到 Azure，就像使用 Run As 帐户对 Azure 进行身份验证时一样。 

## <a name="modules-supporting-get-automationpscredential"></a>支持获取自动化PS凭据的模块

对于使用 Azure 自动化创作工具包的本地开发，cmdlet`Get-AutomationPSCredential`是程序集 Azure[自动化创作工具包的](https://www.powershellgallery.com/packages/AzureAutomationAuthoringToolkit/0.2.3.9)一部分。 对于使用自动化上下文的 Azure，cmdlet 位于`Orchestrator.AssetManagement.Cmdlets`中。 要了解有关在 Azure 自动化中使用凭据的更多信息，请参阅[Azure 自动化 中的凭据资产](credentials.md)。

## <a name="migrating-to-az-modules"></a>迁移到 Az 模块

在 Azure 自动化中使用 Az 模块时，需要考虑以下几点：

* 自动化帐户中的更高级别解决方案可以使用 Runbook 和模块。 因此，编辑 Runbook 或升级模块可能会导致解决方案出现问题。 在导入 Az 模块之前，应在单独的自动化帐户中仔细测试所有 Runbook 和解决方案。 

* 对模块的任何修改都会对[非工作时间解决方案的启动/停止 VM](../automation-solution-vm-management.md)产生负面影响。 

* 将 Az 模块导入自动化帐户不会自动在运行手册使用的 PowerShell 会话中导入该模块。 在以下情况中，模块会导入到 PowerShell 会话中：

    * 当 Runbook 从模块调用 cmdlet 时
    * 当 Runbook 使用`Import-Module`cmdlet 显式导入模块时
    * 当 Runbook 导入另一个从属模块时

完成模块迁移后，不要尝试使用自动化帐户上的 AzureRM 模块开始运行簿。 还建议不要导入或更新帐户上的 AzureRM 模块。 请考虑迁移到 Az.自动化的帐户，并且仅使用 Az 模块操作。 

>[!IMPORTANT]
>创建新的自动化帐户时，Azure 自动化默认安装 AzureRM 模块。 您仍可以使用 AzureRM cmdlet 更新教程运行簿。 但是，不应运行这些 Runbook。

### <a name="stop-and-unschedule-all-runbooks-that-use-azurerm-modules"></a>停止并取消计划使用 AzureRM 模块的所有 Runbook

为了确保不运行任何使用 AzureRM 模块的现有 Runbook，停止并取消计划所有受影响的 Runbook。 通过运行类似于此示例的代码，您可以看到存在哪些计划以及要删除的计划：

  ```powershell-interactive
  Get-AzureRmAutomationSchedule -AutomationAccountName "<AutomationAccountName>" -ResourceGroupName "<ResourceGroupName>" | Remove-AzureRmAutomationSchedule -WhatIf
  ```

请务必分别查看每个计划，以确保如有必要，可以在未来为 Runbook 重新计划。

### <a name="import-the-az-modules"></a>导入 Az 模块

本节介绍如何在 Azure 门户中导入 Az 模块。 请记住仅导入所需的 Az 模块，而不是导入整个 Az.自动化模块。 由于[Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0)是其他 Az 模块的依赖项，因此请确保在任何其他模块之前导入此模块。

1. 从您的自动化帐户中，选择**共享资源**下的**模块**。 
2. 单击“浏览库”，打开“浏览库”页****。  
3. 在搜索栏中，输入模块名称，例如 。 `Az.Accounts` 
4. 在 PowerShell 模块页面上，单击"**导入**"以将模块导入自动化帐户。

    ![将模块导入自动化帐户](../media/modules/import-module.png)

此导入过程也可以通过[PowerShell 库](https://www.powershellgallery.com)通过搜索要导入的模块来完成。 找到模块后，选择它，选择 Azure**自动化**选项卡，然后单击"**部署到 Azure 自动化**"。

![直接从库中导入模块](../media/modules/import-gallery.png)

### <a name="test-your-runbooks"></a>测试 runbook

将 Az 模块导入自动化帐户后，可以开始编辑 Runbook 以使用新模块。 大多数 cmdlet 的名称与 AzureRM 模块的名称相同，但 AzureRM（或 AzureRm） 前缀已更改为 Az。 有关不遵循此命名约定的模块列表，请参阅[异常列表](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters)。

测试 Runbook 的修改以使用新 cmdlet 的一种方法是在 Runbook 的开头使用`Enable-AzureRmAlias -Scope Process`。 通过将此命令添加到 runbook，脚本可以运行而不进行更改。

## <a name="authoring-modules"></a>创作模块

我们建议您在编写 PowerShell 模块以在 Azure 自动化中使用时遵循本节中的注意事项。

### <a name="references-to-azurerm-and-az"></a>对 AzureRM 和 Az 的引用

如果引用模块中的 Az 模块，请确保不引用 AzureRM 模块。 不能同时使用这两组模块。 

### <a name="version-folder"></a>版本文件夹

请勿在模块的 **.zip**包中包含版本文件夹。  此问题不太适合 Runbook，但确实会导致状态配置 （DSC） 服务出现问题。 当模块分发到由 DSC 管理的节点时，Azure 自动化会自动创建版本文件夹。 如果存在版本文件夹，则最终有两个实例。 下面是 DSC 模块的示例文件夹结构：

```powershell
myModule
  - DSCResources
    - myResourceFolder
      myResourceModule.psm1
      myResourceSchema.mof
  myModuleManifest.psd1
```

### <a name="help-information"></a>帮助信息

包括模块中每个 cmdlet 的概要、说明和帮助 URI。 在 PowerShell 中，您可以使用`Get-Help`cmdlet 为 cmdlet 定义帮助信息。 下面的示例演示如何在 **.psm1**模块文件中定义概要并帮助 URI。

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

  提供此信息通过 PowerShell 控制台`Get-Help`中的 cmdlet 显示帮助文本。 此文本也显示在 Azure 门户中。

  ![集成模块帮助](../media/modules/module-activity-description.png)

### <a name="connection-type"></a>连接类型

如果模块连接到外部服务，请定义[连接类型](#adding-a-connection-type-to-your-module)。 模块中的每个 cmdlet 都应接受连接对象（该连接类型的实例）作为参数。 用户每次调用 cmdlet 时都会将连接资产的参数映射到 cmdlet 的相应参数。 以下 Runbook 示例基于上一节中的示例，使用调用`ContosoConnection`的 Contoso 连接资产来访问 Contoso 资源并从外部服务返回数据。 在此示例中，字段映射到`UserName``Password``PSCredential`对象的 和 属性，然后传递到 cmdlet。

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

  处理此行为的一种更简单、更好的方法是将连接对象直接传递到 cmdlet：

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

  您可以通过允许 cmdlet 直接接受连接对象作为参数，而不是仅为参数接受连接字段，从而为 cmdlet 启用类似的行为。 通常情况下，需要为每个 cmdlet 提供一个参数集，这样不使用 Azure 自动化的用户在调用 cmdlet 时就不需要构造一个哈希表来充当连接对象。 参数集`UserAccount`用于传递连接字段属性。 `ConnectionObject` 用于一直传递连接。

### <a name="output-type"></a>输出类型

定义模块中所有 cmdlet 的输出类型。 定义 cmdlet 的输出类型允许设计时 IntelliSense 帮助确定 cmdlet 在创作过程中的输出属性。 在图形 Runbook 创作过程中，这种做法尤其有用，为此，设计时间知识是轻松体验模块的关键。

添加`[OutputType([<MyOutputType>])]`位置`MyOutputType`是有效的类型。 要了解有关 详细信息`OutputType`，请参阅[有关函数输出类型属性](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute)。 以下代码示例演示如何将 `OutputType` 添加到 cmdlet：

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

  ![图形 Runbook 输出类型](../media/modules/runbook-graphical-module-output-type.png)

  此行为类似于 PowerShell ISE 中 cmdlet 输出的“预输入”功能，但不需要运行。

  ![POSH IntelliSense](../media/modules/automation-posh-ise-intellisense.png)

### <a name="cmdlet-state"></a>Cmdlet 状态

使模块中的所有 cmdlet 都无状态。 多个 Runbook 作业可以在同一`AppDomain`进程和沙盒中同时运行。 如果在这两些级别共享了任何状态，则作业可能会相互影响。 此行为可能导致间歇性和难以诊断的问题。 下面是不该做什么的示例：

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

确保模块完全包含在可以使用 xcopy 复制的包中。 执行 Runbook 时，Azure 自动化模块将分发到自动化沙盒。 模块必须独立于运行它们的主机工作。 

您应该能够压缩和移动模块包，并在导入到另一个主机的 PowerShell 环境中时使其正常工作。 为此，请确保模块不依赖于模块文件夹外的任何文件，这些文件在将模块导入 Azure 自动化时压缩。 

您的模块不应依赖于主机上的任何唯一注册表设置。 示例是安装产品时所做的设置。 

### <a name="module-file-paths"></a>模块文件路径

确保模块中的所有文件具有少于 140 个字符的路径。 长度超过 140 个字符的任何路径都会导致导入 Runbook 出现问题。 Azure 自动化无法将路径大小超过 140 个字符的文件导入到 具有 的`Import-Module`PowerShell 会话中。

## <a name="importing-modules"></a>导入模块

本节定义了将模块导入自动化帐户的几种方法。 

### <a name="import-modules-in-azure-portal"></a>Azure 门户中的导入模块

要在 Azure 门户中导入模块，请参阅：

1. 导航到自动化帐户。
2. 在**共享资源**下选择**模块**。
3. 单击 **"添加模块**"。 
4. 选择包含模块的 **.zip**文件。
5. 单击 **"确定**"开始导入过程。

### <a name="import-modules-using-powershell"></a>使用 PowerShell 导入模块

您可以使用[新阿兹自动化模块](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationmodule?view=azps-3.7.0)cmdlet 将模块导入自动化帐户。 cmdlet 获取模块 .zip 包的 URL。

```azurepowershell-interactive
New-AzAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

您还可以使用相同的 cmdlet 直接从 PowerShell 库导入模块。 一定要抓住`ModuleName`并从`ModuleVersion`[PowerShell画廊](https://www.powershellgallery.com)。

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

### <a name="import-modules-from-powershell-gallery"></a>从 PowerShell 库导入模块

您可以直接从库或从自动化帐户导入[PowerShell 库](https://www.powershellgallery.com)模块。

要直接从 PowerShell 库导入模块：

1. 转到https://www.powershellgallery.com并搜索要导入的模块。
2. 单击 **"在****"安装选项**"下的**Azure 自动化**选项卡上部署到 Azure 自动化。 此操作将打开 Azure 门户。 
3. 在"导入"页上，选择您的自动化帐户，然后单击 **"确定**"。

![PowerShell 库导入模块](../media/modules/powershell-gallery.png)

要直接从您的自动化帐户导入 PowerShell 库模块，请：

1. 在**共享资源**下选择**模块**。 
2. 在"模块"页上，单击 **"浏览库**"，然后搜索库中的模块。 
3. 选择要导入的模块，然后单击"**导入**"。 
4. 在"导入"页上，单击"**确定"** 以启动导入过程。

![从 Azure 门户导入电源外壳库](../media/modules/gallery-azure-portal.png)

## <a name="deleting-modules"></a>删除模块

如果模块出现问题或需要回滚到模块的早期版本，则可以将其从自动化帐户中删除。 无法删除创建自动化帐户时导入的[默认模块](#default-modules)的原始版本。 如果要删除的模块是[默认模块](#default-modules)之一的较新版本，则它回滚到与自动化帐户一起安装的版本。 否则，您从自动化帐户中删除的任何模块都将被删除。

### <a name="delete-modules-in-azure-portal"></a>删除 Azure 门户中的模块

要删除 Azure 门户中的模块，请执行以下工作：

1. 导航到您的自动化帐户，并在**共享资源**下选择**模块**。 
2. 选择要删除的模块。 
3. 在 **"模块"** 页上，选择 **"删除**"。 如果此模块是[默认模块](#default-modules)之一，它将回滚到创建自动化帐户时存在的版本。

### <a name="delete-modules-using-powershell"></a>使用 PowerShell 删除模块

要通过 PowerShell 删除模块，请运行以下命令：

```azurepowershell-interactive
Remove-AzAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="adding-a-connection-type-to-your-module"></a>向模块添加连接类型

您可以通过向模块添加可选元数据文件，提供要在自动化帐户中使用的自定义[连接类型](../automation-connections.md)。 此文件指定要与自动化帐户中的模块 cmdlet 一起使用的 Azure 自动化连接类型。 要了解有关创作 PowerShell 模块的更多信息，请参阅[如何编写 PowerShell 脚本模块](https://docs.microsoft.com/powershell/scripting/developer/module/how-to-write-a-powershell-script-module?view=powershell-7)。

![在 Azure 门户中使用自定义连接](../media/modules/connection-create-new.png)

指定连接类型属性的文件名为**&lt;ModuleName&gt;-自动化.json，** 位于压缩 **.zip**文件的模块文件夹中。 此文件包含连接到模块表示的系统或服务所需的连接字段。 配置允许在 Azure 自动化中创建连接类型。 使用此文件，您可以设置字段名称、类型以及字段是加密还是模块的连接类型的可选。 下面的示例是 **.json**文件格式的模板，用于定义用户名和密码属性：

```json
{
   "ConnectionFields": [
   {
      "IsEncrypted":  false,
      "IsOptional":  true,
      "Name":  "Username",
      "TypeName":  "System.String"
   },
   {
      "IsEncrypted":  true,
      "IsOptional":  false,
      "Name":  "Password",
      "TypeName":  "System.String"
   }
   ],
   "ConnectionTypeName":  "MyModuleConnection",
   "IntegrationModuleName":  "MyModule"
}
```

## <a name="next-steps"></a>后续步骤

* 有关使用 Azure PowerShell 模块的详细信息，请参阅[使用 Azure PowerShell 入门](https://docs.microsoft.com/powershell/azure/get-started-azureps?view=azps-3.7.0)。
* 要了解有关创建 PowerShell 模块的更多信息，请参阅[编写 Windows PowerShell 模块](https://docs.microsoft.com/powershell/scripting/developer/module/writing-a-windows-powershell-module?view=powershell-7)。
