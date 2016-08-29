<properties
	pageTitle="联合使用 Site Recovery 与 Azure 自动化 DSC，将 VMware 虚拟机复制到 Azure | Azure"
	description="介绍如何通过 Azure 自动化 DSC 将适用于虚拟机/物理机的 ASR 移动服务和 Azure 代理自动部署到 Azure。"
	services="site-recovery"
	documentationCenter=""
	authors="krnese"
	manager="lorenr"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.date="07/26/2016"
	wacn.date=""/>

# 联合使用 Site Recovery 与 Azure 自动化 DSC，将 VMware VM 复制到 Azure

在 OMS 中，我们为你提供了一个全面的备份和灾难恢复解决方案，方便你在业务连续性计划中使用。

我们一开始还提供了使用 Hyper-V 副本的 Hyper-V，但在进行扩展后可以支持异类设置，因为我们清楚地知道，客户的云中存在多种虚拟机监控程序和平台。

如果你目前运行的是 VMware 工作负荷和/或物理服务器，则我们所依赖的管理服务器可运行在你的环境中运行的所有 Site Recovery 组件，以便处理 Azure 的通信和数据复制（如果 Azure 是你的目标）。

## 使用 OMS 自动化 DSC 部署 ASR 移动服务
让我们开始对此管理服务器的真正功能进行快速的剖析：

管理服务器运行多个服务器角色，例如**配置** – 协调通信，管理数据复制和恢复过程。

另外，**进程**角色可充当复制网关，此服务器可在其中接收受保护源计算机提供的复制数据，通过缓存、压缩和加密对其进行优化，然后将数据发送到 Azure 存储帐户。进程角色的一大功能是，它还可以将移动服务的安装推送到受保护的计算机，并执行自动发现 VMware VM 的操作。

从 Azure 进行故障回复时，**主目标**角色将全权负责，并会处理此操作过程中的复制数据。

至于受保护的计算机，我们依赖于**移动服务** – 一个组件，可部署到每台需要复制到 Azure 的计算机（VMware VM 或物理服务器）。其角色是捕获计算机上的数据写入操作，并将这些操作转发到管理服务器（进程角色）。

处理业务连续性时，必须了解你的工作负荷、基础结构以及所涉及的组件，这样才能满足对 RTO 和 RPO 的要求。在这种情况下，移动服务的作用很重要，可以确保你的工作负荷受到预期的保护。

那么，我们如何通过优化的方式来确保在借助某些 OMS 组件的情况下，我们的受保护设置具有可靠的功能？

在本文中，我们将为你提供一个示例，介绍如何利用 OMS 自动化 DSC 与 OMS Site Recovery 来确保移动服务与 Azure VM 代理能够部署到需要保护的 Windows 计算机，并在 Azure 是复制目标时始终处于运行状态。

## 先决条件

- 一个存储库，用于存储所需设置
- 一个存储库，用于存储注册到管理服务器时所需的通行短语
- 在计算机上安装 Windows Management Framework 5.0，需要启用以获取保护（OMS 自动化 DSC 的要求）

如果你需要将 DSC 用于使用 WMF 4.0 的 Windows 计算机，请参阅“在断开连接的环境中使用 DSC”部分

（将会为每个管理服务器生成唯一通行短语。若要部署多个管理服务器，需确保将正确的通行短语存储在 passphrase.txt 文件中）

移动服务可以通过命令行安装，并可接受多个参数。

因此，在从设置中提取二进制文件后，我们需要保留这些文件，将其存储在能够使用 DSC 配置进行检索的某个位置。

## 步骤 1：提取二进制文件

1. 若要提取完成此设置所需的文件，请导航到管理服务器上的以下目录：**\\Microsoft Azure Site Recovery\\home\\svsystems\\pushinstallsvc\\repository**

    在此文件夹中，你应该会看到一个 MSI 文件，其名称为：

    **Microsoft-ASR\_UA\_<版本>_Windows\_GA_<日期>\_Release.exe**

    使用以下 cmdlet 来提取安装程序：

    .\\Microsoft-ASR\_UA\_9.1.0.0_Windows\_GA_02May2016\_release.exe /q /x:C:\\Users\\Administrator\\Desktop\\Mobility\_Service\\Extract

