---
title: 在 Azure Automation State Configuration 中编译配置
description: 本文介绍如何编译 Azure 自动化的 Desired State Configuration (DSC) 配置。
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.date: 09/10/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 58aa310316a31eb63ca8dd614b60fb4bad73d997
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2019
ms.locfileid: "71959991"
---
# <a name="compiling-dsc-configurations-in-azure-automation-state-configuration"></a>在 Automation State Configuration 中编译 DSC 配置

使用 Azure Automation State Configuration 时可通过两种方法编译 Desired State Configuration (DSC)：使用 Azure，或者使用 Windows PowerShell。 下表可帮助你根据每种方法的特征确定何时应使用哪种方法：

- Azure State Configuration 编译服务
  - 使用交互式用户界面的初级方法
   - 轻松跟踪作业状态

- Windows PowerShell
  - 从本地工作站上的 Windows PowerShell 或生成服务调用
  - 与开发测试管道集成
  - 提供复杂的参数值
  - 大规模使用节点和非节点数据
  - 显著的性能提升

## <a name="compiling-a-dsc-configuration-in-azure-state-configuration"></a>在 Azure State Configuration 中编译 DSC 配置

### <a name="portal"></a>门户

1. 从自动化帐户中，单击“State Configuration (DSC)”。
1. 单击“配置”选项卡，然后单击要编译的配置名称。
1. 单击“编译”。
1. 如果该配置没有参数，系统会提示确认是否要进行编译。 如果该配置有参数，则会打开“编译配置”边栏选项卡让用户提供参数值。 有关参数的更多详细信息，请参阅下面的[**基本参数**](#basic-parameters)部分。
1. “编译作业”页面随即打开，用户可跟踪编译作业的状态，并可将由于此作业引起的节点配置（MOF 配置文档）放在 Azure Automation State Configuration“拉”服务器上。

### <a name="azure-powershell"></a>Azure PowerShell

可以在 Windows PowerShell 中使用 [`Start-AzureRmAutomationDscCompilationJob`](/powershell/module/azurerm.automation/start-azurermautomationdsccompilationjob) 开始编译。 以下示例代码启动名为 **SampleConfig** 的 DSC 配置编译。

```powershell
Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'
```

`Start-AzureRmAutomationDscCompilationJob` 返回可用于跟踪作业状态的编译作业对象。 然后，可以将此编译作业对象与 [`Get-AzureRmAutomationDscCompilationJob`](/powershell/module/azurerm.automation/get-azurermautomationdsccompilationjob) 一起使用
以确定编译作业的状态，与 [`Get-AzureRmAutomationDscCompilationJobOutput`](/powershell/module/azurerm.automation/get-azurermautomationdsccompilationjoboutput) 一起使用
以查看其流（输出）。 以下示例代码启动 **SampleConfig** 配置的编译，并在编译完成后显示其流。

```powershell
$CompilationJob = Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'

while($null -eq $CompilationJob.EndTime -and $null -eq $CompilationJob.Exception)
{
    $CompilationJob = $CompilationJob | Get-AzureRmAutomationDscCompilationJob
    Start-Sleep -Seconds 3
}

$CompilationJob | Get-AzureRmAutomationDscCompilationJobOutput –Stream Any
```

###  <a name="basic-parameters"></a>基本参数

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

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ParametersExample' -Parameters $Parameters
```

若要了解如何将 PSCredentials 作为参数传递，请参阅下面的[凭据资产](#credential-assets)。

### <a name="compiling-configurations-in-azure-automation-that-contain-composite-resources"></a>在 Azure 自动化中编译包含复合资源的配置

借助**复合资源**，可将 DSC 配置用作某个配置中的嵌套资源。 这样，便可将多个配置应用到单个资源。 请参阅[复合资源：将 DSC 配置用作资源](/powershell/dsc/authoringresourcecomposite)，了解有关**复合资源**的详细信息。

> [!NOTE]
> 若要正确编译包含**复合资源**的配置，首先必须确保复合资源所依赖的所有 DSC 资源已导入到 Azure 自动化中。

添加 DSC**组合资源**与将任何 PowerShell 模块添加到 Azure 自动化没有什么不同。
在[Azure 自动化中管理模块](/azure/automation/shared-resources/modules)一文中介绍了此过程的分步说明。

### <a name="managing-configurationdata-when-compiling-configuration-in-azure-automation"></a>在 Azure Automation 中编译配置时管理 ConfigurationData

通过 **ConfigurationData** 可在使用 PowerShell DSC 时分开结构化配置与任何环境特定配置。 有关 **ConfigurationData** 的详细信息，请参阅[区分 PowerShell DSC 中的“What”与“Where”](https://blogs.msdn.com/b/powershell/archive/2014/01/09/continuous-deployment-using-dsc-with-minimal-change.aspx)。

> [!NOTE]
> 使用 Azure PowerShell 在 Azure Automation State Configuration 中进行编译时，可使用 **ConfigurationData**，但在 Azure 门中编译时不可使用。

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

可以使用 Windows PowerShell 编译上述 DSC 配置。 以下脚本将两个节点配置添加到 Azure Automation State Configuration 拉取服务：**ConfigurationDataSample.MyVM1** 和 **ConfigurationDataSample.MyVM3**：

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

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ConfigurationDataSample' -ConfigurationData $ConfigData
```

### <a name="working-with-assets-in-azure-automation-during-compilation"></a>编译时在 Azure 自动化中使用资产

Azure Automation State Configuration 和 Runbook 中的资产引用是相同的。 有关详细信息，请参阅以下主题：

- [证书](automation-certificates.md)
- [连接](automation-connections.md)
- [凭据](automation-credentials.md)
- [变量](automation-variables.md)

#### <a name="credential-assets"></a>凭据资产

Azure 自动化中的 DSC 配置可以使用 `Get-AutomationPSCredential` cmdlet 引用自动化凭据资产。 如果配置具有包含 PSCredential 类型的参数，则可以通过将 Azure 自动化凭据资产的字符串名称传递给 cmdlet 来检索凭据，从而使用 `Get-AutomationPSCredential` cmdlet。 然后可以将该对象用于需要 PSCredential 对象的参数。 在后台将检索具有该名称的 Azure 自动化凭据资产并将其传递给配置。 以下示例在操作中演示了这一点。

要在节点配置（MOF 配置文档）中保持凭据的安全，需要在节点配置 MOF 文件中为凭据加密。 不过，目前必须告知 PowerShell DSC 在节点配置 MOF 生成期间以纯文本形式输出凭据是可行的，因为 PowerShell DSC 并不知道在通过编译作业生成 MOF 文件之后 Azure 自动化将加密整个文件。

可告知 PowerShell DSC，使用配置数据在生成的节点配置 MOF 中以纯文本形式输出凭据是可行的。 应针对每个出现在 DSC 配置中且使用凭据的节点块名称，通过 **ConfigurationData** 传递 `PSDscAllowPlainTextPassword = $true`。

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

可以使用 PowerShell 编译上述 DSC 配置。 以下 PowerShell 将两个节点配置添加到 Azure Automation State Configuration 拉取服务器：**CredentialSample.MyVM1** 和 **CredentialSample.MyVM2**。

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

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'CredentialSample' -ConfigurationData $ConfigData
```

> [!NOTE]
> 编译完成后，可能会收到一条错误消息：由于已导入“Microsoft.PowerShell.Management”管理单元，因此未导入“Microsoft.PowerShell.Management”模块。 可以安全地忽略此警告。

## <a name="compiling-configurations-in-windows-powershell-and-publishing-to-azure-automation"></a>在 Windows PowerShell 中编译配置并将其发布到 Azure 自动化

还可以导入已在 Azure 外部编译的节点配置 (MOF)。
这包括从开发人员工作站或在[Azure DevOps](https://dev.azure.com)等服务中进行编译。
此方法有多个缺点，其中包括性能和可靠性方面的缺点。
在 Windows PowerShell 中编译还提供了签署配置内容的选项。
已签名的节点配置由 DSC 代理在托管节点上进行本地验证，确保应用于节点的配置来自于授权源。

> [!NOTE]
> 节点配置文件不得大于 1 MB，以便将其导入 Azure 自动化。

若要详细了解如何为节点配置签名，请参阅 [WMF 5.1 中的改进 - 如何为配置和模块签名](/powershell/scripting/wmf/whats-new/dsc-improvements#dsc-module-and-configuration-signing-validations)。

### <a name="compiling-a-configuration-in-windows-powershell"></a>在 Windows PowerShell 中编译配置

在 Windows PowerShell 中编译 DSC 配置的过程包含在以下 PowerShell DSC 文档中：[编写、编译和应用配置](/powershell/dsc/configurations/write-compile-apply-configuration#compile-the-configuration)。
这可以从开发人员工作站或在生成服务（例如[Azure DevOps](https://dev.azure.com)）中执行。

然后，可以将通过编译配置生成的一个或多个 MOF 文件直接导入到 Azure State Configuration 服务中。

### <a name="importing-a-node-configuration-in-the-azure-portal"></a>在 Azure 门户中导入节点配置

1. 在“自动化帐户”中的“配置管理”下，单击“State Configuration (DSC)”。
1. 在“State Configuration (DSC)”页中，依次单击“配置”选项卡、“+ 添加”。
1. 在“导入”页中，单击“节点配置文件”文本框旁边的文件夹图标，在本地计算机上浏览节点配置文件 (MOF)。

   ![浏览本地文件](./media/automation-dsc-compile/import-browse.png)

1. 在“配置名称”文本框中，输入名称。 此名称必须与编译节点配置的配置名称匹配。
1. 单击 **“确定”** 。

### <a name="importing-a-node-configuration-with-azure-powershell"></a>使用 Azure PowerShell 导入节点配置

可以使用 [Import-AzureRmAutomationDscNodeConfiguration](/powershell/module/azurerm.automation/import-azurermautomationdscnodeconfiguration) cmdlet 将节点配置导入自动化帐户。

```powershell
Import-AzureRmAutomationDscNodeConfiguration -AutomationAccountName 'MyAutomationAccount' -ResourceGroupName 'MyResourceGroup' -ConfigurationName 'MyNodeConfiguration' -Path 'C:\MyConfigurations\TestVM1.mof'
```

## <a name="next-steps"></a>后续步骤

- 有关入门信息，请参阅 [Azure Automation State Configuration 入门](automation-dsc-getting-started.md)
- 若要了解如何编译 DSC 配置，以便将它们分配给目标节点，请参阅[在 Azure Automation State Configuration 中编译配置](automation-dsc-compile.md)
- 有关 PowerShell cmdlet 参考，请参阅 [Azure Automation State Configuration cmdlet](/powershell/module/azurerm.automation/#automation)
- 有关定价信息，请参阅 [Azure Automation State Configuration 定价](https://azure.microsoft.com/pricing/details/automation/)
- 若要查看在持续部署管道中使用 Azure Automation State Configuration 的示例，请参阅[使用 Azure Automation State Configuration 和 Chocolatey 进行持续部署](automation-dsc-cd-chocolatey.md)
