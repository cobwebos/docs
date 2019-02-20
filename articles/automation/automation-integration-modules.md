---
title: 创建 Azure 自动化集成模块
description: 本教程指导用户在 Azure 自动化中创建、测试以及通过示例方式使用集成模块。
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 01/24/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 9122cf5cc908d578d8b781c6fdc49d7b04b0ab58
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2019
ms.locfileid: "55990336"
---
# <a name="azure-automation-integration-modules"></a>Azure 自动化集成模块

PowerShell 是 Azure 自动化背后的基本技术。 由于 Azure 自动化是基于 PowerShell 构建的，因此 PowerShell 模块对于 Azure 自动化的可扩展性很重要。 在本文中，你将学习有关 Azure 自动化如何使用 PowerShell 模块（称为集成模块）的详细信息。 你还将学习创建自己的 PowerShell 模块的建议做法，以确保它们在 Azure 自动化中用作集成模块。 

## <a name="what-is-a-powershell-module"></a>什么是 PowerShell 模块？

PowerShell 模块是一组 PowerShell cmdlet（例如 **Get-Date** 或 **Copy-Item**），可以通过以下方法进行使用：

* PowerShell 控制台
* 脚本
* workflows
* runbook
* DSC 资源

PowerShell 的所有功能都通过 cmdlet 和 DSC 资源公开。 每个 cmdlet 和 DSC 资源都由一个 PowerShell 模块支持，其中许多模块都随 PowerShell 本身提供。 例如，**Get-Date** cmdlet 属于 `Microsoft.PowerShell.Utility` PowerShell 模块，**Copy-Item** cmdlet 属于 `Microsoft.PowerShell.Management` PowerShell 模块，Package DSC 资源属于 PSDesiredStateConfiguration PowerShell 模块。 这些模块都是 PowerShell 附带的。 许多 PowerShell 模块不作为 PowerShell 的一部分提供。 这些模块随第一方或第三方产品分发，或者在 PowerShell 库等位置分发。 这些模块很有用，因为模块可以通过封装的功能简化复杂的任务。  可以详细了解 [MSDN 上的 PowerShell 模块](https://msdn.microsoft.com/library/dd878324%28v=vs.85%29.aspx)。 

## <a name="what-is-an-azure-automation-integration-module"></a>什么是 Azure 自动化集成模块？

集成模块与 PowerShell 模块并没有很大不同。 集成模块就是 PowerShell 模块，只是选择性地包含了另一个文件 - 元数据文件，该文件指定的 Azure 自动化连接类型需要在 Runbook 中用于模块的 cmdlet。 PowerShell 模块可导入到 Azure 自动化中，这样其 cmdlet 就可以在 runbook 中使用，其 DSC 资源就可以在 DSC 配置中使用。 Azure 自动化在后台存储这些模块，在执行 runbook 作业和 DSC 编译作业时将其载入 Azure 自动化沙盒中，并在其中执行 Runbook 以及编译 DSC 配置。 模块中的所有 DSC 资源还会自动放置在 Automation DSC 拉取服务器上。 它们可以在应用 DSC 配置时被计算机拉取。  

我们在 Azure 自动化中提供多个开箱即用的 Azure PowerShell 模块。 但你可以为要集成的任何系统、服务或工具导入 PowerShell 模块。

> [!NOTE]
> 某些模块作为自动化服务中的**全局模块**随附。 在创建自动化帐户时，可以使用这些全局模块。我们有时会更新这些模块，此时会自动将这些模块推送到用户的自动化帐户。 如果不想自动更新这些模块，可以始终自行导入相同的模块，该模块将优先于我们在服务中提供的该模块的全局模块版本。

导入集成模块包时，采用压缩文件格式，文件名与模块名相同，使用 .zip 扩展名。 该包包含 Windows PowerShell 模块以及任何支持文件，包括一个清单文件 (.psd1)，如果模块有的话。

如果模块应包含 Azure 自动化连接类型，则还必须包含名为 `<ModuleName>-Automation.json` 的文件，以便指定连接类型属性。 这是一个 json 文件，它位于 .zip 压缩文件的 module 文件夹中。 此文件包含连接到模块所代表的系统或服务所需的**连接**的字段。 此配置最终会在 Azure 自动化中创建一个连接类型。 可以使用此文件为模块的连接类型设置字段名称、类型，以及这些字段是否应加密和/或可选。 以下示例是采用 json 文件格式的模板：

```json
{ 
   "ConnectionFields": [
   {
      "IsEncrypted":  false,
      "IsOptional":  false,
      "Name":  "ComputerName",
      "TypeName":  "System.String"
   },
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
   }],
   "ConnectionTypeName":  "DataProtectionManager",
   "IntegrationModuleName":  "DataProtectionManager"
}
```

如果已经部署过 Service Management Automation 并为自动化 runbook 创建过集成模块包，则此模板对你来说应该很熟悉。

## <a name="authoring-best-practices"></a>创作最佳实践

虽然集成模块是 PowerShell 模块，但我们仍建议在创作 PowerShell 模块以便用于 Azure 自动化时注意某些事项。 某些建议对于让模块在 PowerShell 工作流中良好运行非常有用。

1. 在模块中为每个 cmdlet 提供摘要、说明和帮助 URI。 可以在 PowerShell 中为 cmdlet 定义特定的帮助信息，使用户可以通过 **Get-Help** cmdlet 获取这些 cmdlet 的使用帮助。 例如，下面说明了如何为 .psm1 文件中编写的 PowerShell 模块定义摘要和帮助 URI。 
   
    ```powershell
    <#
        .SYNOPSIS
         Gets all outgoing phone numbers for this Twilio account 
    #>
    function Get-TwilioPhoneNumbers {
    [CmdletBinding(DefaultParameterSetName='SpecifyConnectionFields', `
    HelpUri='https://www.twilio.com/docs/api/rest/outgoing-caller-ids')]
    param(
       [Parameter(ParameterSetName='SpecifyConnectionFields', Mandatory=$true)]
       [ValidateNotNullOrEmpty()]
       [string]
       $AccountSid,
   
       [Parameter(ParameterSetName='SpecifyConnectionFields', Mandatory=$true)]
       [ValidateNotNullOrEmpty()]
       [string]
       $AuthToken,
   
       [Parameter(ParameterSetName='UseConnectionObject', Mandatory=$true)]
       [ValidateNotNullOrEmpty()]
       [Hashtable]
       $Connection
    )
   
    $cred = CreateTwilioCredential -Connection $Connection -AccountSid $AccountSid -AuthToken $AuthToken
   
    $uri = "$TWILIO_BASE_URL/Accounts/" + $cred.UserName + "/IncomingPhoneNumbers"
   
    $response = Invoke-RestMethod -Method Get -Uri $uri -Credential $cred
   
    $response.TwilioResponse.IncomingPhoneNumbers.IncomingPhoneNumber
    }
    ```

   提供此信息可在 PowerShell 控制台中使用 **Get-Help** cmdlet 显示此帮助。 此信息还会在 Azure 自动化中公开。  例如，在进行 Runbook 创作过程中插入活动。 在使用 Web 浏览器访问 Azure 自动化时，单击“查看详细帮助”即可在该浏览器的另一选项卡中打开帮助 URI。

   ![集成模块帮助](media/automation-integration-modules/automation-integration-module-activitydesc.png)

2. 如果模块是针对远程系统运行的：

   1. 该模块应包含一个集成模块元数据文件，用于定义连接到该远程系统所需的信息，即定义连接类型。
   2. 模块中的每个 cmdlet 都应能够使用连接对象（该连接类型的实例）作为参数。  

    如果允许将对象传递给模块中的 cmdlet 并使用连接类型的字段作为参数，则这些 cmdlet 在 Azure 自动化中将更易于使用。 这让用户能够在每次调用 cmdlet 时将连接资产的参数映射到 cmdlet 的相应参数。

    从上面的 Runbook 示例来看，可以使用名为 CorpTwilio 的 Twilio 连接资产来访问 Twilio，并在帐户中返回所有电话号码。  注意到它是如何将连接的字段映射到 cmdlet 的参数的吗？

    ```powershell
    workflow Get-CorpTwilioPhones
    {
      $CorpTwilio = Get-AutomationConnection -Name 'CorpTwilio'

      Get-TwilioPhoneNumbers 
        -AccountSid $CorpTwilio.AccountSid  
        -AuthToken $CorptTwilio.AuthToken
    }
    ```
  
    实现此行为的更轻松且更好的方法是直接将连接对象传递给 cmdlet -

    ```powershell
    workflow Get-CorpTwilioPhones
    {
      $CorpTwilio = Get-AutomationConnection -Name 'CorpTwilio'

      Get-TwilioPhoneNumbers -Connection $CorpTwilio
    }
    ```

    若要启用 cmdlet 的类似上述示例的行为，可以允许 cmdlet 以参数的形式直接接受连接对象，而不是只接受连接字段作为参数。 通常情况下，需要为每个 cmdlet 提供一个参数集，这样不使用 Azure 自动化的用户在调用 cmdlet 时就不需要构造一个哈希表来充当连接对象。 参数集 **SpecifyConnectionFields** 可用于逐个传递连接字段属性。 **UseConnectionObject** 允许将连接一直传下去。 可以看到，[Twilio PowerShell 模块](https://gallery.technet.microsoft.com/scriptcenter/Twilio-PowerShell-Module-8a8bfef8) 中的 Send-TwilioSMS cmdlet 允许通过任一种方式进行传递：

    ```powershell
    function Send-TwilioSMS {
      [CmdletBinding(DefaultParameterSetName='SpecifyConnectionFields', `
      HelpUri='https://www.twilio.com/docs/api/rest/sending-sms')]
      param(
         [Parameter(ParameterSetName='SpecifyConnectionFields', Mandatory=$true)]
         [ValidateNotNullOrEmpty()]
         [string]
         $AccountSid,
   
         [Parameter(ParameterSetName='SpecifyConnectionFields', Mandatory=$true)]
         [ValidateNotNullOrEmpty()]
         [string]
         $AuthToken,
   
         [Parameter(ParameterSetName='UseConnectionObject', Mandatory=$true)]
         [ValidateNotNullOrEmpty()]
         [Hashtable]
         $Connection
   
       )
    }
    ```

3. 在模块中定义所有 cmdlet 的输出类型。 定义 cmdlet 的输出类型以后，就可以利用设计时 IntelliSense 来确定 cmdlet 的输出属性，供创作时使用。 这在自动化 runbook 的图形创作过程中特别有用，这种创作过程要求掌握一定程度的设计时知识，以改进用户对模块的使用体验。

   ![图形 Runbook 输出类型](media/automation-integration-modules/runbook-graphical-module-output-type.png)

   此行为类似于 PowerShell ISE 中 cmdlet 输出的“预输入”功能，但不需要运行。

   ![POSH IntelliSense](media/automation-integration-modules/automation-posh-ise-intellisense.png)

4. 模块中的 cmdlet 不应使用复杂对象类型作为参数。 PowerShell 工作流不同于 PowerShell，因为前者采用反序列化格式存储复杂类型。 基元类型仍为基元类型，而复杂类型则转换为反序列化版本，后者实质上是属性包。 例如，如果在 Runbook（或 PowerShell 工作流）中使用了 **Get-Process** cmdlet，则会返回 [Deserialized.System.Diagnostic.Process] 类型的对象，而不是所需的 [System.Diagnostic.Process] 类型。 此类型包含所有与非反序列化类型相同的属性，但不包含相关方法。 如果尝试将该值作为参数传递给某个 cmdlet，而此 cmdlet 需要该参数的值为 [System.Diagnostic.Process]，则会收到以下错误：*无法处理参数 'process' 的参数转换。错误：无法将 "System.Diagnostics.Process (CcmExec)" 值从类型 "Deserialized.System.Diagnostics.Process" 转换为类型 "System.Diagnostics.Process"。*   这是因为，预期的 [System.Diagnostic.Process] 类型与给定的 [Deserialized.System.Diagnostic.Process] 类型之间存在类型不匹配的情况。 若要解决此问题，必须确保模块的 cmdlet 不会使用复杂类型作为参数。 下面是错误的处理方式。

    ```powershell
    function Get-ProcessDescription {
      param (
            [System.Diagnostic.Process] $process
      )
      $process.Description
    }
    ```

    正确方法是采用由 cmdlet 在内部使用的基元类型来获取复杂对象并使用。 由于 cmdlet 是在 PowerShell 环境而非 PowerShell 工作流环境中执行的，因此在 cmdlet 中，$process 会变为正确的 [System.Diagnostic.Process] 类型。  

    ```powershell
    function Get-ProcessDescription {
      param (
            [String] $processName
      )
      $process = Get-Process -Name $processName
   
      $process.Description
    }
    ```

   Runbook 中的连接资产是哈希表，这些表属于复杂类型。不过，这些哈希表似乎可以传递到 cmdlet 中用作连接参数而不出任何问题，不会有任何强制转换异常。 从技术上讲，某些 PowerShell 类型是能够正常地从序列化形式强制转换为反序列化形式的，因此可以传递到 cmdlet 中作为参数来接受非反序列化类型。 哈希表就是其中之一。 可以对模块作者所定义的类型进行某种方式的实施，使之也能正确地反序列化，但这种方式需要克服某些弊端。 该类型需要有一个默认的构造函数，需要公开其所有属性，且需要有一个 PSTypeConverter。 但是，对于模块作者没有所有权的已定义类型，则无法对其进行“修复”，因此还是建议用户避免使用复杂类型作为参数。 Runbook 创作提示：如果 cmdlet 需要采用复杂类型参数，那么 PowerShell 工作流中的解决方法是包装生成复杂类型的 cmdlet 和在同一 InlineScript 活动中使用复杂类型的 cmdlet。 由于 InlineScript 以 PowerShell 方式而非 PowerShell 工作流方式执行其内容，生成复杂类型的 cmdlet 会生成相应的正确类型，而不是反序列化的复杂类型。

5. 使模块中的所有 cmdlet 以无状态方式呈现。 PowerShell 工作流在不同的会话中运行在工作流中调用的每个 cmdlet。 这意味着，任何依赖于同一模块中其他 cmdlet 创建/修改的会话状态的 cmdlet 都无法在 PowerShell 工作流 runbook 中使用。  下面是应避免的处理方式的示例。
   
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

6. 该模块应完全包含在能够进行 Xcopy 操作的包中。 需要执行 runbook 时，Azure 自动化模块将分发到自动化沙盒中。 这些模块需要独立于它们在其上运行的​​主机工作。 你应能够压缩并移动模块包，并在导入到其他主机的 PowerShell 环境时使其正常运行。 为了实现这一点，模块不应该依赖于模块文件夹以外的任何文件。 此文件夹是将模块导入 Azure 自动化时压缩的文件夹。 该模块还不应依赖于主机上的任何唯一注册表设置，例如安装产品时设置的那些设置。 如果不遵循此最佳做法，则无法在 Azure 自动化中使用该模块。  

7. 如果在模块中引用 [Azure Powershell Az 模块](/powershell/azure/new-azureps-module-az?view=azps-1.1.0)，请确保没有同时引用 `AzureRM`。 `Az` 模块不能与 `AzureRM` 模块一起使用。 Runbook 支持 `Az`，但默认情况下不会导入。 若要了解 `Az` 模块和需要考虑的注意事项，请参阅 [Azure 自动化中的 Az 模块支持](az-modules.md)。

## <a name="next-steps"></a>后续步骤

* 若要开始使用 PowerShell 工作流 Runbook，请参阅 [我的第一个 PowerShell 工作流 Runbook](automation-first-runbook-textual.md)
* 若要详细了解如何创建 PowerShell 模块，请参阅 [编写 Windows PowerShell 模块](https://msdn.microsoft.com/library/dd878310%28v=vs.85%29.aspx)
