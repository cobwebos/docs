---
title: 创建 Azure 自动化集成模块
description: 本教程指导用户在 Azure 自动化中创建、测试以及通过示例方式使用集成模块。
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 7b7aa09776045705ce98a472adbbe2f36d827ccc
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="azure-automation-integration-modules"></a>Azure 自动化集成模块
PowerShell 是 Azure 自动化背后的基本技术。 由于 Azure 自动化是基于 PowerShell 构建的，因此 PowerShell 模块对于 Azure 自动化的可扩展性很重要。 在本文中，我们将向你详细介绍 Azure 自动化如何使用 PowerShell 模块（也称“集成模块”），以及如何根据最佳做法创建自己的 PowerShell 模块，确保这些模块在 Azure 自动化中作为集成模块来运行。 

## <a name="what-is-a-powershell-module"></a>什么是 PowerShell 模块？
PowerShell 模块是指一组可以通过 PowerShell 控制台、脚本、工作流、Runbook 使用的 PowerShell cmdlet（例如 **Get-Date** 或 **Copy-Item**），以及可以通过 PowerShell DSC 配置使用的 PowerShell DSC 资源（例如 WindowsFeature 或文件）。 PowerShell 的所有功能都是通过 cmdlet 和 DSC 资源公开的，所有 cmdlet/DSC 资源都受 PowerShell 模块支持，许多模块是 PowerShell 自带的。 例如，**Get-Date** cmdlet 属于 Microsoft.PowerShell.Utility PowerShell 模块，**Copy-Item** cmdlet 属于 Microsoft.PowerShell.Management PowerShell 模块，Package DSC 资源属于 PSDesiredStateConfiguration PowerShell 模块。 这些模块都是 PowerShell 附带的。 但是，许多 PowerShell 模块不是 PowerShell 附带的，而是通过第一方或第三方产品（例如 System Center 2012 Configuration Manager）分发的，或者由广大的 PowerShell 社区在 PowerShell 库这样的地方分发的。 这些模块很有用，因为模块可以通过封装的功能简化复杂的任务。  可以详细了解 [MSDN 上的 PowerShell 模块](https://msdn.microsoft.com/library/dd878324%28v=vs.85%29.aspx)。 

## <a name="what-is-an-azure-automation-integration-module"></a>什么是 Azure 自动化集成模块？
集成模块与 PowerShell 模块并没有很大不同。 集成模块就是 PowerShell 模块，只是选择性地包含了另一个文件 - 元数据文件，该文件指定的 Azure 自动化连接类型需要在 Runbook 中用于模块的 cmdlet。 不管是否为选择性文件，这些 PowerShell 模块均可导入到 Azure 自动化中，这样其 cmdlet 就可以在 Runbook 中使用，其 DSC 资源就可以在 DSC 配置中使用。 Azure 自动化在后台存储这些模块，在执行 Runbook 作业和 DSC 编译作业时将其载入 Azure 自动化沙盒中，并在其中执行 Runbook 以及编译 DSC 配置。 此外还会自动将模块中的任何 DSC 资源放置在自动化 DSC 拉取服务器上，供那些尝试应用 DSC 配置的计算机拉取。  

我们在 Azure 自动化中为用户附送许多现成的 Azure PowerShell 模块，以便用户能够立刻使用这些模块进行 Azure 自动化管理，但用户也可以地导入 PowerShell 模块，不管要集成的系统、服务或工具是什么。 

> [!NOTE]
> 某些模块作为自动化服务中的“全局模块”随附。 在创建自动化帐户时，可以使用这些全局模块。我们有时会更新这些模块，此时会自动将这些模块推送到用户的自动化帐户。 如果不想自动更新这些模块，可以始终自行导入相同的模块，该模块将优先于我们在服务中提供的该模块的全局模块版本。 

导入集成模块包时，采用压缩文件格式，文件名与模块名相同，使用 .zip 扩展名。 该包包含 Windows PowerShell 模块以及任何支持文件，包括一个清单文件 (.psd1)，如果模块有的话。

如果模块应包含 Azure 自动化连接类型，则还必须包含名为 `<ModuleName>-Automation.json` 的文件，以便指定连接类型属性。 该文件为 json 文件，放置在 .zip 压缩文件的模块文件夹中，其中包含的“connection”字段是连接到模块所代表的系统或服务所必需的。 该文件最终会在 Azure 自动化中创建一个连接类型。 可以使用此文件为模块的连接类型设置字段名称、类型，以及这些字段是否应加密和/或可选。 下面是一个采用 json 文件格式的模板：

```
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

如果已经部署过 Service Management Automation 并为自动化 Runbook 创建过集成模块包，则该模板对你来说应该很熟悉。 

## <a name="authoring-best-practices"></a>创作最佳实践
虽然集成模块本质上是 PowerShell 模块，但建议在进行 PowerShell 模块的创作时注意某些事项，尽量提高其在 Azure 自动化中的可用性。 其中一些事项是特定于 Azure 自动化的，而另一些事项在得到妥当处理后可以提高模块在 PowerShell 工作流中的运行效率，与自动化的使用无关。 

1. 在模块中为每个 cmdlet 提供摘要、说明和帮助 URI。 可以在 PowerShell 中为 cmdlet 定义特定的帮助信息，使用户可以通过 **Get-Help** cmdlet 获取这些 cmdlet 的使用帮助。 例如，下面说明了如何为 .psm1 文件中编写的 PowerShell 模块定义摘要和帮助 URI。<br>  
   
    ```
    <#
        .SYNOPSIS
         Gets all outgoing phone numbers for this Twilio account 
    #>
    function Get-TwilioPhoneNumbers {
    [CmdletBinding(DefaultParameterSetName='SpecifyConnectionFields', `
    HelpUri='http://www.twilio.com/docs/api/rest/outgoing-caller-ids')]
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
   <br> 提供此信息以后，用户不仅可以使用 **Get-Help** cmdlet 在 PowerShell 控制台中显示此帮助，还可以在特定情况下在 Azure 自动化中公开此帮助功能。  例如，在进行 Runbook 创作过程中插入活动。 在使用 Web 浏览器访问 Azure 自动化时，单击“查看详细帮助”即可在该浏览器的另一标签页中打开帮助 URI。<br>![集成模块帮助](media/automation-integration-modules/automation-integration-module-activitydesc.png)
2. 如果该模块是针对远程系统运行的：

    a. 该模块应包含一个集成模块元数据文件，用于定义连接到该远程系统所需的信息，即定义连接类型。  
    b. 模块中的每个 cmdlet 都应能够使用连接对象（该连接类型的实例）作为参数。  

    如果允许将对象传递给模块中的 cmdlet 并使用连接类型的字段作为参数，则这些 cmdlet 在 Azure 自动化中将更易于使用。 这样一来，用户就不必在每次调用 cmdlet 时会连接资产的参数映射到 cmdlet 的相应参数。 从上面的 Runbook 示例来看，可以使用名为 CorpTwilio 的 Twilio 连接资产来访问 Twilio，并在帐户中返回所有电话号码。  注意到它是如何将连接的字段映射到 cmdlet 的参数的吗？<br>
   
    ```
    workflow Get-CorpTwilioPhones
    {
      $CorpTwilio = Get-AutomationConnection -Name 'CorpTwilio'
   
      Get-TwilioPhoneNumbers 
        -AccountSid $CorpTwilio.AccountSid  
        -AuthToken $CorptTwilio.AuthToken
    }
    ```
  
    更轻松且更佳的访问方式是直接将连接对象传递给 cmdlet -
   
    ```
    workflow Get-CorpTwilioPhones
    {
      $CorpTwilio = Get-AutomationConnection -Name 'CorpTwilio'
   
      Get-TwilioPhoneNumbers -Connection $CorpTwilio
    }
    ```
   
    要启用 cmdlet 的此类行为，可以允许 cmdlet 以参数的形式直接接受连接对象，而不是只接受连接字段作为参数。 通常情况下，需要为每个 cmdlet 提供一个参数集，这样不使用 Azure 自动化的用户在调用 cmdlet 时就不需要构造一个哈希表来充当连接对象。 下面的参数集 **SpecifyConnectionFields** 可用于逐个传递连接字段属性。 **UseConnectionObject** 允许将连接一直传下去。 可以看到，[Twilio PowerShell 模块](https://gallery.technet.microsoft.com/scriptcenter/Twilio-PowerShell-Module-8a8bfef8) 中的 Send-TwilioSMS cmdlet 允许通过任一种方式进行传递： 
   
    ```
    function Send-TwilioSMS {
      [CmdletBinding(DefaultParameterSetName='SpecifyConnectionFields', `
      HelpUri='http://www.twilio.com/docs/api/rest/sending-sms')]
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
   <br>
3. 在模块中定义所有 cmdlet 的输出类型。 定义 cmdlet 的输出类型以后，就可以利用设计时 IntelliSense 来确定 cmdlet 的输出属性，供创作时使用。 这在自动化 Runbook 的图形创作过程中特别有用，这种创作过程要求掌握一定程度的设计时知识，以改进用户对模块的使用体验。<br><br> ![图形 Runbook 输出类型](media/automation-integration-modules/runbook-graphical-module-output-type.png)<br> 这类似于 PowerShell ISE 中 cmdlet 输出的“预输入”功能，不需要运行。<br><br> ![POSH IntelliSense](media/automation-integration-modules/automation-posh-ise-intellisense.png)<br>
4. 模块中的 cmdlet 不应使用复杂对象类型作为参数。 PowerShell 工作流不同于 PowerShell，因为前者采用反序列化格式存储复杂类型。 基元类型仍为基元类型，而复杂类型则转换为反序列化版本，后者实质上是属性包。 例如，如果在 Runbook（或 PowerShell 工作流）中使用了 **Get-Process** cmdlet，则会返回 [Deserialized.System.Diagnostic.Process] 类型的对象，而不是所需的 [System.Diagnostic.Process] 类型。 此类型包含所有与非反序列化类型相同的属性，但不包含相关方法。 如果尝试将该值作为参数传递给某个 cmdlet，而此 cmdlet 需要该参数的值为 [System.Diagnostic.Process]，则会收到以下错误：*无法处理参数 'process' 的参数转换。错误：无法将 "System.Diagnostics.Process (CcmExec)" 值从类型 "Deserialized.System.Diagnostics.Process" 转换为类型 "System.Diagnostics.Process"。*   这是因为，预期的 [System.Diagnostic.Process] 类型与给定的 [Deserialized.System.Diagnostic.Process] 类型存在类型不匹配的情况。 若要解决此问题，必须确保模块的 cmdlet 不使用复杂类型作为参数。 下面是错误的处理方式。
   
    ```
    function Get-ProcessDescription {
      param (
            [System.Diagnostic.Process] $process
      )
      $process.Description
    }
    ``` 
    <br>
    下面是正确的处理方式，即采用可以由 cmdlet 在内部使用的基元类型来获取复杂对象并使用。 由于 cmdlet 是在 PowerShell 环境而非 PowerShell 工作流环境中执行的，因此在 cmdlet 中，$process 会变为正确的 [System.Diagnostic.Process] 类型。  
   
    ```
    function Get-ProcessDescription {
      param (
            [String] $processName
      )
      $process = Get-Process -Name $processName
   
      $process.Description
    }
    ```
   <br>
   Runbook 中的连接资产是哈希表，这些表属于复杂类型。不过，这些哈希表似乎可以传递到 cmdlet 中用作 -Connection 参数而不出任何问题，不会有任何强制转换异常。 从技术上讲，某些 PowerShell 类型是能够正常地从序列化形式强制转换为反序列化形式的，因此可以传递到 cmdlet 中作为参数来接受非反序列化类型。 哈希表就是其中之一。 可以对模块作者所定义的类型进行某种方式的实施，使之也能正确地反序列化，但这种方式需要克服某些弊端。 该类型需要有一个默认的构造函数，需要公开其所有属性，且需要有一个 PSTypeConverter。 不过，对于模块作者没有所有权的已定义类型，则无法对其进行“修复”，因此建议用户还是避免使用复杂类型作为参数。 Runbook 创作提示：如果 cmdlet 因故而需要使用复杂类型参数，或者使用的是其他人的需要复杂类型参数的模块，则要想在本地 PowerShell 的 PowerShell 工作流 Runbook 和 PowerShell 工作流中解决这个问题，需要将生成复杂类型的 cmdlet 和使用该复杂类型的 cmdlet 打包到同一个 InlineScript 活动中。 由于 InlineScript 以 PowerShell 方式而非 PowerShell 工作流方式执行其内容，生成复杂类型的 cmdlet 会生成相应的正确类型，而不是反序列化的复杂类型。
5. 使模块中的所有 cmdlet 以无状态方式呈现。 PowerShell 工作流在不同的会话中运行在工作流中调用的每个 cmdlet。 这意味着，任何依赖于同一模块中其他 cmdlet 创建/修改的会话状态的 cmdlet 都无法在 PowerShell 工作流 Runbook 中使用。  下面是应避免的处理方式的示例。
   
    ```
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
   <br>
6. 该模块应完全包含在能够进行 Xcopy 操作的包中。 Azure 自动化模块是在需要执行 Runbook 时分发到自动化沙盒中的，因此在使用时需独立于运行时所在的主机。 这意味着，应该可以在压缩模块包后将其移到任何其他使用相同 PowerShell 版本或更新版本的主机上，并将其导入该主机的 PowerShell 环境中，就能正常地运行该模块包了。 因此，该模块不应依赖于模块文件夹（即导入到 Azure 自动化中时需要进行压缩的文件夹）外的任何文件，也不应依赖于主机上任何特殊的注册表设置，例如在安装产品时进行的设置。 如果不遵循此最佳实践，将无法在 Azure 自动化中使用该模块。  

## <a name="next-steps"></a>后续步骤

* 若要开始使用 PowerShell 工作流 Runbook，请参阅 [我的第一个 PowerShell 工作流 Runbook](automation-first-runbook-textual.md)
* 若要详细了解如何创建 PowerShell 模块，请参阅 [编写 Windows PowerShell 模块](https://msdn.microsoft.com/library/dd878310%28v=vs.85%29.aspx)