2. 选择所有文件，将其发送到压缩的文件夹 (zip)。

就这么简单！ 现在，你已经有了所需的二进制文件，可以使用 OMS 自动化 DSC 自动安装移动服务了。

### 通行短语
接下来，你需要确定在何处放置这个压缩的文件夹。我稍后会在我的示例中对此进行介绍，我使用 Azure 中的一个存储帐户，我还在其中放置了完成设置所需的通行短语，以便代理在此过程中注册到管理服务器。

部署管理服务器时获得的通行短语可以保存到 txt 文件 (passphrase.txt) 中。

我将压缩的文件夹和通行短语都放置在我的 Azure 存储帐户的专用容器中

![文件夹位置](./media/site-recovery-automate-mobilitysevice-install/folder-and-passphrase-location.png)

如果你想要将这些文件保存在网络的共享文件夹中，也完全没有问题。你只需确保我们随后会使用的 DSC 资源可以进行访问，并可获取设置和通行短语。

## 步骤 2 - 创建 DSC 配置
我的设置依赖于 WMF 5.0，也就是说，若要让计算机成功将配置应用到 OMS 自动化 DSC，WMF 5.0 必须存在。

我的环境中使用以下 DSC 配置：


	configuration ASRMobilityService {

	    $RemoteFile = 'https://knrecstor01.blob.core.chinacloudapi.cn/asr/ASR.zip'
	    $RemotePassphrase = 'https://knrecstor01.blob.core.chinacloudapi.cn/asr/passphrase.txt'
	    $RemoteAzureAgent = 'http://go.microsoft.com/fwlink/p/?LinkId=394789'
	    $LocalAzureAgent = 'C:\Temp\AzureVmAgent.msi'
	    $TempDestination = 'C:\Temp\asr.zip'
	    $LocalPassphrase = 'C:\Temp\Mobility_service\passphrase.txt'
	    $Role = 'Agent'
	    $Install = 'C:\Program Files (x86)\Microsoft Azure Site Recovery'
	    $CSEndpoint = '10.0.0.115'
	    $Arguments = '/Role ' + '"' + $Role + '"' + ' /InstallLocation ' + '"' + $Install + '"' + ' /CSEndpoint ' + '"' + $CSEndpoint + '"' + ' /PassphraseFilePath ' +  '"' +$LocalPassphrase + '"'  

	    Import-DscResource -ModuleName xPSDesiredStateConfiguration

	    node localhost {

	        xRemoteFile Setup {
	           URI = $RemoteFile
	           DestinationPath = $TempDestination
	           DependsOn = "[File]Directory"
	            }

	        xRemoteFile Passphrase {
	            URI = $RemotePassphrase
	            DestinationPath = $LocalPassphrase
	            DependsOn = "[File]Directory"
	            }

	        xRemoteFile AzureAgent {
	            URI = $RemoteAzureAgent
	            DestinationPath = $LocalAzureAgent
	            DependsOn = "[File]Directory"
	            }

	        File Directory {
	            DestinationPath = "C:\Temp\ASRSetup"
	            Type = "Directory"            
	            }

	        Archive ASRzip {
	           Path = $TempDestination
	           Destination = "C:\Temp\ASRSetup"
	           DependsOn = "[xRemotefile]Setup"
	           }

	        Package Install {
	            Path = "C:\temp\ASRSetup\ASR\UNIFIEDAGENT.EXE"
	            Ensure = "Present"
	            Name = "Microsoft Azure Site Recovery mobility Service/Master Target Server"
	            ProductId = "275197FC-14FD-4560-A5EB-38217F80CBD1"
	            Arguments = $Arguments
	            DependsOn = "[Archive]ASRzip"
	            }

	        Package AzureAgent {
	            Path = "C:\Temp\AzureVmAgent.msi"
	            Ensure = "Present"
	            Name = "Microsoft Azure VM Agent - 2.7.1198.735"
	            ProductId = "5CF4D04A-F16C-4892-9196-6025EA61F964"
	            Arguments = '/q /l "c:\temp\agentlog.txt'
	            DependsOn = "[Package]Install"
	            }

	        Service ASRvx {
	            Name = "svagents"
	            Ensure = "Present"
	            State = "Running"
	            DependsOn = "[Package]Install"
	            }

	        Service ASR {
	            Name = "InMage Scout Application Service"
	            Ensure = "Present"
	            State = "Running"
	            DependsOn = "[Package]Install"
	            }

	        Service AzureAgentService {
	            Name = "WindowsAzureGuestAgent"
	            Ensure = "Present"
	            State = "Running"
	            DependsOn = "[Package]AzureAgent"
	            }

	        Service AzureTelemetry {
	            Name = "WindowsAzureTelemetryService"
	            Ensure = "Present"
	            State = "Running"
	            DependsOn = "[Package]AzureAgent"
	            }
	    }
	}

