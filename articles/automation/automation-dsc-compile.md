<properties 
   pageTitle="在 Azure 自动化 DSC 中编译配置 | Microsoft Azure" 
   description="概述编译所需状态配置 (DSC) 的两种方法：使用 Azure 门户，或者使用 Windows PowerShell。" 
   services="automation" 
   documentationCenter="na" 
   authors="coreyp-at-msft" 
   manager="stevenka" 
   editor="tysonn"/>

<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="powershell"
   ms.workload="na" 
   ms.date="01/25/2016"
   ms.author="coreyp"/>
   
#在 Azure 自动化 DSC 中编译配置#

使用 Azure 自动化时可通过两种方法编译所需状态配置 (DSC)：使用 Azure 门户，或者使用 Windows PowerShell。下表将帮助你根据每种方法的特征确定何时应使用哪种方法：

###Azure 预览门户###
- 使用交互式用户界面的最简单方法
- 用于提供简单参数值的窗体
- 轻松跟踪作业状态
- 使用 Azure 登录对访问进行身份验证

###Windows PowerShell###
- 使用 Windows PowerShell cmdlet 从命令行调用
- 可以使用多个步骤包含在自动化解决方案中
- 提供简单和复杂的参数值
- 跟踪作业状态
- 支持 PowerShell cmdlet 所需的客户端
- 传递 ConfigurationData
- 编译使用凭据的配置

在确定编译方法后，可以遵循以下相应过程开始编译。

##使用 Azure 预览门户编译 DSC 配置##

1.  从自动化帐户中，单击“配置”。
2.  单击某个配置以打开其边栏选项卡。
3.  单击“编译”。
4.  如果该配置没有参数，系统将提示你确认是否要进行编译。如果该配置有参数，则会打开“编译配置”边栏选项卡让你提供参数值。有关参数的详细信息，请参阅以下<a href="#basic-parameters">基本参数</a>部分。
5.  “编译作业”边栏选项卡随即打开，让你跟踪编译作业的状态，以及由于此作业而放在 Azure 自动化 DSC“拉”服务器上的节点配置（MOF 配置文档）。

##使用 Windows PowerShell 编译 DSC 配置##

