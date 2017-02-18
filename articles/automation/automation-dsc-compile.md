---
title: "在 Azure 自动化 DSC 中编译配置 | Microsoft Docs"
description: "本文介绍如何编译 Azure 自动化的 Desired State Configuration (DSC) 配置。"
services: automation
documentationcenter: na
author: eslesar
manager: carmonm
ms.assetid: 49f20b31-4fa5-4712-b1c7-8f4409f1aecc
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: na
ms.date: 02/07/2017
ms.author: magoedte; eslesar
translationtype: Human Translation
ms.sourcegitcommit: 032747ffb7a603c54e8913c0d82edbc8e11b73c3
ms.openlocfilehash: 0b808dd6bcf0a0d1f8e459927a4010dc1887ca60

---

# <a name="compiling-configurations-in-azure-automation-dsc"></a>在 Azure 自动化 DSC 中编译配置

使用 Azure 自动化时可通过两种方法编译 Desired State Configuration (DSC) ：使用 Azure 门户，或者使用 Windows PowerShell。 下表将帮助你根据每种方法的特征确定何时应使用哪种方法：

### <a name="azure-portal"></a>Azure 门户

* 使用交互式用户界面的最简单方法
* 用于提供简单参数值的窗体
* 轻松跟踪作业状态
* 使用 Azure 登录对访问进行身份验证

### <a name="windows-powershell"></a>Windows PowerShell

* 使用 Windows PowerShell cmdlet 从命令行调用
* 可以使用多个步骤包含在自动化解决方案中
* 提供简单和复杂的参数值
* 跟踪作业状态
* 支持 PowerShell cmdlet 所需的客户端
* 传递 ConfigurationData
* 编译使用凭据的配置

在确定编译方法后，可以遵循以下相应过程开始编译。

## <a name="compiling-a-dsc-configuration-with-the-azure-portal"></a>使用 Azure 门户编译 DSC 配置