配置将执行以下操作：

- 变量将告诉配置在何处获取移动服务和 Azure VM 代理的二进制文件和通行短语，以及在何处存储输出。
- 导入 xPSDesiredStateConfiguration DscResource，以便使用“xRemoteFile”下载存储库中的文件。
- 创建需存储二进制文件的目录。
- 存档资源将提取已压缩文件夹中的文件。
- 包的“安装”资源将使用特定参数安装 UNIFIEDAGENT.EXE 安装程序中的移动服务（需根据你的环境对构造参数的变量进行更改）。
- 包的“AzureAgent”资源将安装 Azure VM 代理，该代理可用于在 Azure 中运行的所有 VM，并可在故障转移后向 VM 添加扩展。
- 服务资源将确保相关的移动服务和 Azure 服务始终运行。

我已将配置作为 **ASRMobilityService** 保存在我计算机的文件夹中。

（请记住，需根据实际的管理服务器替换配置中的 CSIP，这样代理才会正确进行连接，才会使用正确的通行短语）。

## 步骤 3 – 上载到 OMS 自动化 DSC

由于我们创建的 DSC 配置会导入所需的 DSC 资源模块 (xPSDesiredStateConfiguration)，因此你需要先将该模块导入 OMS 自动化中，然后才能上载 DSC 配置。

登录到你的自动化帐户，导航到“资产”>“模块”，然后单击“浏览库”。

你可以在此处搜索模块，并将其导入到帐户中。

![导入模块](./media/site-recovery-automate-mobilitysevice-install/search-and-import-module.png)

完成此操作后，转到安装了 Azure RM 模块的计算机，然后导入新建的 DSC 配置。

### 导入 cmdlet

在 PowerShell 中登录到你的 Azure 订阅，根据你的环境修改 cmdlet。

首先需使用以下 cmdlet 将配置上载到 OMS 自动化 DSC：


	Import-AzureRmAutomationDscConfiguration `
	                                        -AutomationAccountName KNOMSAA `
	                                        -ResourceGroupName KNOMS `
	                                        -SourcePath C:\ASR\ASRMobilityService.ps1 `
	                                        -Published `
	                                        -Description "DSC Config for Mobility Service"


接下来需在 OMS 自动化 DSC 中编译配置，以便注册节点。

### 在 OMS 自动化 DSC 中编译配置

为此，需运行以下 cmdlet：


	Start-AzureRmAutomationDscCompilationJob `
	                                        -AutomationAccountName KNOMSAA `
	                                        -ResourceGroupName KNOMS `
	                                        -ConfigurationName ASRMobilityService


这可能需要数分钟的时间，因为我们基本上是将配置部署到托管的 DSC 拉取服务。

完成后，即可使用 PowerShell (Get-AzureRmAutomationDscCompilationJob) 或 portal.azure.cn 检索作业信息

![检索作业](./media/site-recovery-automate-mobilitysevice-install/retrieve-job.png)  


现在，我们已成功发布 DSC 配置并将其上载到 OMS 自动化 DSC。

## 步骤 4 – 将计算机加入 OMS 自动化 DSC
*完成此方案的一个先决条件是，你的 Windows 计算机已使用最新版 WMF 进行了更新。你可以按照以下 URL 下载和安装正确版本的平台：https://www.microsoft.com/download/details.aspx?id=50395*

现在将创建需应用到节点的 DSC 元配置。若要成功，你需要检索适用于 Azure 中所选自动化帐户的终结点 URL 和主密钥。

