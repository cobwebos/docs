---
title: 在 Azure 自动化中管理模块
description: 本文介绍如何在 Azure 自动化中管理模块。
services: automation
ms.service: automation
ms.subservice: shared-resources
author: bobbytreed
ms.author: robreed
ms.date: 06/05/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 69817d1412aa13d0e7983aa3ad27c15e59185432
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478176"
---
# <a name="manage-modules-in-azure-automation"></a>在 Azure 自动化中管理模块

Azure 自动化提供相应的功能用于将 PowerShell 模块导入到基于 PowerShell 的 Runbook 所用的自动化帐户。 这些模块可以是已创建，从 PowerShell 库的自定义模块或适用于 Azure 的 AzureRM 和 Az 模块。 创建自动化帐户时一些模块是默认情况下导入。

## <a name="import-modules"></a>导入模块

可通过多种方法将模块导入到自动化帐户。 以下部分介绍了不同的模块导入方法。

> [!NOTE]
> 在 Azure 自动化中使用的模块中文件的最大路径长度为 140 个字符。 如果任何路径的长度超过 140 个字符，则无法使用 `Import-Module` 将其导入到 PowerShell 会话。

### <a name="powershell"></a>PowerShell

可以使用 [New-AzureRmAutomationModule](/powershell/module/azurerm.automation/new-azurermautomationmodule) 将模块导入到自动化帐户。 该 cmdlet 采用某个模块 zip 包的 URL。