1. 从自动化帐户中，单击“配置”。
2. 单击某个配置以打开其边栏选项卡。
3. 单击“编译”。
4. 如果该配置没有参数，系统将提示你确认是否要进行编译。 如果该配置有参数，则会打开“编译配置”边栏选项卡让用户提供参数值。 有关参数的详细信息，请参阅下面的[**基本参数**](#basic-parameters)部分。
5. “编译作业”边栏选项卡随即打开，用户可跟踪编译作业的状态，且会将由于此作业引起的节点配置（MOF 配置文档）放在 Azure 自动化 DSC“拉”服务器上。

## <a name="compiling-a-dsc-configuration-with-windows-powershell"></a>使用 Windows PowerShell 编译 DSC 配置

可以在 Windows PowerShell 中使用 [`Start-AzureRmAutomationDscCompilationJob`](https://msdn.microsoft.com/library/mt244118.aspx) 开始编译。 以下示例代码启动名为 **SampleConfig** 的 DSC 配置编译。

```powershell
Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "SampleConfig"
```

`Start-AzureRmAutomationDscCompilationJob` 返回可用于跟踪作业状态的编译作业对象。 接下来，可使用此编译作业对象与 [`Get-AzureRmAutomationDscCompilationJob`](https://msdn.microsoft.com/library/mt244120.aspx) 来确定编译作业的状态，并使用 [`Get-AzureRmAutomationDscCompilationJobOutput`](https://msdn.microsoft.com/library/mt244103.aspx) 查看其流（输出）。 以下示例代码启动 **SampleConfig** 配置的编译，并在编译完成后显示其流。

```powershell
$CompilationJob = Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "SampleConfig"

while($CompilationJob.EndTime –eq $null -and $CompilationJob.Exception –eq $null)
{
    $CompilationJob = $CompilationJob | Get-AzureRmAutomationDscCompilationJob
    Start-Sleep -Seconds 3
}

$CompilationJob | Get-AzureRmAutomationDscCompilationJobOutput –Stream Any
```

## <a name="basic-parameters"></a>基本参数
DSC 配置中的参数声明（包括参数类型和属性）的工作方式与 Azure 自动化 Runbook 中相同。 若要了解有关 Runbook 参数的详细信息，请参阅 [Starting a runbook in Azure Automation](automation-starting-a-runbook.md)（在 Azure 自动化中启动 Runbook）。

以下示例使用名为 **FeatureName** 和 **IsPresent** 的两个参数来确定在编译期间生成的 **ParametersExample.sample** 节点配置中的属性值。

```powershell
Configuration ParametersExample
{
    param(
        [Parameter(Mandatory=$true)]

        [string] $FeatureName,

        [Parameter(Mandatory=$true)]
        [boolean] $IsPresent
    )

    $EnsureString = "Present"
    if($IsPresent -eq $false)
    {
        $EnsureString = "Absent"
    }

    Node "sample"
    {
        WindowsFeature ($FeatureName + "Feature")
        {
            Ensure = $EnsureString
            Name = $FeatureName
        }
    }
}
```

你可以在 Azure 自动化 DSC 门户或 Azure PowerShell 中编译使用基本参数的 DSC 配置：

### <a name="portal"></a>门户

在门户中，可在单击“编译”后输入参数值。

![替换文字](./media/automation-dsc-compile/DSC_compiling_1.png)

### <a name="powershell"></a>PowerShell

PowerShell 需要[哈希表](http://technet.microsoft.com/library/hh847780.aspx)中的参数，其中的键必须与参数名称匹配，值等于参数值。

```powershell
$Parameters = @{
    "FeatureName" = "Web-Server"
    "IsPresent" = $False
}

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "ParametersExample" -Parameters $Parameters
```

详细了解如何将 PSCredentials 作为参数传入，请参阅下面的<a href="#credential-assets">**凭据资产**</a>。

## <a name="configurationdata"></a>ConfigurationData
通过 **ConfigurationData** 可在使用 PowerShell DSC 时区分结构化配置与任何环境特定配置。 有关 **ConfigurationData** 的详细信息，请参阅[区分 PowerShell DSC 中的“What”与“Where”](http://blogs.msdn.com/b/powershell/archive/2014/01/09/continuous-deployment-using-dsc-with-minimal-change.aspx)。

> [!NOTE]
> 使用 Azure PowerShell 在 Azure 自动化 DSC 中进行编译时，可使用 **ConfigurationData**，但在 Azure 门中编译时不可使用。

以下示例 DSC 配置通过 **$ConfigurationData** 和 **$AllNodes** 关键字来使用 **ConfigurationData**。 在本示例中还需要 [**xWebAdministration**](https://www.powershellgallery.com/packages/xWebAdministration/) 模块：

```powershell
Configuration ConfigurationDataSample
{
    Import-DscResource -ModuleName xWebAdministration -Name MSFT_xWebsite

    Write-Verbose $ConfigurationData.NonNodeData.SomeMessage

    Node $AllNodes.Where{$_.Role -eq "WebServer"}.NodeName
    {
        xWebsite Site
        {
            Name = $Node.SiteName
            PhysicalPath = $Node.SiteContents
            Ensure   = "Present"
        }
    }
}
```

可以使用 PowerShell 编译上述 DSC 配置。 以下 PowerShell 将两个节点配置添加到 Azure 自动化 DSC“拉”服务器：**ConfigurationDataSample.MyVM1** 和 **ConfigurationDataSample.MyVM3**：

```powershell
$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = "MyVM1"
            Role = "WebServer"
        },
        @{
            NodeName = "MyVM2"
            Role = "SQLServer"
        },
        @{
            NodeName = "MyVM3"
            Role = "WebServer"
        }
    )

    NonNodeData = @{
        SomeMessage = "I love Azure Automation DSC!"
    }
}

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "ConfigurationDataSample" -ConfigurationData $ConfigData
```

## <a name="assets"></a>资产

Azure 自动化 DSC 配置和 Runbook 中的资产引用是相同的。 有关详细信息，请参阅以下主题：

* [证书](automation-certificates.md)
* [连接](automation-connections.md)
* [凭据](automation-credentials.md)
* [变量](automation-variables.md)

### <a name="credential-assets"></a>凭据资产

尽管 Azure 自动化中的 DSC 配置可以使用 **Get-AzureRmAutomationCredential** 引用凭据资产，但如果需要，也可以通过参数传入凭据资产。 如果配置采用属于 **PSCredential** 类型的参数，则需要将 Azure 自动化凭据资产的字符串名称传递为该参数的值，而不是 PSCredential 对象。 具有该名称的 Azure 自动化凭据资产在后台检索并传递到配置。

要在节点配置（MOF 配置文档）中保持凭据的安全，需要在节点配置 MOF 文件中为凭据加密。 Azure 自动化进一步执行此步骤并加密整个 MOF 文件。 不过，目前必须告知 PowerShell DSC 在节点配置 MOF 生成期间以纯文本形式输出凭据是可行的，因为 PowerShell DSC 并不知道在通过编译作业生成 MOF 文件之后 Azure 自动化将加密整个文件。

可告知 PowerShell DSC，使用 [**ConfigurationData**](#configurationdata) 在生成的节点配置 MOF 中以纯文本形式输出凭据是可行的。 应针对每个出现在 DSC 配置中且使用凭据的节点块名称，通过 **ConfigurationData** 传递 `PSDscAllowPlainTextPassword = $true`。

以下示例演示使用自动化凭据资产的 DSC 配置。

```powershell
Configuration CredentialSample
{
    $Cred = Get-AzureRmAutomationCredential -ResourceGroupName "ResourceGroup01" -AutomationAccountName "AutomationAcct" -Name "SomeCredentialAsset"

    Node $AllNodes.NodeName
    {
        File ExampleFile
        {
            SourcePath = "\\Server\share\path\file.ext"
            DestinationPath = "C:\destinationPath"
            Credential = $Cred
        }
    }
}
```

可以使用 PowerShell 编译上述 DSC 配置。 以下 PowerShell 将两个节点配置添加到 Azure 自动化 DSC“拉”服务器：**CredentialSample.MyVM1** 和 **CredentialSample.MyVM2**。

```powershell
$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = "*"
            PSDscAllowPlainTextPassword = $True
        },
        @{
            NodeName = "MyVM1"
        },
        @{
            NodeName = "MyVM2"
        }
    )
}

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "CredentialSample" -ConfigurationData $ConfigData
```



<!--HONumber=Feb17_HO2-->