可以在自动化帐户的“所有设置”边栏选项卡的“密钥”下找到这些值。

![密钥值](./media/site-recovery-automate-mobilitysevice-install/key-values.png)  


在我的环境中，我有一个 Windows Server 2012 R2 物理服务器，需使用 OMS Site Recovery 对其进行保护。

开始将服务器与自动化 DSC 终结点进行关联之前，建议你查看注册表中是否有待完成的文件重命名操作，因为该操作会引发重新启动，从而妨碍设置完成。

### 查看注册表中是否有待完成的文件重命名操作

运行以下 cmdlet，确保服务器上没有待完成的重新启动操作：


	Get-ItemProperty "HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager" | select PendingFileRenameOperations

如果显示的结果为空，则可继续操作。否则，应在维护时段内重新启动服务器以解决此问题。

为了应用服务器上的配置，我需要启动 PowerShell ISE 并运行以下脚本。这实质上是一个 DSC 本地配置管理器配置，该配置将指示本地配置管理器引擎注册到 OMS 自动化 DSC 服务并检索特定的配置 (ASRMobilityService.localhost)


	[DSCLocalConfigurationManager()]

	Configuration metaconfig
	{
	    node localhost
	    {

	        Settings
	        {
	            RefreshFrequencyMins = '30'
	            RebootNodeIfNeeded = $true
	            RefreshMode = 'PULL'
	            ActionAfterReboot = 'ContinueConfiguration'
	            ConfigurationMode = 'ApplyAndMonitor'
	            AllowModuleOverwrite = $true
	        }
	                ResourceRepositoryWeb AzureAutomationDSC
	            {
	                ServerURL = 'https://we-agentservice-prod-1.azure-automation.net/accounts/4d54e439-8540-4c8f-8e0f-3d7afdda6746'
	                RegistrationKey = 'KaTg5zTtI/gf8OGiN28L9FT2gMbowzOv3P9rouQfhxcR+42Mac80QKxOFXYLMdKxRdz8QtJiXLb7noG1U4zJ6g=='
	            }
	                ConfigurationRepositoryWeb AzureAutomationDSC
	            {
	                ServerURL = 'https://we-agentservice-prod-1.azure-automation.net/accounts/4d54e439-8540-4c8f-8e0f-3d7afdda6746'
	                RegistrationKey = 'KaTg5zTtI/gf8OGiN28L9FT2gMbowzOv3P9rouQfhxcR+42Mac80QKxOFXYLMdKxRdz8QtJiXLb7noG1U4zJ6g=='
	                ConfigurationNames = 'ASRMobilityService.localhost'
	            }
	                ReportServerWeb AzureAutomationDSC
	            {
	                ServerURL = 'https://we-agentservice-prod-1.azure-automation.net/accounts/4d54e439-8540-4c8f-8e0f-3d7afdda6746'
	                RegistrationKey = 'KaTg5zTtI/gf8OGiN28L9FT2gMbowzOv3P9rouQfhxcR+42Mac80QKxOFXYLMdKxRdz8QtJiXLb7noG1U4zJ6g=='
	            }

	    }
	}
	metaconfig

	Set-DscLocalConfigurationManager .\metaconfig -Force -Verbose


此配置会将本地配置管理器配置为自行注册到 OMS 自动化 DSC，并会大致说明引擎应如何运行以及在存在配置偏差的情况下应如何操作 (ApplyAndAutoCorrect)，而如果需要重新启动，则继续完成之后的配置。

运行此脚本后，节点就会启动注册到自动化 DSC 的过程

![注册节点](./media/site-recovery-automate-mobilitysevice-install/register-node.png)

如果回到 portal.azure.cn，可以看到新注册的节点现已出现在门户中。

![注册节点](./media/site-recovery-automate-mobilitysevice-install/registered-node.png)

在服务器上，我们可以运行以下 PowerShell cmdlet 来验证节点是否已正确注册：


	Get-DscLocalConfigurationManager


拉取配置并将其应用到服务器以后，可运行以下命令对其进行验证：


	Get-DscConfigurationStatus


输出显示服务器已成功拉取其配置：

![注册节点](./media/site-recovery-automate-mobilitysevice-install/successful-config.png)

