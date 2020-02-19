---
title: 在 Azure Automation State Configuration 中编译配置
description: 本文介绍如何编译 Azure 自动化的 Desired State Configuration (DSC) 配置。
services: automation
ms.subservice: dsc
ms.date: 09/10/2018
ms.topic: conceptual
ms.openlocfilehash: 5462d0fae44217f6217d5be1b321df53c4706aaa
ms.sourcegitcommit: dfa543fad47cb2df5a574931ba57d40d6a47daef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2020
ms.locfileid: "77430561"
---
# <a name="compiling-dsc-configurations-in-azure-automation-state-configuration"></a>在 Automation State Configuration 中编译 DSC 配置

你可以通过两种方式将所需状态配置（DSC）配置编译为 Azure 自动化状态配置：在 Azure 和 Windows PowerShell 中。 下表可帮助你根据每种方法的特征确定何时应使用哪种方法：

- Azure 状态配置编译服务
  - 带有交互式用户界面的初级方法
   - 轻松跟踪作业状态

- Windows PowerShell
  - 从本地工作站上的 Windows PowerShell 或生成服务调用
  - 与开发测试管道集成
  - 提供复杂的参数值
  - 大规模使用节点和非节点数据
  - 显著提高性能

有关编译的详细信息，请参阅[Azure 资源管理器模板所需的状态配置扩展](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template#details)。

## <a name="compiling-a-dsc-configuration-in-azure-state-configuration"></a>在 Azure 状态配置中编译 DSC 配置

### <a name="portal"></a>门户

1. 从自动化帐户中，单击“State Configuration (DSC)”。
1. 单击“配置”选项卡，然后单击要编译的配置名称。
1. 单击“编译”。
1. 如果该配置没有参数，系统会提示确认是否要进行编译。 如果配置有参数，则会打开 "**编译配置**" 边栏选项卡，以便您提供参数值。
1. “编译作业”页面随即打开，用户可跟踪编译作业的状态，并可将由于此作业引起的节点配置（MOF 配置文档）放在 Azure Automation State Configuration“拉”服务器上。

### <a name="azure-powershell"></a>Azure PowerShell

可以在 Windows PowerShell 中使用 [`Start-AzAutomationDscCompilationJob`](/powershell/module/az.automation/start-azautomationdsccompilationjob) 开始编译。 以下示例代码启动名为 **SampleConfig** 的 DSC 配置编译。

```powershell
Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'
```

`Start-AzAutomationDscCompilationJob` 返回可用于跟踪作业状态的编译作业对象。 然后，可以将此编译作业对象与 [`Get-AzAutomationDscCompilationJob`](/powershell/module/az.automation/get-azautomationdsccompilationjob) 一起使用
以确定编译作业的状态，与 [`Get-AzAutomationDscCompilationJobOutput`](/powershell/module/az.automation/get-azautomationdscconfiguration) 一起使用
以查看其流（输出）。 以下示例代码启动 **SampleConfig** 配置的编译，并在编译完成后显示其流。

```powershell
$CompilationJob = Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'

while($null -eq $CompilationJob.EndTime -and $null -eq $CompilationJob.Exception)
{
    $CompilationJob = $CompilationJob | Get-AzAutomationDscCompilationJob
    Start-Sleep -Seconds 3
}

$CompilationJob | Get-AzAutomationDscCompilationJobOutput –Stream Any
```

### <a name="declare-basic-parameters"></a>声明基本参数

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

    $EnsureString = 'Present'
    if($IsPresent -eq $false)
    {
        $EnsureString = 'Absent'
    }

    Node 'sample'
    {
        WindowsFeature ($FeatureName + 'Feature')
        {
            Ensure = $EnsureString
            Name   = $FeatureName
        }
    }
}
```

可以在 Azure Automation State Configuration 门户或 Azure PowerShell 中编译使用基本参数的 DSC 配置：

#### <a name="portal"></a>门户

在门户中，可在单击“编译”后输入参数值。

![配置编译参数](./media/automation-dsc-compile/DSC_compiling_1.png)

#### <a name="azure-powershell"></a>Azure PowerShell

PowerShell 需要[哈希表](/powershell/module/microsoft.powershell.core/about/about_hash_tables)中的参数，其中的键必须与参数名称匹配，值等于参数值。

```powershell
$Parameters = @{
    'FeatureName' = 'Web-Server'
    'IsPresent' = $False
}

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ParametersExample' -Parameters $Parameters
```

若要了解如何将 PSCredentials 作为参数传递，请参阅下面的[凭据资产](#credential-assets)。

### <a name="compile-configurations-containing-composite-resources-in-azure-automation"></a>在 Azure 自动化中包含复合资源的编译配置

使用**复合资源**功能，可以在配置内将 DSC 配置用作嵌套资源。 这样，便可将多个配置应用到单个资源。 请参阅[复合资源：将 DSC 配置用作资源](/powershell/scripting/dsc/resources/authoringresourcecomposite)可详细了解复合资源。

> [!NOTE]
> 对于包含要正确编译的**组合资源**的配置，必须首先确保该复合依赖的所有 DSC 资源首先导入到 Azure 自动化中。

添加 DSC**组合资源**与将任何 PowerShell 模块添加到 Azure 自动化没有什么不同。
在[Azure 自动化中管理模块](/azure/automation/shared-resources/modules)一文中介绍了此过程的分步说明。

### <a name="manage-configurationdata-when-compiling-configurations-in-azure-automation"></a>在 Azure 自动化中编译配置时管理 ConfigurationData

通过 **ConfigurationData** 可在使用 PowerShell DSC 时分开结构化配置与任何环境特定配置。 有关 [ConfigurationData](https://blogs.msdn.com/b/powershell/archive/2014/01/09/continuous-deployment-using-dsc-with-minimal-change.aspx) 的详细信息，请参阅**区分 PowerShell DSC 中的“What”与“Where”** 。

> [!NOTE]
> 当使用 Azure PowerShell 而不是在 Azure 门户中进行编译时，可以使用**ConfigurationData** 。

以下示例 DSC 配置通过 **$ConfigurationData** 和 **$AllNodes** 关键字来使用 **ConfigurationData**。 在本示例中还需要 [**xWebAdministration**](https://www.powershellgallery.com/packages/xWebAdministration/) 模块：

```powershell
Configuration ConfigurationDataSample
{
    Import-DscResource -ModuleName xWebAdministration -Name MSFT_xWebsite

    Write-Verbose $ConfigurationData.NonNodeData.SomeMessage

    Node $AllNodes.Where{$_.Role -eq 'WebServer'}.NodeName
    {
        xWebsite Site
        {
            Name         = $Node.SiteName
            PhysicalPath = $Node.SiteContents
            Ensure       = 'Present'
        }
    }
}
```

可以通过 Windows PowerShell 编译上述 DSC 配置。 以下脚本将两个节点配置添加到 Azure 自动化状态配置请求服务： **configurationdatasample.myvm1. MyVM1**和**configurationdatasample.myvm1**：

```powershell
$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = 'MyVM1'
            Role = 'WebServer'
        },
        @{
            NodeName = 'MyVM2'
            Role = 'SQLServer'
        },
        @{
            NodeName = 'MyVM3'
            Role = 'WebServer'
        }
    )

    NonNodeData = @{
        SomeMessage = 'I love Azure Automation State Configuration and DSC!'
    }
}

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ConfigurationDataSample' -ConfigurationData $ConfigData
```

### <a name="work-with-assets-in-azure-automation-during-compilation"></a>在编译期间使用 Azure 自动化中的资产

Azure Automation State Configuration 和 Runbook 中的资产引用是相同的。 有关详细信息，请参阅以下主题：

- [证书](automation-certificates.md)
- [连接](automation-connections.md)
- [凭据](automation-credentials.md)
- [变量](automation-variables.md)

#### <a name="credential-assets"></a>凭据资产

Azure 自动化中的 DSC 配置可以使用 `Get-AutomationPSCredential` cmdlet 引用自动化凭据资产。 如果配置具有包含 PSCredential 类型的参数，则可以通过将 Azure 自动化凭据资产的字符串名称传递给 cmdlet 来检索凭据，从而使用  **cmdlet**`Get-AutomationPSCredential`。 然后可以将该对象用于需要 PSCredential 对象的参数。 在后台将检索具有该名称的 Azure 自动化凭据资产并将其传递给配置。 以下示例在操作中演示了这一点。

要在节点配置（MOF 配置文档）中保持凭据的安全，需要在节点配置 MOF 文件中为凭据加密。 不过，目前必须告知 PowerShell DSC 在节点配置 MOF 生成期间以纯文本形式输出凭据是可行的，因为 PowerShell DSC 并不知道在通过编译作业生成 MOF 文件之后 Azure 自动化将加密整个文件。

可以告诉 PowerShell DSC，使用配置数据在生成的节点配置 Mof 中以纯文本形式输出凭据是正确的。 应针对每个出现在 DSC 配置中且使用凭据的节点块名称，通过 `PSDscAllowPlainTextPassword = $true`ConfigurationData**传递**。

以下示例演示使用自动化凭据资产的 DSC 配置。

```powershell
Configuration CredentialSample
{
    Import-DscResource -ModuleName PSDesiredStateConfiguration
    $Cred = Get-AutomationPSCredential 'SomeCredentialAsset'

    Node $AllNodes.NodeName
    {
        File ExampleFile
        {
            SourcePath      = '\\Server\share\path\file.ext'
            DestinationPath = 'C:\destinationPath'
            Credential      = $Cred
        }
    }
}
```

可以使用 PowerShell 编译上述 DSC 配置。 以下 PowerShell 将两个节点配置添加到 Azure Automation State Configuration“拉”服务器：**CredentialSample.MyVM1** 和 **CredentialSample.MyVM2**。

```powershell
$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = '*'
            PSDscAllowPlainTextPassword = $True
        },
        @{
            NodeName = 'MyVM1'
        },
        @{
            NodeName = 'MyVM2'
        }
    )
}

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'CredentialSample' -ConfigurationData $ConfigData
```

> [!NOTE]
> 编译完成后，可能会收到一条错误消息：由于已导入“Microsoft.PowerShell.Management”管理单元，因此未导入“Microsoft.PowerShell.Management”模块。 可以安全地忽略此警告。

## <a name="compiling-configurations-in-windows-powershell"></a>在 Windows PowerShell 中编译配置

还可以导入已在 Azure 外部编译的节点配置 (MOF)。
这包括从开发人员工作站或在[Azure DevOps](https://dev.azure.com)等服务中进行编译。
此方法有多个优点，包括性能和可靠性。
Windows PowerShell 中的编译还提供了用于对配置内容进行签名的选项。
已签名的节点配置由 DSC 代理在托管节点上进行本地验证，确保应用于节点的配置来自于授权源。

> [!NOTE]
> 节点配置文件不得大于 1 MB，以便将其导入 Azure 自动化。

若要详细了解如何为节点配置签名，请参阅 [WMF 5.1 中的改进 - 如何为配置和模块签名](/powershell/scripting/wmf/whats-new/dsc-improvements#dsc-module-and-configuration-signing-validations)。

### <a name="compile-a-configuration-in-windows-powershell"></a>在 Windows PowerShell 中编译配置

在 Windows PowerShell 中编译 DSC 配置的过程包含在 PowerShell DSC 文档中[，即编写、编译和应用配置](/powershell/scripting/dsc/configurations/write-compile-apply-configuration#compile-the-configuration)。
这可以从开发人员工作站或在生成服务（例如[Azure DevOps](https://dev.azure.com)）中执行。

然后，可以将通过编译配置生成的 MOF 文件直接导入到 Azure 状态配置服务中。

### <a name="import-a-node-configuration-in-the-azure-portal"></a>导入 Azure 门户中的节点配置

1. 在“自动化帐户”中的“配置管理”下，单击“State Configuration (DSC)”。
1. 在“State Configuration (DSC)”页中，依次单击“配置”选项卡、“+ 添加”。
1. 在“导入”页中，单击“节点配置文件”文本框旁边的文件夹图标，在本地计算机上浏览节点配置文件 (MOF)。

   ![浏览本地文件](./media/automation-dsc-compile/import-browse.png)

1. 在“配置名称”文本框中，输入名称。 此名称必须与编译节点配置的配置名称匹配。
1. 单击“确定”。

### <a name="import-a-node-configuration-with-azure-powershell"></a>使用 Azure PowerShell 导入节点配置

可以使用[AzAutomationDscNodeConfiguration](/powershell/module/az.automation/import-azautomationdscnodeconfiguration) cmdlet 将节点配置导入自动化帐户。

```powershell
Import-AzAutomationDscNodeConfiguration -AutomationAccountName 'MyAutomationAccount' -ResourceGroupName 'MyResourceGroup' -ConfigurationName 'MyNodeConfiguration' -Path 'C:\MyConfigurations\TestVM1.mof'
```

## <a name="next-steps"></a>后续步骤

- 若要开始，请参阅 [Azure 自动化状态配置入门] （Automation-dsc-started.md。
- 若要了解如何编译 DSC 配置，以便可以将其分配给目标节点，请参阅[在 Azure 自动化状态配置中编译配置](automation-dsc-compile.md)。
- 有关 PowerShell cmdlet 参考，请参阅[Azure 自动化状态配置 cmdlet](/powershell/module/az.automation)。
- 有关定价信息，请参阅[Azure 自动化状态配置定价](https://azure.microsoft.com/pricing/details/automation/)。
- 若要查看在持续部署管道中使用 Azure 自动化状态配置的示例，请参阅[使用 Azure 自动化状态配置和 Chocolatey 进行连续部署](automation-dsc-cd-chocolatey.md)。
