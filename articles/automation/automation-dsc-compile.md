---
title: 在 Azure 自动化状态配置中编译 DSC 配置
description: 本文介绍如何为 Azure 自动化编译 Desired State Configuration (DSC) 配置。
services: automation
ms.subservice: dsc
ms.date: 04/06/2020
ms.topic: conceptual
ms.openlocfilehash: eeb60012ae607e49b1249fda13222cb2fa753911
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2020
ms.locfileid: "82996060"
---
# <a name="compile-dsc-configurations-in-azure-automation-state-configuration"></a>在 Azure 自动化状态配置中编译 DSC 配置

可通过以下方式在 Azure 自动化 State Configuration 中编译 Desired State Configuration (DSC) 配置：

- Azure State Configuration 编译服务
  - 使用交互式用户界面的初级方法
   - 轻松跟踪作业状态

- Windows PowerShell
  - 从本地工作站上的 Windows PowerShell 调用或生成服务
  - 与开发测试管道集成
  - 提供复杂的参数值
  - 大规模使用节点和非节点数据
  - 显著的性能提升

还可以通过 azure 所需状态配置（DSC）扩展使用 Azure 资源管理器模板将配置推送到 Azure Vm。 Azure DSC 扩展使用 Azure VM 代理框架来传送、启用和报告 Azure VM 上运行的 DSC 配置。 有关使用 Azure 资源管理器模板的编译详细信息，请参阅[使用 azure 资源管理器模板所需的状态配置扩展](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template#details)。 

## <a name="compiling-a-dsc-configuration-in-azure-state-configuration"></a>在 Azure 状态配置中编译 DSC 配置

### <a name="portal"></a>门户

1. 在自动化帐户中，单击 "**状态配置（DSC）**"。
1. 单击“配置”选项卡，然后单击要编译的配置名称。****
1. 单击“编译”****。
1. 如果该配置没有参数，系统将提示您确认是否要对其进行编译。 如果配置有参数，则会打开 "**编译配置**" 边栏选项卡，以便您提供参数值。
1. 将打开 "编译作业" 页，以便您可以跟踪编译作业状态。 你还可以使用此页跟踪放置在 Azure 自动化状态配置请求服务器上的节点配置（MOF 配置文档）。

### <a name="azure-powershell"></a>Azure PowerShell

可以使用[AzAutomationDscCompilationJob](/powershell/module/az.automation/start-azautomationdsccompilationjob)开始使用 Windows PowerShell 进行编译。 下面的示例代码开始编译名为**SampleConfig**的 DSC 配置。

```powershell
Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'
```

`Start-AzAutomationDscCompilationJob`返回可用于跟踪作业状态的编译作业对象。 然后，可以使用此编译作业对象与[AzAutomationDscCompilationJob](/powershell/module/az.automation/get-azautomationdsccompilationjob)来确定编译作业的状态，并使用[AzAutomationDscCompilationJobOutput](/powershell/module/az.automation/get-azautomationdscconfiguration)查看其流（输出）。 下面的示例启动 SampleConfig 配置的编译，等待它完成，然后显示其流。

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

下面的示例使用`FeatureName`和`IsPresent`参数来确定编译期间生成的**ParametersExample**节点配置中的属性值。

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

可以在 Azure 自动化状态配置门户或 Azure PowerShell 中编译使用基本参数的 DSC 配置。

#### <a name="portal"></a>门户

在门户中，可在单击“编译”**** 后输入参数值。

![配置编译参数](./media/automation-dsc-compile/DSC_compiling_1.png)

#### <a name="azure-powershell"></a>Azure PowerShell

PowerShell 需要[哈希表](/powershell/module/microsoft.powershell.core/about/about_hash_tables)中的参数，其中密钥与参数名称匹配，值等于参数值。

```powershell
$Parameters = @{
    'FeatureName' = 'Web-Server'
    'IsPresent' = $False
}

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ParametersExample' -Parameters $Parameters
```

有关以参数形式`PSCredential`传递对象的信息，请参阅[凭据资产](#credential-assets)。

### <a name="compile-configurations-containing-composite-resources-in-azure-automation"></a>在 Azure 自动化中包含复合资源的编译配置

使用**复合资源**功能，可以在配置中将 DSC 配置用作嵌套资源。 此功能允许将多个配置应用到单个资源。 请参阅[复合资源：将 DSC 配置用作资源](/powershell/scripting/dsc/resources/authoringresourcecomposite)可详细了解复合资源。

> [!NOTE]
> 为了正确编译包含复合资源的配置，必须首先将该组合所依赖的任何 DSC 资源导入到 Azure 自动化中。 添加 DSC 组合资源与将任何 PowerShell 模块添加到 Azure 自动化没有什么不同。 此过程记录在[Azure 自动化中的管理模块](/azure/automation/shared-resources/modules)中。

### <a name="manage-configurationdata-when-compiling-configurations-in-azure-automation"></a>在 Azure 自动化中编译配置时管理 ConfigurationData

`ConfigurationData`是一个内置 DSC 参数，可让你在使用 PowerShell DSC 时将结构配置与任何环境特定的配置分开。 有关详细信息，请参阅[从 POWERSHELL DSC 中的 "位置" 分隔 "内容](https://devblogs.microsoft.com/powershell/separating-what-from-where-in-powershell-dsc/)"。

> [!NOTE]
> 在 Azure 自动化状态配置中进行编译时，可以`ConfigurationData`在 Azure PowerShell 中使用，但不能在 Azure 门户中使用。

以下示例 DSC 配置通过`ConfigurationData` `$ConfigurationData`和`$AllNodes`关键字使用。 对于本示例，还需要[xWebAdministration 模块](https://www.powershellgallery.com/packages/xWebAdministration/)。

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

可以使用 Windows PowerShell 编译上述 DSC 配置。 以下脚本将两个节点配置添加到 Azure 自动化状态配置请求服务： **configurationdatasample.myvm1. MyVM1**和**configurationdatasample.myvm1。**

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

Azure 自动化状态配置和 runbook 中的资产引用相同。 有关详细信息，请参阅以下主题：

- [证书](automation-certificates.md)
- [连接](automation-connections.md)
- [凭据](automation-credentials.md)
- [变量](automation-variables.md)

#### <a name="credential-assets"></a>凭据资产

Azure 自动化中的 DSC 配置可以使用 `Get-AutomationPSCredential` cmdlet 引用自动化凭据资产。 如果配置具有指定`PSCredential`对象的参数，请使用`Get-AutomationPSCredential`通过将 Azure 自动化凭据资产的字符串名称传递到 cmdlet 来检索凭据。 然后对需要`PSCredential`对象的参数使用该对象。 在后台将检索具有该名称的 Azure 自动化凭据资产并将其传递给配置。 下面的示例演示了这种操作方案。

要在节点配置（MOF 配置文档）中保持凭据的安全，需要在节点配置 MOF 文件中为凭据加密。 目前，在节点配置 MOF 生成期间，你必须将 PowerShell DSC 权限指定为以纯文本格式输出凭据。 PowerShell DSC 并不知道在通过编译作业生成整个 MOF 文件后，Azure 自动化会将其加密。

可以告诉 PowerShell DSC，使用配置数据在生成的节点配置 Mof 中以纯文本形式输出凭据是正确的。 应`ConfigurationData`为 DSC `PSDscAllowPlainTextPassword = $true`配置中显示的每个节点块名称传递，并使用凭据。

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

可以使用 PowerShell 编译上述 DSC 配置。 以下 PowerShell 代码将两个节点配置添加到 Azure 自动化状态配置请求服务器： **credentialsample.myvm1. MyVM1**和**credentialsample.myvm1**。

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
> 编译完成后，你可能会收到错误消息`The 'Microsoft.PowerShell.Management' module was not imported because the 'Microsoft.PowerShell.Management' snap-in was already imported.` ，你可以放心地忽略此消息。

## <a name="compiling-your-dsc-configuration-in-windows-powershell"></a>在 Windows PowerShell 中编译 DSC 配置

还可以导入已在 Azure 外部编译的节点配置（MOF 文件）。 导入包括从开发人员工作站或在[Azure DevOps](https://dev.azure.com)等服务中进行编译。 此方法具有多项优势，包括性能和可靠性。

在 Windows PowerShell 中编译还提供了签署配置内容的选项。 DSC 代理在托管节点上本地验证已签名的节点配置。 验证可确保应用于节点的配置来自授权源。

> [!NOTE]
> 节点配置文件不得大于 1 MB，以允许 Azure 自动化导入它。

有关对节点配置进行签名的详细信息，请参阅[WMF 5.1 中的改进-如何对配置和模块进行签名](/powershell/scripting/wmf/whats-new/dsc-improvements#dsc-module-and-configuration-signing-validations)。

### <a name="compile-the-dsc-configuration"></a>编译 DSC 配置

在 Windows PowerShell 中编译 DSC 配置的过程包含在以下 PowerShell DSC 文档中：[编写、编译和应用配置](/powershell/scripting/dsc/configurations/write-compile-apply-configuration#compile-the-configuration)。
可以从开发人员工作站或在生成服务（例如[Azure DevOps](https://dev.azure.com)）中执行此过程。 然后，你可以将通过编译配置生成的 MOF 文件导入到 Azure 状态配置服务中。

### <a name="import-a-node-configuration-in-the-azure-portal"></a>导入 Azure 门户中的节点配置

1. 在自动化帐户中，单击 "**配置管理**" 下的 "**状态配置（DSC）** "。
1. 在 "状态配置（DSC）" 页上，单击 "**配置**" 选项卡，然后单击 "**添加**"。
1. 在 "导入" 页上，单击 "**节点配置文件**" 字段旁边的文件夹图标，浏览本地计算机上的节点配置 MOF 文件。

   ![浏览本地文件](./media/automation-dsc-compile/import-browse.png)

1. 在 "**配置名称**" 字段中输入名称。 此名称必须与编译节点配置的配置名称匹配。
1. 单击" **确定**"。

### <a name="import-a-node-configuration-with-azure-powershell"></a>使用 Azure PowerShell 导入节点配置

可以使用[AzAutomationDscNodeConfiguration](/powershell/module/az.automation/import-azautomationdscnodeconfiguration) cmdlet 将节点配置导入自动化帐户。

```powershell
Import-AzAutomationDscNodeConfiguration -AutomationAccountName 'MyAutomationAccount' -ResourceGroupName 'MyResourceGroup' -ConfigurationName 'MyNodeConfiguration' -Path 'C:\MyConfigurations\TestVM1.mof'
```

## <a name="next-steps"></a>后续步骤

- 若要开始，请参阅[Azure 自动化状态配置](automation-dsc-getting-started.md)入门。
- 若要了解如何编译 DSC 配置，以便可以将其分配给目标节点，请参阅[在 Azure 自动化状态配置中编译配置](automation-dsc-compile.md)。
- 有关 PowerShell cmdlet 参考，请参阅 [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
)。
- 有关定价信息，请参阅[Azure 自动化状态配置定价](https://azure.microsoft.com/pricing/details/automation/)。
- 若要查看在持续部署管道中使用 Azure 自动化状态配置的示例，请参阅[使用 Azure 自动化状态配置和 Chocolatey 持续部署到虚拟机](automation-dsc-cd-chocolatey.md)。