另外，移动服务安装程序自带的日志位于“<SystemDrive>\\ProgramData\\ASRSetupLogs”中。

就这样，我们现在成功地在需要使用 Site Recovery 进行保护的计算机上部署和注册了移动服务。我们可以依赖 DSC，因为所需的服务将始终处于运行状态。

![注册节点](./media/site-recovery-automate-mobilitysevice-install/successful-install.png)  


管理服务器检测到这种情况后，即可继续使用 Site Recovery 配置保护措施并允许在计算机上进行复制。

## 在断开连接的环境中使用 DSC

即使计算机未连接到 Internet，也可通过 DSC 在需要保护的工作负荷上部署和配置移动服务。

你可以在你的环境中实例化自己的 DSC 拉取服务器，其提供的功能与你从 OMS 自动化 DSC 中获取的功能基本上是一样的，即客户端在注册到 DSC 终结点之后就可以拉取配置。不过，我们还有其他选择，那就是使用推送，即手动将 DSC 配置推送到你的计算机，不管是本地计算机还是远程计算机。

请注意，在此示例中，我们添加了一个针对计算机名的参数。远程文件现在位于远程共享中，该共享应可通过需保护的计算机进行访问。而在脚本的最后，我们启用了该配置，然后开始将 DSC 配置应用到目标计算机。

### 先决条件

· 安装 xPSDesiredStateConfiguration PowerShell 模块

对于安装了 WMF 5.0 的 Windows 计算机，可在目标计算机上运行以下 cmdlet 来直接安装 xPSDesiredStateConfiguration 模块：


	Find-Module -Name xPSDesiredStateConfiguration | Install-Module


你还可以在装有 PowerShellGet (WMF 5.0) 的计算机上运行以下 cmdlet，以便在需要将模块分发到使用 WMF 4.0 的 Windows 计算机时下载和保存该模块：


	Save-Module -Name xPSDesiredStateConfiguration -Path <location>


另外，对于 WMF 4.0，请确保在计算机上安装以下更新：

https://www.microsoft.com/download/details.aspx?id=40749  


以下配置可以通过 WMF 5.0 和 4.0 推送到这两种 Windows 计算机


	configuration ASRMobilityService {

	    param (
	    [Parameter(Mandatory=$true)]
	    $computername
	    )

	    $RemoteFile = '\\myfileserver\share\asr.zip'
	    $RemotePassphrase = '\\myfileserver\share\passphrase.txt'
	    $RemoteAzureAgent = '\\myfileserver\share\AzureVmAgent.msi'
	    $LocalAzureAgent = 'C:\Temp\AzureVmAgent.msi'
	    $TempDestination = 'C:\Temp\asr.zip'
	    $LocalPassphrase = "C:\Temp\Mobility_service\passphrase.txt"
	    $Role = 'Agent'
	    $Install = 'C:\Program Files (x86)\Microsoft Azure Site Recovery'
	    $CSEndpoint = '10.0.0.115'
	    $Arguments = '/Role ' + '"' + $Role + '"' + ' /InstallLocation ' + '"' + $Install + '"' + ' /CSEndpoint ' + '"' + $CSEndpoint + '"' + ' /PassphraseFilePath ' +  '"' +$LocalPassphrase + '"'  

	    Import-DscResource -ModuleName xPSDesiredStateConfiguration

	    node $computername {      

	        xRemoteFile Setup {
	           URI = $RemoteFile
	           DestinationPath = $TempDestination
	           DependsOn = "[File]Directory"
	            }

	        xRemoteFile Passphrase {
	            URI = $RemotePassphrase
	            DestinationPath = $LocalPassphrase
	            DependsOn = "[File]Directory"
	            }

	        xRemoteFile AzureAgent {
	            URI = $RemoteAzureAgent
	            DestinationPath = $LocalAzureAgent
	            DependsOn = "[File]Directory"
	            }

	        File Directory {
	            DestinationPath = "C:\Temp\ASRSetup"
	            Type = "Directory"            
	            }

	        Archive ASRzip {
	           Path = $TempDestination
	           Destination = "C:\Temp\ASRSetup"
	           DependsOn = "[xRemotefile]Setup"
	           }

	        Package Install {
	            Path = "C:\temp\ASRSetup\ASR\UNIFIEDAGENT.EXE"
	            Ensure = "Present"
	            Name = "Microsoft Azure Site Recovery mobility Service/Master Target Server"
	            ProductId = "275197FC-14FD-4560-A5EB-38217F80CBD1"
	            Arguments = $Arguments
	            DependsOn = "[Archive]ASRzip"
	            }

	        Package AzureAgent {
	            Path = "C:\Temp\AzureVmAgent.msi"
	            Ensure = "Present"
	            Name = "Microsoft Azure VM Agent - 2.7.1198.735"
	            ProductId = "5CF4D04A-F16C-4892-9196-6025EA61F964"
	            Arguments = '/q /l "c:\temp\agentlog.txt'
	            DependsOn = "[Package]Install"
	            }

	        Service ASRvx {
	            Name = "svagents"
	            State = "Running"
	            DependsOn = "[Package]Install"
	            }

	        Service ASR {
	            Name = "InMage Scout Application Service"
	            State = "Running"
	            DependsOn = "[Package]Install"
	            }

	        Service AzureAgentService {
	            Name = "WindowsAzureGuestAgent"
	            State = "Running"
	            DependsOn = "[Package]AzureAgent"
	            }

	        Service AzureTelemetry {
	            Name = "WindowsAzureTelemetryService"
	            State = "Running"
	            DependsOn = "[Package]AzureAgent"
	            }
	    }
	}
	ASRMobilityService

	Start-DscConfiguration -ComputerName $computername .\ASRMobilityService -wait -force -Verbose