可以在 Windows PowerShell 中使用 [`Start-AzureRmAutomationDscCompilationJob`](https://msdn.microsoft.com/library/mt244118.aspx) 开始编译。以下示例代码启动 DSC 配置 **SampleConfig** 的编译。

    Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "SampleConfig" 
 
`Start-AzureRmAutomationDscCompilationJob` 返回可用于跟踪作业状态的编译作业对象。接下来，你可以使用此编译作业对象与 [`Get-AzureRmAutomationDscCompilationJob`](https://msdn.microsoft.com/library/mt244120.aspx) 确定编译作业的状态，并使用 [`Get-AzureRmAutomationDscCompilationJobOutput`](https://msdn.microsoft.com/library/mt244103.aspx) 查看其流（输出）。以下示例代码启动 **SampleConfig** 配置的编译，并在编译完成后显示其流。
    
    $CompilationJob = Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "SampleConfig"
    
    while($CompilationJob.EndTime –eq $null -and $CompilationJob.Exception –eq $null)       	
    {
    	$CompilationJob = $CompilationJob | Get-AzureRmAutomationDscCompilationJob
    	Start-Sleep -Seconds 3
    }
    
    $CompilationJob | Get-AzureRmAutomationDscCompilationJobOutput –Stream Any 


##基本参数##

DSC 配置中的参数声明（包括参数类型和属性）的工作方式与 Azure 自动化 Runbook 中相同。若要了解有关 Runbook 参数的详细信息，请参阅 [Starting a runbook in Azure Automation](automation-starting-a-runbook.md)（在 Azure 自动化中启动 Runbook）。

以下示例使用名为 **FeatureName** 和 **IsPresent** 的两个参数来确定在编译期间生成的 **ParametersExample.sample** 节点配置中的属性值。

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

你可以在 Azure 自动化 DSC 门户或 Azure PowerShell 中编译使用基本参数的 DSC 配置：

###门户###

在门户中，可以在单击“编译”后输入参数值。

![替换文字](./media/automation-dsc-compile/DSC_compiling_1.png)

###PowerShell###

PowerShell 要求将参数放入[哈希表](http://technet.microsoft.com/library/hh847780.aspx)中，其中的键必须与参数名称匹配，值等于参数值。

    $Parameters = @{
    		"FeatureName" = "Web-Server"
    		"IsPresent" = $False
    }
    
    
    Start-AzureRMAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "ParametersExample" -Parameters $Parameters 
    

有关如何将 PSCredentials 作为参数传入的信息，请参阅下面的<a href="#credential-assets">凭据资产</a>。

##ConfigurationData##

**ConfigurationData** 可让你在使用 PowerShell DSC 时区分结构化配置与任何环境特定配置。有关 **ConfigurationData** 的详细信息，请参阅 [Separating "What" from "Where" in PowerShell DSC](http://blogs.msdn.com/b/powershell/archive/2014/01/09/continuous-deployment-using-dsc-with-minimal-change.aspx)（区分 PowerShell DSC 中的“What”与“Where”）。

>[AZURE.NOTE] 使用 Azure PowerShell 在 Azure 自动化 DSC 中进行编译时可以使用 **ConfigurationData**，但使用 Azure 门户时则不可以。

以下示例 DSC 配置通过 **$ConfigurationData** 和 **$AllNodes** 关键字来使用 **ConfigurationData**。在本示例中还需要 [**xWebAdministration** 模块](https://www.powershellgallery.com/packages/xWebAdministration/)：

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

可以使用 PowerShell 编译上述 DSC 配置。PowerShell 将以下两个节点配置添加到 Azure 自动化 DSC“拉”服务器：**ConfigurationDataSample.MyVM1** 和 **ConfigurationDataSample.MyVM3**：

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


##资产##

Azure 自动化 DSC 配置和 Runbook 中的资产引用是相同的。有关详细信息，请参阅以下主题：

- [证书](automation-certificates.md)
- [连接](automation-connections.md)
- [凭据](automation-credentials.md)
- [变量](automation-variables.md)

###凭据资产###
尽管 Azure 自动化中的 DSC 配置可以使用 **Get-AutomationPSCredential** 引用凭据资产，但如有需要，也可以通过参数传入凭据资产。如果配置采用属于 **PSCredential** 类型的参数，则需要将 Azure 自动化凭据资产的字符串名称传递为该参数的值，而不是 PSCredential 对象。具有该名称的 Azure 自动化凭据资产在后台检索并传递到配置。

要在节点配置（MOF 配置文档）中保持凭据的安全，需要在节点配置 MOF 文件中为凭据加密。Azure 自动化进一步执行此步骤并加密整个 MOF 文件。不过，目前必须告知 PowerShell DSC 在节点配置 MOF 生成期间以纯文本形式输出凭据是可行的，因为 PowerShell DSC 并不知道在通过编译作业生成 MOF 文件之后 Azure 自动化将加密整个文件。

你可以告知 PowerShell DSC，使用 <a href="#configurationdata">ConfigurationData</a> 在生成的节点配置 MOF 中以纯文本形式输出凭据是可行的。应针对每个出现在 DSC 配置中且使用凭据的节点块名称，通过 **ConfigurationData** 传递 `PSDscAllowPlainTextPassword = $true`。

以下示例演示使用自动化凭据资产的 DSC 配置。

    Configuration CredentialSample
    {
       $Cred = Get-AutomationPSCredential -Name "SomeCredentialAsset"
    
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

可以使用 PowerShell 编译上述 DSC 配置。PowerShell 将以下两个节点配置添加到 Azure 自动化 DSC“拉”服务器：**CredentialSample.MyVM1** 和 **CredentialSample.MyVM2**。


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

<!---HONumber=Mooncake_0411_2016-->