```azurepowershell-interactive
New-AzureRmAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

### <a name="azure-portal"></a>Azure 门户

在 Azure 门户中导航到你的自动化帐户，然后选择“共享资源”下的“模块”。   单击“+ 添加模块”。  选择包含你的模块的 **.zip** 文件，然后单击“确定”开始执行导入过程。 

### <a name="powershell-gallery"></a>PowerShell 库

从 PowerShell 库模块可以是从导入[PowerShell 库](https://www.powershellgallery.com)直接或通过自动化帐户。

若要从 PowerShell 库导入模块，请转到 https://www.powershellgallery.com 并搜索你想要导入的模块。 单击**部署到 Azure 自动化**上**Azure 自动化**选项卡上的**安装选项**。 此操作将打开 Azure 门户。 上**导入**页上，选择自动化帐户并单击**确定**。

![PowerShell 库导入模块](../media/modules/powershell-gallery.png)

此外可以直接从你的自动化帐户从 PowerShell 库导模块。 在自动化帐户中，选择**模块**下**共享资源**。 在模块页上，单击**浏览库**。 此操作将打开**浏览库**页。 此页可用于模块在 PowerShell 库中搜索。 选择你想要导入和单击的模块**导入**。 上**导入**页上，单击**确定**以启动导入过程。

![从 Azure 门户的 PowerShell 库导入](../media/modules/gallery-azure-portal.png)

## <a name="delete-modules"></a>删除模块

如果具有模块的问题或需要回滚到以前版本的模块，则可以在自动化帐户中将其删除。 不能删除的原始版本[默认模块](#default-modules)创建自动化帐户时，导入。 如果你想要删除的模块是之一的较新版本[默认模块](#default-modules)安装，它将回滚到与你的自动化帐户并安装的版本。 否则，将删除在自动化帐户中删除任何模块。

### <a name="azure-portal"></a>Azure 门户

在 Azure 门户中导航到你的自动化帐户，然后选择“共享资源”下的“模块”。   选择你想要删除的模块。 上**模块**页上，clcick**删除**。 如果此模块是之一[默认模块](#default-modules)则会将其回滚到已创建自动化帐户时存在的版本。

### <a name="powershell"></a>PowerShell

若要删除通过 PowerShell 模块，请运行以下命令：

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="internal-cmdlets"></a>内部 cmdlet

下面是导入到每个自动化帐户的内部 `Orchestrator.AssetManagement.Cmdlets` 模块中的 cmdlet 列表。 可在 Runbook 和 DSC 配置中访问这些 cmdlet，使用它们可与自动化帐户中的资产进行交互。 此外，使用内部 cmdlet 还可以从加密的“变量”值、“凭据”和加密的“连接”字段中检索机密。    Azure PowerShell cmdlet 无法检索这些机密。 无需隐式连接到 Azure 即可使用这些 cmdlet。 如果需要使用某个连接（例如运行方式帐户）在 Azure 中进行身份验证，则这些内部 cmdlet 就非常有利。

|名称|描述|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Start-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Wait-AutomationJob|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

## <a name="add-a-connection-type-to-your-module"></a>将连接类型添加到模块

可以通过将一个可选文件添加到模块来提供自定义的[连接类型](../automation-connections.md)，以便在自动化帐户中使用。 此文件是一个元数据文件，用于指定要在自动化帐户中配合模块 cmdlet 使用的 Azure 自动化连接类型。 若要实现此目的，首先必须知道如何创作 PowerShell 模块。 有关模块创作的详细信息，请参阅[如何编写 PowerShell 脚本模块](/powershell/developer/module/how-to-write-a-powershell-script-module)。

![在 Azure 门户中使用自定义连接](../media/modules/connection-create-new.png)

若要添加 Azure 自动化连接类型，模块必须包含用于指定连接类型属性的名为 `<ModuleName>-Automation.json` 的文件。 这是一个 json 文件，它位于 .zip 压缩文件的 module 文件夹中。 此文件包含连接到模块所代表的系统或服务所需的连接的字段。 此配置最终会在 Azure 自动化中创建一个连接类型。 可以使用此文件为模块的连接类型设置字段名称、类型，以及这些字段是否应加密或可选。 以下示例是一个采用 JSON 文件格式的模板，用于定义用户名和密码属性：

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

创作一个要在 Azure 自动化中使用的 PowerShell 模块时，我们建议考虑以下事项：

* 在模块中为每个 cmdlet 提供摘要、说明和帮助 URI。 可以在 PowerShell 中为 cmdlet 定义特定的帮助信息，使用户可以通过 **Get-Help** cmdlet 获取这些 cmdlet 的使用帮助。 以下示例演示如何定义要在 .psm1 模块文件中使用的摘要和帮助 URI：

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

  提供此信息可在 PowerShell 控制台中使用 **Get-Help** cmdlet 显示此帮助。 Azure 门户中也会显示此说明。

  ![集成模块帮助](../media/modules/module-activity-description.png)

* 如果模块连接到外部服务，则它应该包含一个[连接类型](#add-a-connection-type-to-your-module)。 模块中的每个 cmdlet 都应能够使用连接对象（该连接类型的实例）作为参数。 这让用户能够在每次调用 cmdlet 时将连接资产的参数映射到 cmdlet 的相应参数。 根据上面的 Runbook 示例，该模块使用名为 ContosoConnection 的示例 Contoso 连接资产来访问 Contoso 资源，并从外部服务返回数据。

  在以下示例中，字段将映射到 `PSCredential` 对象的 UserName 和 Password 属性，然后传递给 cmdlet。

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

  若要启用 cmdlet 的类似上述示例的行为，可以允许 cmdlet 以参数的形式直接接受连接对象，而不是只接受连接字段作为参数。 通常情况下，需要为每个 cmdlet 提供一个参数集，这样不使用 Azure 自动化的用户在调用 cmdlet 时就不需要构造一个哈希表来充当连接对象。 参数集 `UserAccount` 用于传递连接字段属性。 `ConnectionObject` 用于一直传递连接。

* 在模块中定义所有 cmdlet 的输出类型。 定义 cmdlet 的输出类型以后，就可以利用设计时 IntelliSense 来确定 cmdlet 的输出属性，供创作时使用。 这在自动化 runbook 的图形创作过程中特别有用，这种创作过程要求掌握一定程度的设计时知识，以改进用户对模块的使用体验。

  可以通过添加 `[OutputType([<MyOutputType>])]`（其中，MyOutputType 是有效类型）来实现此目的。 若要详细了解 OutputType，请参阅[关于函数 OutputTypeAttribute](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute)。 以下代码示例演示如何将 `OutputType` 添加到 cmdlet：

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

* 该模块应完全包含在能够进行 xcopy 操作的包中。 需要执行 Runbook 时，Azure 自动化模块将分发到自动化沙盒中。 这些模块需要独立于它们在其上运行的​​主机工作。 你应能够压缩并移动模块包，并在导入到其他主机的 PowerShell 环境时使其正常运行。 为了实现这一点，模块不应该依赖于模块文件夹以外的任何文件。 此文件夹是将模块导入 Azure 自动化时压缩的文件夹。 该模块还不应依赖于主机上的任何唯一注册表设置，例如安装产品时设置的那些设置。 该模块中所有文件的路径长度应小于 140 个字符。 长度超过 140 个字符的任何路径将导致导入 Runbook 时出现问题。 如果不遵循此最佳做法，则无法在 Azure 自动化中使用该模块。  

* 如果在模块中引用 [Azure Powershell Az 模块](/powershell/azure/new-azureps-module-az?view=azps-1.1.0)，请确保没有同时引用 `AzureRM`。 `Az` 模块不能与 `AzureRM` 模块一起使用。 Runbook 支持 `Az`，但默认情况下不会导入。 若要了解 `Az` 模块和需要考虑的注意事项，请参阅 [Azure 自动化中的 Az 模块支持](../az-modules.md)。

## <a name="default-modules"></a>默认模块

下表列出了创建自动化帐户时默认情况下导入的模块。 下面列出的模块可以有较新版本的这些导入，但原始版本可以从你的自动化帐户中删除，即使你删除它们的较新版本。

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
| Orchestrator.AssetManagement.Cmdlets | 第 |
| PSDscResources | 2.9.0.0 |
| SecurityPolicyDsc | 2.1.0.0 |
| StateConfigCompositeResources | 第 |
| xDSCDomainjoin | 1.1 |
| xPowerShellExecutionPolicy | 1.1.0.0 |
| xRemoteDesktopAdmin | 1.1.0.0 |

## <a name="next-steps"></a>后续步骤

* 若要详细了解如何创建 PowerShell 模块，请参阅 [编写 Windows PowerShell 模块](https://msdn.microsoft.com/library/dd878310%28v=vs.85%29.aspx)