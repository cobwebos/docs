---
title: 在 Azure 自动化 DSC 中编译配置
description: 本文介绍如何编译 Azure 自动化的 Desired State Configuration (DSC) 配置。
services: automation
ms.service: automation
ms.component: dsc
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 897681cda31b2f187fca64e77621b7dc5ed4dfae
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/17/2018
ms.locfileid: "39072103"
---
# <a name="compiling-configurations-in-azure-automation-dsc"></a>在 Azure 自动化 DSC 中编译配置

使用 Azure 自动化时可通过两种方法编译 Desired State Configuration (DSC) ：使用 Azure 门户，或者使用 Windows PowerShell。 下表可帮助你根据每种方法的特征确定何时应使用哪种方法：

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

确定编译方法后，可以按照以下过程开始编译。

## <a name="compiling-a-dsc-configuration-with-the-azure-portal"></a>使用 Azure 门户编译 DSC 配置

1. 从自动化帐户中，单击“DSC 配置”。
2. 单击某个配置以打开其边栏选项卡。
3. 单击“编译”。
4. 如果该配置没有参数，系统会提示确认是否要进行编译。 如果该配置有参数，则会打开“编译配置”边栏选项卡让用户提供参数值。 有关参数的更多详细信息，请参阅下面的[**基本参数**](#basic-parameters)部分。
5. “编译作业”边栏选项卡随即打开，用户可跟踪编译作业的状态，且会将由于此作业引起的节点配置（MOF 配置文档）放在 Azure 自动化 DSC“拉”服务器上。

## <a name="compiling-a-dsc-configuration-with-windows-powershell"></a>使用 Windows PowerShell 编译 DSC 配置

可以在 Windows PowerShell 中使用 [`Start-AzureRmAutomationDscCompilationJob`](/powershell/module/azurerm.automation/start-azurermautomationdsccompilationjob) 开始编译。 以下示例代码启动名为 **SampleConfig** 的 DSC 配置编译。

```powershell
Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "SampleConfig"
```

`Start-AzureRmAutomationDscCompilationJob` 返回可用于跟踪作业状态的编译作业对象。 接下来，可使用此编译作业对象与 [`Get-AzureRmAutomationDscCompilationJob`](/powershell/module/azurerm.automation/get-azurermautomationdsccompilationjob) 来确定编译作业的状态，并使用 [`Get-AzureRmAutomationDscCompilationJobOutput`](/powershell/module/azurerm.automation/get-azurermautomationdsccompilationjoboutput) 查看其流（输出）。 以下示例代码启动 **SampleConfig** 配置的编译，并在编译完成后显示其流。

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

可以在 Azure 自动化 DSC 门户或 Azure PowerShell 中编译使用基本参数的 DSC 配置：

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

## <a name="composite-resources"></a>复合资源

借助**复合资源**，可将 DSC 配置用作某个配置中的嵌套资源。 这样，便可将多个配置应用到单个资源。 有关**复合资源**的详细信息，请参阅[复合资源：将 DSC 配置用作资源](https://docs.microsoft.com/powershell/dsc/authoringresourcecomposite)

> [!NOTE]
> 若要正确编译**复合资源**，首先必须确保复合资源所依赖的所有 DSC 资源已事先安装在 Azure 自动化帐户模块存储库中，否则复合资源不会正确导入。

若要添加 DSC **复合资源**，必须将资源模块添加到存档 (*.zip)。 在 Azure 自动化帐户中转到“模块”存储库。 然后单击“添加模块”按钮。

![添加模块](./media/automation-dsc-compile/add_module.png)

导航到存档所在的目录。 选择该存档文件，单击“确定”。

![选择模块](./media/automation-dsc-compile/select_dscresource.png)

将会回到模块目录，在其中可以监视**复合资源**在解包和注册到 Azure 自动化时的状态。

![导入复合资源](./media/automation-dsc-compile/register_composite_resource.png)

注册模块后，可以单击它，以验证**复合资源**现在是否可在配置中使用。

![验证复合资源](./media/automation-dsc-compile/validate_composite_resource.png)

然后，可在配置中调用**复合资源**，如下所示：

```powershell

    Node ($AllNodes.Where{$_.Role -eq "WebServer"}).NodeName
    {
            
            JoinDomain DomainJoin
            {
                DomainName = $DomainName
                Admincreds = $Admincreds
            }

            PSWAWebServer InstallPSWAWebServer
            {
                DependsOn = "[JoinDomain]DomainJoin"
            }        
    }

```

## <a name="configurationdata"></a>ConfigurationData
通过 **ConfigurationData** 可在使用 PowerShell DSC 时分开结构化配置与任何环境特定配置。 有关 **ConfigurationData** 的详细信息，请参阅[区分 PowerShell DSC 中的“What”与“Where”](http://blogs.msdn.com/b/powershell/archive/2014/01/09/continuous-deployment-using-dsc-with-minimal-change.aspx)。

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

Azure 自动化中的 DSC 配置可以使用 `Get-AutomationPSCredential` 引用自动化凭据资产。 如果配置具有包含 PSCredential 类型的参数，则可以通过将 Azure 自动化凭据资产的字符串名称传递给 cmdlet 来检索凭据，从而使用 `Get-AutomationPSCredential` cmdlet。 然后可以将该对象用于需要 PSCredential 对象的参数。 在后台将检索具有该名称的 Azure 自动化凭据资产并将其传递给配置。 以下示例在操作中演示了这一点。

要在节点配置（MOF 配置文档）中保持凭据的安全，需要在节点配置 MOF 文件中为凭据加密。 不过，目前必须告知 PowerShell DSC 在节点配置 MOF 生成期间以纯文本形式输出凭据是可行的，因为 PowerShell DSC 并不知道在通过编译作业生成 MOF 文件之后 Azure 自动化将加密整个文件。

可告知 PowerShell DSC，使用 [**ConfigurationData**](#configurationdata) 在生成的节点配置 MOF 中以纯文本形式输出凭据是可行的。 应针对每个出现在 DSC 配置中且使用凭据的节点块名称，通过 **ConfigurationData** 传递 `PSDscAllowPlainTextPassword = $true`。

以下示例演示使用自动化凭据资产的 DSC 配置。

```powershell
Configuration CredentialSample
{
    Import-DscResource -ModuleName PSDesiredStateConfiguration
    $Cred = Get-AutomationPSCredential "SomeCredentialAsset"

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

> [!NOTE]
> 编译完成后，可能会收到一条错误消息：由于已导入“Microsoft.PowerShell.Management”管理单元，因此未导入“Microsoft.PowerShell.Management”模块。 可以安全地忽略此警告。

## <a name="importing-node-configurations"></a>导入节点配置

还可以导入已在 Azure 外部编译的节点配置 (MOF)。 其中一个优点是可以对节点配置进行签名。
已签名的节点配置由 DSC 代理在托管节点上进行本地验证，确保应用于节点的配置来自于授权源。

> [!NOTE]
> 可以将已签名的配置导入 Azure 自动化帐户，但 Azure 自动化目前不支持编译已签名的配置。

> [!NOTE]
> 节点配置文件不得大于 1 MB，以便将其导入 Azure 自动化。

可以在以下网页中了解如何对节点配置进行签名：https://msdn.microsoft.com/powershell/wmf/5.1/dsc-improvements#how-to-sign-configuration-and-module。

### <a name="importing-a-node-configuration-in-the-azure-portal"></a>在 Azure 门户中导入节点配置

1. 在“自动化帐户”页上的“配置管理”下，单击“DSC 节点配置”。

    ![DSC 节点配置](./media/automation-dsc-compile/node-config.png)
2. 在“DSC 节点配置”边栏选项卡中，单击“添加 NodeConfiguration”。
3. 在“导入”边栏选项卡中，单击“节点配置文件”文本框旁边的文件夹图标，在本地计算机上浏览节点配置文件 (MOF)。

    ![浏览本地文件](./media/automation-dsc-compile/import-browse.png)
4. 在“配置名称”文本框中，输入名称。 此名称必须与编译节点配置的配置名称匹配。
5. 单击“确定”。

### <a name="importing-a-node-configuration-with-powershell"></a>使用 PowerShell 导入节点配置

可以使用 [Import-AzureRmAutomationDscNodeConfiguration](/powershell/module/azurerm.automation/import-azurermautomationdscnodeconfiguration) cmdlet 将节点配置导入自动化帐户。

```powershell
Import-AzureRmAutomationDscNodeConfiguration -AutomationAccountName "MyAutomationAccount" -ResourceGroupName "MyResourceGroup" -ConfigurationName "MyNodeConfiguration" -Path "C:\MyConfigurations\TestVM1.mof"
```