若要在公司网络中实例化自己的 DSC 拉取服务器来模拟从 OMS 自动化 DSC 获取的相同功能，请参阅以下指南：https://msdn.microsoft.com/zh-cn/powershell/dsc/pullserver?f=255&MSPPError=-2147217396

## 可选：使用 Azure Resource Manager 模板部署 DSC 配置

在本文中，我们目前关注的是如何通过创建自己的 DSC 配置来自动部署移动服务和 Azure VM 代理，以及如何确保它们在你要保护的计算机上处于运行状态。另外，我们还额外介绍了如何使用 Azure Resource Manager## ## 模板将此 DSC 配置部署到新的或现有的 Azure 自动化帐户，以便通过模板中的输入参数创建将包含环境变量的自动化资产。

部署完成以后，若要加入你的计算机，可直接参阅本指南中的步骤 4。

该模板将执行以下操作：

· 使用现有的 OMS 自动化帐户或新建一个

· 获取以下项的输入参数：

	· ASRRemoteFile – the location where you have stored the Mobility Service setup
	· ASRPassphrase – the location where you have stored the passphrase.txt file
	· ASRCSEndpoint – the IP address of your Management server
	· Import xPSDesiredStateConfiguration PowerShell module
	· Create and compile the DSC configuration


所有上述步骤均需按正确顺序操作，这样即可轻松加入要保护的计算机。

附带部署说明的模板位于：

https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/DSC

使用 PowerShell 进行部署：


	$OMSAutomationRGname = 'KNOMS'

	$DSCJobGuid = (New-Guid).Guid

	New-AzureRmResourceGroupDeployment `
	                                  -Name "DSC3" `
	                                  -ResourceGroupName $OMSAutomationRGname `
	                                  -TemplateFile https://raw.githubusercontent.com/krnese/AzureDeploy/master/OMS/MSOMS/DSC/azuredeploy.json `
	                                  -OMSAutomationAccountName KNOMSAA `
	                                  -ASRRemoteFile "https://knrecstor01.blob.core.chinacloudapi.cn/asr/ASR.zip" `
	                                  -ASRRemotePassphrase "https://knrecstor01.blob.core.chinacloudapi.cn/asr/passphrase.txt" `
	                                  -ASRCSEndpoint '10.0.0.115' `
	                                  -DSCJobGuid $DSCJobGuid `
	                                  -Verbose


## 后续步骤：

部署移动服务代理以后，即可继续为虚拟机[启用复制](/documentation/articles/site-recovery-vmware-to-azure/#step-6-replicate-applications)。

<!---HONumber=Mooncake_0822_2016-->