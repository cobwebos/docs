---
title: 管理 Azure 自动化中的模块
description: 本文介绍如何管理 Azure 自动化中的模块
services: automation
ms.service: automation
ms.subservice: shared-resources
author: georgewallace
ms.author: gwallace
ms.date: 03/13/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: fa7f5d3fb38eb1dbca51dec9b73dca3c998436aa
ms.sourcegitcommit: b8f9200112cae265155b8877f7e1621c4bcc53fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/14/2019
ms.locfileid: "57905297"
---
# <a name="manage-modules-in-azure-automation"></a>管理 Azure 自动化中的模块

Azure 自动化提供 PowerShell 模块导入到自动化帐户通过基于 PowerShell runbook 使用的功能。 这些模块可以是已创建，从 PowerShell 库的自定义模块或适用于 Azure 的 AzureRM 和 Az 模块。

## <a name="import-modules"></a>导入模块

有多个方法，可以导入自动化帐户中的模块。 以下部分说明导入模块的不同方法。

> [!NOTE]
> 要在 Azure 自动化中使用的模块中的文件的最大路径为 140 个字符。 任何超过 140 个字符的路径将不能导入到 PowerShell 会话`Import-Module`。

### <a name="powershell"></a>PowerShell

可以使用[New-azurermautomationmodule](/powershell/module/azurerm.automation/new-azurermautomationmodule)将模块导入自动化帐户。 该 cmdlet 会转到模块的 zip 包的 url。

