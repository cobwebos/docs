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
ms.openlocfilehash: 859eea66d10e07a3503e33166bc77c8a97577acd
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548939"
---
# <a name="manage-modules-in-azure-automation"></a>在 Azure 自动化中管理模块

Azure 自动化允许将 PowerShell 模块导入自动化帐户，以便基于 PowerShell 的 Runbook 使用。 这些模块可以是已创建的自定义模块、PowerShell 库中的模块或 Azure 的 AzureRM 和 Az 模块。 创建自动化帐户时，默认情况下将导入某些模块。

## <a name="import-modules"></a>导入模块

您可以通过多种方式将模块导入自动化帐户。 以下部分介绍了不同的模块导入方法。

> [!NOTE]
> Azure 自动化中使用的模块中文件的最大路径大小为 140 个字符。 自动化无法将路径大小超过 140 个字符的文件导入到 具有 的 PowerShell 会话中`Import-Module`。

### <a name="powershell"></a>PowerShell

您可以使用["新建 AzureRm 自动化模块](/powershell/module/azurerm.automation/new-azurermautomationmodule)cmdlet"将模块导入自动化帐户。 cmdlet 获取模块 .zip 包的 URL。

```azurepowershell-interactive
New-AzureRmAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

您还可以使用相同的 cmdlet 直接从 PowerShell 库导入模块。 一定要抓住`ModuleName`并从`ModuleVersion`[PowerShell画廊](https://www.powershellgallery.com)。

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

### <a name="azure-portal"></a>Azure 门户

要在 Azure 门户中导入模块，请参阅：

1. 导航到自动化帐户。
2. 在**共享资源**下选择**模块**。
3. 单击 **"添加模块**"。 
4. 选择包含模块的 **.zip**文件。
5. 单击 **"确定**"开始导入过程。

### <a name="powershell-gallery"></a>PowerShell 库

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

## <a name="delete-modules"></a>删除模块

如果模块出现问题或需要回滚到模块的早期版本，则可以将其从自动化帐户中删除。 无法删除创建自动化帐户时导入的[默认模块](#default-modules)的原始版本。 如果要删除的模块是[默认模块](#default-modules)之一的较新版本，则它回滚到与自动化帐户一起安装的版本。 否则，您从自动化帐户中删除的任何模块都将被删除。

### <a name="azure-portal"></a>Azure 门户

要删除 Azure 门户中的模块，请执行以下工作：

1. 导航到您的自动化帐户，并在**共享资源**下选择**模块**。 
2. 选择要删除的模块。 
3. 在 **"模块"** 页上，选择 **"删除**"。 如果此模块是[默认模块](#default-modules)之一，它将回滚到创建自动化帐户时存在的版本。

### <a name="powershell"></a>PowerShell

要通过 PowerShell 删除模块，请运行以下命令：

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="internal-cmdlets"></a>内部 cmdlet

下表列出了导入到每个自动化帐户的内部`Orchestrator.AssetManagement.Cmdlets`模块中的 cmdlet。 这些 cmdlet 可在 Runbook 和 DSC 配置中访问，并允许您与自动化帐户中的资产进行交互。 此外，内部 cmdlet 允许您从加密的变量、凭据和加密连接中检索机密。 Azure PowerShell cmdlet 无法检索这些机密。 这些 cmdlet 不需要在使用它们时隐式连接到 Azure，就像使用"运行作为帐户"对 Azure 进行身份验证时一样。

>[!NOTE]
>这些内部 cmdlet 在 Windows 混合 Runbook 辅助角色上可用，但在 Linux 混合 Runbook 辅助功能程序上不可用。 对于直接在计算机上运行的 Runbook 或针对环境中的资源，请使用相应的[AzureRM.自动化](https://docs.microsoft.com/powershell/module/AzureRM.Automation/?view=azurermps-6.13.0)或[Az 模块](../az-modules.md)cmdlet。 

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

## <a name="module-best-practices"></a>模块最佳做法

您可以将 PowerShell 模块导入 Azure 自动化，使其 cmdlet 在 Runbook 中可用，并在 DSC 配置中提供其 DSC 资源。 在幕后，Azure 自动化存储这些模块。 在 Runbook 作业和 DSC 编译作业执行时间，自动化将它们加载到 Azure 自动化沙盒中，其中运行簿执行和 DSC 配置编译。 模块中的所有 DSC 资源还会自动放置在 Automation DSC 拉取服务器上。 计算机在应用 DSC 配置时可以拔取它们。

我们建议您在创作用于 Azure 自动化的 PowerShell 模块时考虑以下事项：

* 请不要在 **.zip**包中包含版本文件夹。  此问题不太适合 Runbook，但确实会导致状态配置服务出现问题。 当模块分发到由 DSC 管理的节点时，Azure 自动化会自动创建版本文件夹。 如果存在版本文件夹，则最终有两个实例。 下面是 DSC 模块的示例文件夹结构：

```powershell
myModule
  - DSCResources
    - myResourceFolder
      myResourceModule.psm1
      myResourceSchema.mof
  myModuleManifest.psd1
```

* 在模块中为每个 cmdlet 提供摘要、说明和帮助 URI。 在 PowerShell 中，您可以使用`Get-Help`cmdlet 为 cmdlet 定义帮助信息。 下面的示例演示如何在 **.psm1**模块文件中定义概要并帮助 URI：

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

* 如果模块连接到外部服务，请定义[连接类型](#add-a-connection-type-to-your-module)。 模块中的每个 cmdlet 都应接受连接对象（该连接类型的实例）作为参数。 用户每次调用 cmdlet 时都会将连接资产的参数映射到 cmdlet 的相应参数。 基于上面的 Runbook 示例，它使用调用`ContosoConnection`的 Contoso 连接资产的示例来访问 Contoso 资源并从外部服务返回数据。

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

* 在模块中定义所有 cmdlet 的输出类型。 定义 cmdlet 的输出类型以后，就可以利用设计时 IntelliSense 来确定 cmdlet 的输出属性，供创作时使用。 这在自动化 runbook 的图形创作过程中特别有用，这种创作过程要求掌握一定程度的设计时知识，以改进用户对模块的使用体验。

添加`[OutputType([<MyOutputType>])]`"我的输出类型"的有效类型。 若要详细了解 OutputType，请参阅[关于函数 OutputTypeAttribute](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute)。 以下代码示例演示如何将 `OutputType` 添加到 cmdlet：

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

* 使模块中的所有 cmdlet 以无状态方式呈现。 多个 Runbook 作业可在同一个 AppDomain 和同一个进程与沙盒中同时运行。 如果在这两些级别共享了任何状态，则作业可能会相互影响。 此行为可能导致间歇性的且难以诊断的问题。  下面是应避免的处理方式的示例。

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

* 该模块应完全包含在能够进行 xcopy 操作的包中。 需要执行 Runbook 时，Azure 自动化模块将分发到自动化沙盒中。 这些模块需要独立于它们在其上运行的​​主机工作。 您应该能够压缩和移动模块包，并在导入到另一个主机的 PowerShell 环境中时使其正常工作。 为此，模块不应依赖于模块文件夹外部在将模块导入 Azure 自动化时压缩的任何文件。 该模块还不应依赖于主机上的任何唯一注册表设置，例如安装产品时设置的那些设置。 该模块中所有文件的路径长度应小于 140 个字符。 任何超过 140 个字符的路径都会导致导入 Runbook 时出现问题。 如果不遵循此最佳实践，该模块在 Azure 自动化中不可用。  

* 如果在模块中引用[Azure PowerShell Az 模块](/powershell/azure/new-azureps-module-az?view=azps-1.1.0)，请确保不引用 。 `AzureRM` 不能将`Az`模块与`AzureRM`模块结合使用。 `Az`在 Runbook 中受支持，但默认情况下不导入。 要了解要考虑`Az`的模块和注意事项，请参阅 Azure[自动化 中的 Az 模块支持](../az-modules.md)。

## <a name="default-modules"></a>默认模块

下表列出了默认情况下在创建自动化帐户时导入的模块。 自动化可以导入这些模块的较新版本。 但是，即使您删除了较新版本，也不能从自动化帐户中删除原始版本。

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

* 要了解有关创建 PowerShell 模块的更多信息，请参阅[编写 Windows PowerShell 模块](/powershell/scripting/developer/windows-powershell)。
