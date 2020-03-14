---
title: 在 Azure 自动化中管理模块
description: 本文介绍如何在 Azure 自动化中管理模块
services: automation
ms.service: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: e300bc0f29808215673407d21b65fe329e50ad45
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79278332"
---
# <a name="manage-modules-in-azure-automation"></a>在 Azure 自动化中管理模块

Azure Automation 提供将 PowerShell 模块导入到自动化帐户中的功能，供基于 PowerShell 的 runbook 使用。 这些模块可以是你创建的自定义模块、PowerShell 库或 Azure 的 AzureRM 和 Az 模块。 创建自动化帐户时，默认情况下会导入某些模块。

## <a name="import-modules"></a>导入模块

可以通过多种方式将模块导入到自动化帐户中。 以下各节介绍了导入模块的不同方法。

> [!NOTE]
> 要在 Azure 自动化中使用的模块中文件的最大路径为140个字符。 超过140个字符的任何路径都无法与 `Import-Module`一起导入 PowerShell 会话。

### <a name="powershell"></a>PowerShell

可以使用[AzureRmAutomationModule](/powershell/module/azurerm.automation/new-azurermautomationmodule)将模块导入到自动化帐户中。 此 cmdlet 采用模块 zip 包的 url。

```azurepowershell-interactive
New-AzureRmAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

你还可以使用同一 cmdlet 直接从 PowerShell 库导入模块。 请确保从[PowerShell 库](https://www.powershellgallery.com)获取**ModuleName**和**ModuleVersion** 。

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

### <a name="azure-portal"></a>Azure 门户

在 Azure 门户中，导航到自动化帐户，并选择 "**共享资源**" 下的 "**模块**"。 单击 " **+ 添加模块**"。 选择包含模块的 **.zip**文件，然后单击 **"确定"** 以开始导入过程。

### <a name="powershell-gallery"></a>PowerShell 库

PowerShell 库中的模块可以直接从[PowerShell 库](https://www.powershellgallery.com)导入，也可以从自动化帐户导入。

若要从 PowerShell 库导入模块，请参阅 https://www.powershellgallery.com 并搜索要导入的模块。 单击 "**安装选项**" 下的 " **azure 自动化**" 选项卡上的 "**部署到 azure 自动化**"。 此操作会打开 Azure 门户。 在 "**导入**" 页上，选择自动化帐户，然后单击 **"确定"** 。

![PowerShell 库导入模块](../media/modules/powershell-gallery.png)

你还可以直接从你的自动化帐户中导入来自 PowerShell 库的模块。 在自动化帐户中，选择 "**共享资源**" 下的 "**模块**"。 在 "模块" 页上，单击 "**浏览库**"，然后搜索模块的 PowerShell 库。 选择要导入的模块，然后单击 "**导入**"。 在 "**导入**" 页上，单击 **"确定"** 以开始导入过程。

![从 Azure 门户 PowerShell 库导入](../media/modules/gallery-azure-portal.png)

## <a name="delete-modules"></a>删除模块

如果你遇到了某个模块的问题，或者需要回滚到以前版本的模块，则可以将其从自动化帐户中删除。 你无法删除在创建自动化帐户时导入的[默认模块](#default-modules)的原始版本。 如果要删除的模块是安装的其中一个[默认模块](#default-modules)的更新版本，则它将回滚到随你的自动化帐户一起安装的版本。 否则，将删除从自动化帐户中删除的任何模块。

### <a name="azure-portal"></a>Azure 门户

在 Azure 门户中，导航到自动化帐户，并选择 "**共享资源**" 下的 "**模块**"。 选择要删除的模块。 在 "**模块**" 页上，选择 "**删除**"。 如果此模块是[默认模块](#default-modules)之一，则会将其回滚到创建自动化帐户时提供的版本。

### <a name="powershell"></a>PowerShell

若要通过 PowerShell 删除模块，请运行以下命令：

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="internal-cmdlets"></a>内部 cmdlet

下面列出了导入到每个 Automation 帐户的内部 `Orchestrator.AssetManagement.Cmdlets` 模块中的 cmdlet。 这些 cmdlet 可在 runbook 和 DSC 配置中访问，并可用于与自动化帐户中的资产交互。 此外，内部 cmdlet 允许您从加密的**变量**值、**凭据**和加密的**连接**字段检索机密。 Azure PowerShell cmdlet 无法检索这些机密。 使用这些 cmdlet 时，不需要使用运行方式帐户进行 Azure 身份验证，就可以在使用 Azure 时隐式连接到 Azure。

>[!NOTE]
>这些内部 cmdlet 在 Windows 混合 Runbook 辅助角色上可用，在 Linux 混合 Runbook 辅助角色上不可用。 为直接在计算机上运行的 runbook 或环境中的资源使用相应的[AzureRM](https://docs.microsoft.com/powershell/module/AzureRM.Automation/?view=azurermps-6.13.0)或[Az 模块](../az-modules.md)。 
>

|名称|说明|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Start-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Wait-AutomationJob|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

## <a name="add-a-connection-type-to-your-module"></a>将连接类型添加到模块

可以通过将可选文件添加到模块，提供自定义[连接类型](../automation-connections.md)，以便在自动化帐户中使用。 此文件是一个元数据文件，用于指定要与自动化帐户中的模块 cmdlet 一起使用的 Azure 自动化连接类型。 若要实现此目的，必须首先了解如何编写 PowerShell 模块。 有关模块创作的详细信息，请参阅[如何编写 PowerShell 脚本模块](/powershell/scripting/developer/module/how-to-write-a-powershell-script-module)。

![在 Azure 门户中使用自定义连接](../media/modules/connection-create-new.png)

若要添加 Azure 自动化连接类型，模块必须包含名称为 `<ModuleName>-Automation.json` 的文件，该文件指定连接类型属性。 Json 文件放置在压缩的 .zip 文件的模块文件夹中。 此文件包含连接到模块所代表的系统或服务所需的连接字段。 该配置最终会在 Azure 自动化中创建连接类型。 使用此文件，你可以为模块的连接类型设置字段名称、类型以及字段是否应加密或可选。 下面的示例是一个采用 json 文件格式的模板，用于定义用户名和密码属性：

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

## <a name="module-best-practices"></a>模块最佳做法

PowerShell 模块可导入到 Azure 自动化中，这样其 cmdlet 就可以在 runbook 中使用，其 DSC 资源就可以在 DSC 配置中使用。 Azure 自动化在后台存储这些模块，在执行 runbook 作业和 DSC 编译作业时将其载入 Azure 自动化沙盒中，并在其中执行 Runbook 以及编译 DSC 配置。 模块中的所有 DSC 资源还会自动放置在 Automation DSC 拉取服务器上。 它们可以在应用 DSC 配置时被计算机拉取。

建议在创作 PowerShell 模块时考虑以下事项：

* 不要在 .zip 包中包含版本文件夹。  此问题不太关心 runbook，但会导致状态配置服务出现问题。  当模块分发到由 DSC 管理的节点时，Azure 自动化将自动创建版本文件夹，如果版本文件夹存在，你将会得到两个实例。  DSC 模块的示例文件夹结构：

```powershell
myModule
  - DSCResources
    - myResourceFolder
      myResourceModule.psm1
      myResourceSchema.mof
  myModuleManifest.psd1