```azurepowershell-interactive
New-AzureRmAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

### <a name="azure-portal"></a>Azure 门户

在 Azure 门户中，导航到自动化帐户并选择**模块**下**共享资源**。 单击 **+ 添加模块**。 选择 **.zip**文件，其中包含模块，单击**确定**若要开始导入过程。

### <a name="powershell-gallery"></a>PowerShell 库

从 PowerShell 库模块可以是从导入[PowerShell 库](https://www.powershellgallery.com)直接或通过自动化帐户。

若要从 PowerShell 库导入模块，请转到 https://www.powershellgallery.com并搜索你想要导入的模块。 单击**部署到 Azure 自动化**上**Azure 自动化**选项卡上的**安装选项**。 此操作将打开 Azure 门户。 上**导入**页上，选择自动化帐户并单击**确定**。

![PowerShell 库导入模块](../media/modules/powershell-gallery.png)

此外可以直接从你的自动化帐户从 PowerShell 库导模块。 在自动化帐户中，选择**模块**下**共享资源**。 在模块页上，单击**浏览库**。 此操作将打开**浏览库**页。 此页可用于模块在 PowerShell 库中搜索。 选择你想要导入和单击的模块**导入**。 上**导入**页上，单击**确定**以启动导入过程。

![从 Azure 门户的 PowerShell 库导入](../media/modules/gallery-azure-portal.png)

## <a name="internal-cmdlets"></a>内部 cmdlet

以下是在内部的 cmdlet 的列表`Orchestrator.AssetManagement.Cmdlets`模块导入到每个自动化帐户。 这些 cmdlet 可在 runbook 和 DSC 配置中访问，并允许您与您在自动化帐户中的资产进行交互。 此外，内部 cmdlet 允许你检索密钥从加密**变量**值，**凭据**，并加密**连接**字段。 Azure PowerShell cmdlet 将无法检索这些机密。 这些 cmdlet 不需要您使用它们时隐式连接到 Azure。 这是必须的连接，例如运行方式帐户需要使用向 Azure 进行身份验证的方案很有用。

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

可以提供自定义[连接类型](../automation-connections.md)供你使用在你的自动化帐户中，通过将可选文件添加到你的模块。 此文件是指定的 Azure 自动化连接类型与自动化帐户中的模块的 cmdlet 一起使用的元数据文件。 若要实现此目的必须首先知道如何创作一个 PowerShell 模块。 模块创作的详细信息，请参阅[如何编写 PowerShell 脚本模块](/powershell/developer/module/how-to-write-a-powershell-script-module)。

![在 Azure 门户中使用自定义连接](../media/modules/connection-create-new.png)

若要添加的 Azure 自动化连接类型，你的模块必须包含名为文件`<ModuleName>-Automation.json`，它指定连接类型属性。 这是一个 json 文件，它位于 .zip 压缩文件的 module 文件夹中。 此文件包含所需连接到系统或服务模块所代表的连接的字段。 此配置最终会在 Azure 自动化中创建一个连接类型。 使用此文件可以设置字段名称、 类型和字段是否应为加密或可选的该模块的连接类型。 下面的示例是一个模板中定义的用户名和密码属性的 json 文件格式：

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

## <a name="module-best-practices"></a>模块的最佳做法

PowerShell 模块可导入到 Azure 自动化中，这样其 cmdlet 就可以在 runbook 中使用，其 DSC 资源就可以在 DSC 配置中使用。 Azure 自动化在后台存储这些模块，在执行 runbook 作业和 DSC 编译作业时将其载入 Azure 自动化沙盒中，并在其中执行 Runbook 以及编译 DSC 配置。 模块中的所有 DSC 资源还会自动放置在 Automation DSC 拉取服务器上。 它们可以在应用 DSC 配置时被计算机拉取。

我们建议你考虑使用以下创作一个 PowerShell 模块，用于在 Azure 自动化中使用时：

* 在模块中为每个 cmdlet 提供摘要、说明和帮助 URI。 可以在 PowerShell 中为 cmdlet 定义特定的帮助信息，使用户可以通过 **Get-Help** cmdlet 获取这些 cmdlet 的使用帮助。 下面的示例演示如何定义摘要和帮助 URI.psm1 模块文件中：

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

  提供此信息可在 PowerShell 控制台中使用 **Get-Help** cmdlet 显示此帮助。 在 Azure 门户中，还会显示此说明。

  ![集成模块帮助](../media/modules/module-activity-description.png)

* 如果该模块连接到外部服务时，它应包含[连接类型](#add-a-connection-type-to-your-module)。 模块中的每个 cmdlet 都应能够使用连接对象（该连接类型的实例）作为参数。 这让用户能够在每次调用 cmdlet 时将连接资产的参数映射到 cmdlet 的相应参数。 它根据上面的 runbook 示例，使用名为 ContosoConnection 示例 Contoso 连接资产来访问 Contoso 资源并且从外部服务返回的数据。

  在下面的示例中的字段映射到的用户名和密码属性`PSCredential`对象，然后传递给 cmdlet。

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

  此行为更容易且更好地访问方式直接将连接对象传递给 cmdlet:

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

  若要启用 cmdlet 的类似上述示例的行为，可以允许 cmdlet 以参数的形式直接接受连接对象，而不是只接受连接字段作为参数。 通常情况下，需要为每个 cmdlet 提供一个参数集，这样不使用 Azure 自动化的用户在调用 cmdlet 时就不需要构造一个哈希表来充当连接对象。 参数集`UserAccount`，用于传递连接字段属性。 `ConnectionObject` 可以将该连接传递通过直线。

* 在模块中定义的所有 cmdlet 的输出类型。 定义 cmdlet 的输出类型以后，就可以利用设计时 IntelliSense 来确定 cmdlet 的输出属性，供创作时使用。 这在自动化 runbook 的图形创作过程中特别有用，这种创作过程要求掌握一定程度的设计时知识，以改进用户对模块的使用体验。

  这可以通过添加`[OutputType([<MyOutputType>])]`MyOutputType 其中是有效的类型。 若要了解有关 OutputType 的详细信息，请参阅[有关函数 OutputTypeAttribute](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute)。 下面的代码是添加的示例`OutputType`给某个 cmdlet:

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

* 使模块中的所有 cmdlet 以无状态方式呈现。 在同一个 AppDomain 和相同的过程和沙盒中可以同时运行多个 runbook 作业。 如果没有在这两种级别上共享的任何状态，作业可以相互影响。 此行为可能会导致间歇性，难以诊断问题。  下面是应避免的处理方式的示例。

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

* 该模块应完全包含在可 xcopy 的包。 需要执行 Runbook 时，Azure 自动化模块将分发到自动化沙盒中。 这些模块需要独立于它们在其上运行的​​主机工作。 你应能够压缩并移动模块包，并在导入到其他主机的 PowerShell 环境时使其正常运行。 为了实现这一点，模块不应该依赖于模块文件夹以外的任何文件。 此文件夹是将模块导入 Azure 自动化时压缩的文件夹。 该模块还不应依赖于主机上的任何唯一注册表设置，例如安装产品时设置的那些设置。 该模块中的所有文件应都具有少于 140 个字符的路径。 任何路径长度超过 140 个字符将导致导入 runbook 的问题。 如果不遵循此最佳做法，则无法在 Azure 自动化中使用该模块。  

* 如果在模块中引用 [Azure Powershell Az 模块](/powershell/azure/new-azureps-module-az?view=azps-1.1.0)，请确保没有同时引用 `AzureRM`。 `Az` 模块不能与 `AzureRM` 模块一起使用。 Runbook 支持 `Az`，但默认情况下不会导入。 若要了解 `Az` 模块和需要考虑的注意事项，请参阅 [Azure 自动化中的 Az 模块支持](../az-modules.md)。

## <a name="next-steps"></a>后续步骤

* 若要详细了解如何创建 PowerShell 模块，请参阅 [编写 Windows PowerShell 模块](https://msdn.microsoft.com/library/dd878310%28v=vs.85%29.aspx)