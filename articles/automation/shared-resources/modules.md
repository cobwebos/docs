---
title: 在 Azure 自动化中管理模块
description: 本文介绍如何在 Azure 自动化中管理模块。
services: automation
ms.service: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 605d1bc72406a9aeecc9273f9bd2d7fd2b30ab11
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618693"
---
# <a name="manage-modules-in-azure-automation"></a>在 Azure 自动化中管理模块

您可以将 PowerShell 模块导入 Azure 自动化，使其 cmdlet 在 Runbook 及其 DSC 资源中可用 DSC 配置。 在幕后，Azure 自动化存储这些模块。 在 Runbook 作业和 DSC 编译作业执行时间，自动化将它们加载到 Azure 自动化沙盒中，其中运行簿执行和 DSC 配置编译。 模块中的所有 DSC 资源还会自动放置在 Automation DSC 拉取服务器上。 计算机在应用 DSC 配置时可以拔取它们。

Azure 自动化中使用的模块可以是已创建的自定义模块、PowerShell 库中的模块或 Azure 的 AzureRM 和 Az 模块。 创建自动化帐户时，默认情况下将导入某些模块。

## <a name="default-modules"></a>默认模块

下表列出了默认情况下在创建自动化帐户时导入的模块。 自动化可以导入这些模块的较新版本。 但是，即使您删除了较新版本，也不能从自动化帐户中删除原始版本。

|模块名称|Version|
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

## <a name="internal-cmdlets"></a>内部 cmdlet

下表列出了导入到每个自动化帐户的内部`Orchestrator.AssetManagement.Cmdlets`模块中的 cmdlet。 这些 cmdlet 可在 Runbook 和 DSC 配置中访问，并允许您与自动化帐户中的资产进行交互。 此外，内部 cmdlet 允许您从加密的变量、凭据和加密连接中检索机密。 Azure PowerShell cmdlet 无法检索这些机密。 这些 cmdlet 不需要在使用它们时隐式连接到 Azure，就像使用"运行作为帐户"对 Azure 进行身份验证时一样。