```

* 在模块中为每个 cmdlet 提供摘要、说明和帮助 URI。 可以在 PowerShell 中为 cmdlet 定义特定的帮助信息，使用户可以通过 **Get-Help** cmdlet 获取这些 cmdlet 的使用帮助。 下面的示例演示如何在 hbase-runner.psm1 模块文件中定义的概要和 help URI：

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

  提供此信息可在 PowerShell 控制台中使用 **Get-Help** cmdlet 显示此帮助。 此描述也会显示在 Azure 门户中。

  ![集成模块帮助](../media/modules/module-activity-description.png)

* 如果模块连接到外部服务，则它应包含[连接类型](#add-a-connection-type-to-your-module)。 模块中的每个 cmdlet 都应能够使用连接对象（该连接类型的实例）作为参数。 每次调用 cmdlet 时，用户都将连接资产的参数映射到 cmdlet 的相应参数。 基于以上 runbook 示例，它使用名为 ContosoConnection 的示例 Contoso 连接资产访问 Contoso 资源并从外部服务返回数据。

  在下面的示例中，字段被映射到 `PSCredential` 对象的用户名和密码属性，然后传递给 cmdlet。

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

  更简单更好的方法方法是直接将连接对象传递给 cmdlet：

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

  若要启用 cmdlet 的类似上述示例的行为，可以允许 cmdlet 以参数的形式直接接受连接对象，而不是只接受连接字段作为参数。 通常情况下，需要为每个 cmdlet 提供一个参数集，这样不使用 Azure 自动化的用户在调用 cmdlet 时就不需要构造一个哈希表来充当连接对象。 参数集 `UserAccount`用于传递连接字段属性。 `ConnectionObject` 使你可以直接传递连接。

* 在模块中定义所有 cmdlet 的输出类型。 定义 cmdlet 的输出类型以后，就可以利用设计时 IntelliSense 来确定 cmdlet 的输出属性，供创作时使用。 这在自动化 runbook 的图形创作过程中特别有用，这种创作过程要求掌握一定程度的设计时知识，以改进用户对模块的使用体验。

添加 `[OutputType([<MyOutputType>])]`，其中 MyOutputType 为有效类型。 若要了解有关 OutputType 的详细信息，请参阅[关于函数 OutputTypeAttribute](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute)。 下面的代码是将 `OutputType` 添加到 cmdlet 的示例：

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

* 使模块中的所有 cmdlet 以无状态方式呈现。 多个 runbook 作业可以同时在相同的 AppDomain 和相同的进程和沙盒中运行。 如果在这些级别上共享了任何状态，则作业可能会相互影响。 这种行为可能导致问题的诊断出现间歇和困难。  下面是应避免的处理方式的示例。

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

* 该模块应完全包含在能够进行 xcopy 的包中。 需要执行 runbook 时，Azure 自动化模块会分发到自动化沙盒。 这些模块需要独立于它们在其上运行的​​主机工作。 你应能够压缩并移动模块包，并在导入到其他主机的 PowerShell 环境时使其正常运行。 为了实现这一点，模块不应该依赖于模块文件夹以外的任何文件。 此文件夹是将模块导入 Azure 自动化时压缩的文件夹。 该模块还不应依赖于主机上的任何唯一注册表设置，例如安装产品时设置的那些设置。 模块中的所有文件的路径应少于140个字符。 超过140个字符的任何路径都将导致导入 runbook 时出现问题。 如果不遵循此最佳做法，则无法在 Azure 自动化中使用该模块。  

* 如果在模块中引用 [Azure Powershell Az 模块](/powershell/azure/new-azureps-module-az?view=azps-1.1.0)，请确保没有同时引用 `AzureRM`。 `Az` 模块不能与 `AzureRM` 模块一起使用。 Runbook 支持 `Az`，但默认情况下不会导入。 若要了解 `Az` 模块和需要考虑的注意事项，请参阅 [Azure 自动化中的 Az 模块支持](../az-modules.md)。

## <a name="default-modules"></a>默认模块

下表列出了在创建自动化帐户时默认情况下导入的模块。 下面列出的模块可以导入更新的版本，但不能从自动化帐户中删除原始版本，即使删除了更新版本也是如此。

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

## <a name="next-steps"></a>后续步骤

* 若要详细了解如何创建 PowerShell 模块，请参阅 [编写 Windows PowerShell 模块](/powershell/scripting/developer/windows-powershell)