>[!NOTE]
>这些内部 cmdlet 在 Windows 混合 Runbook 辅助角色上可用，但在 Linux 混合 Runbook 辅助功能程序上不可用。 对于直接在计算机上运行的 Runbook 或针对环境中的资源，请使用相应的[AzureRM.自动化](https://docs.microsoft.com/powershell/module/AzureRM.Automation/?view=azurermps-6.13.0)或[Az 模块](../az-modules.md)cmdlet。 

|“属性”|描述|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Start-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Wait-AutomationJob|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

## <a name="importing-modules"></a>导入模块

您可以通过多种方式将模块导入自动化帐户。 以下部分介绍了不同的模块导入方法。

> [!NOTE]
> Azure 自动化中使用的模块中文件的最大路径大小为 140 个字符。 自动化无法将路径大小超过 140 个字符的文件导入到 具有 的 PowerShell 会话中`Import-Module`。

### <a name="import-modules-in-azure-portal"></a>Azure 门户中的导入模块

要在 Azure 门户中导入模块，请参阅：

1. 导航到自动化帐户。
2. 在**共享资源**下选择**模块**。
3. 单击 **"添加模块**"。 
4. 选择包含模块的 **.zip**文件。
5. 单击 **"确定**"开始导入过程。

### <a name="import-modules-using-powershell"></a>使用 PowerShell 导入模块

您可以使用["新建 AzureRm 自动化模块](/powershell/module/azurerm.automation/new-azurermautomationmodule)cmdlet"将模块导入自动化帐户。 cmdlet 获取模块 .zip 包的 URL。

```azurepowershell-interactive
New-AzureRmAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

您还可以使用相同的 cmdlet 直接从 PowerShell 库导入模块。 一定要抓住`ModuleName`并从`ModuleVersion`[PowerShell画廊](https://www.powershellgallery.com)。

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzureRmAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```
### <a name="import-modules-from-powershell-gallery"></a>从 PowerShell 库导入模块

您可以直接从库或从自动化帐户导入[PowerShell 库](https://www.powershellgallery.com)模块。

要直接从 PowerShell 库导入模块：

1. 转到https://www.powershellgallery.com并搜索要导入的模块。
2. 单击 **"在****"安装选项**"下的**Azure 自动化**选项卡上部署到 Azure 自动化。 此操作会打开 Azure 门户。 
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
Remove-AzureRmAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```
## <a name="adding-a-connection-type-to-your-module"></a>向模块添加连接类型

您可以通过向模块添加可选元数据文件，提供要在自动化帐户中使用的自定义[连接类型](../automation-connections.md)。 此文件指定要与自动化帐户中的模块 cmdlet 一起使用的 Azure 自动化连接类型。 为此，您必须首先了解如何编写 PowerShell 模块。 请参阅[如何编写 PowerShell 脚本模块](/powershell/scripting/developer/module/how-to-write-a-powershell-script-module)。

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

## <a name="best-practices-for-authoring-modules"></a>编写模块的最佳做法

我们建议您在编写 PowerShell 模块以在 Azure 自动化中使用时遵循本节中的注意事项。

### <a name="version-folder"></a>版本文件夹

请勿在模块的 **.zip**包中包含版本文件夹。  此问题不太适合 Runbook，但确实会导致状态配置服务出现问题。 当模块分发到由 DSC 管理的节点时，Azure 自动化会自动创建版本文件夹。 如果存在版本文件夹，则最终有两个实例。 下面是 DSC 模块的示例文件夹结构：

```powershell
myModule
  - DSCResources
    - myResourceFolder
      myResourceModule.psm1
      myResourceSchema.mof
  myModuleManifest.psd1
```

### <a name="help-information"></a>帮助信息

包括模块中每个 cmdlet 的概要、说明和帮助 URI。 在 PowerShell 中，您可以使用`Get-Help`cmdlet 为 cmdlet 定义帮助信息。 下面的示例演示如何在 **.psm1**模块文件中定义概要并帮助 URI：

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

如果模块连接到外部服务，请定义[连接类型](#adding-a-connection-type-to-your-module)。 模块中的每个 cmdlet 都应接受连接对象（该连接类型的实例）作为参数。 用户每次调用 cmdlet 时都会将连接资产的参数映射到 cmdlet 的相应参数。 基于上面的 Runbook 示例，它使用调用`ContosoConnection`的 Contoso 连接资产的示例来访问 Contoso 资源并从外部服务返回数据。

  在下面的示例中，字段映射到`UserName``Password``PSCredential`对象的 和 属性，然后传递到 cmdlet。

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

  您可以通过允许 cmdlet 直接接受连接对象作为参数，而不是仅为参数接受连接字段，从而为 cmdlet 启用类似的行为。 通常情况下，需要为每个 cmdlet 提供一个参数集，这样不使用 Azure 自动化的用户在调用 cmdlet 时就不需要构造一个哈希表来充当连接对象。 参数集 `UserAccount` 用于传递连接字段属性。 `ConnectionObject` 用于一直传递连接。

### <a name="output-type"></a>输出类型

定义模块中所有 cmdlet 的输出类型。 定义 cmdlet 的输出类型以后，就可以利用设计时 IntelliSense 来确定 cmdlet 的输出属性，供创作时使用。 在自动化 Runbook 图形创作期间，这种做法尤其有用，为此，设计时间知识是轻松体验模块的关键。

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

确保模块完全包含在可 xcopy 的封装中。 执行 Runbook 时，Azure 自动化模块将分发到自动化沙盒。 模块必须独立于运行它们的主机工作。 

您应该能够压缩和移动模块包，并在导入到另一个主机的 PowerShell 环境中时使其正常工作。 为此，请确保模块不依赖于模块文件夹外的任何文件，这些文件在将模块导入 Azure 自动化时压缩。 

您的模块不应依赖于主机上的任何唯一注册表设置。 例如，安装产品时所做的设置。 

确保模块中的所有文件具有少于 140 个字符的路径。 任何超过 140 个字符的路径都会导致导入 Runbook 出现问题。 如果不遵循此最佳实践，该模块在 Azure 自动化中不可用。  

### <a name="references-to-azurerm-and-az"></a>对 AzureRM 和 Az 的引用

如果在模块中引用[Azure PowerShell Az 模块](/powershell/azure/new-azureps-module-az?view=azps-1.1.0)，请确保不引用 AzureRM。 不能将 Az 模块与 AzureRM 模块结合使用。 在 Runbook 中支持 Az，但默认情况下不导入。 要了解 Az 模块和注意事项，请参阅[Azure 自动化 中的 Az 模块支持](../az-modules.md)。

## <a name="next-steps"></a>后续步骤

* 要了解有关创建 PowerShell 模块的更多信息，请参阅[编写 Windows PowerShell 模块](/powershell/scripting/developer/windows-powershell)。
