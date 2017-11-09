---
title: "使用 Azure Automation DSC 部署 Site Recovery 移动服务 | Microsoft 文档"
description: "介绍如何使用 Azure Automation DSC 将适用于 VMware VM 和物理机服务器复制的 Azure Site Recovery 移动服务与 Azure 代理自动部署到 Azure"
services: site-recovery
documentationcenter: 
author: krnese
manager: lorenr
editor: 
ms.assetid: 1f8cd3ac-0522-48eb-a5f0-679ee9192ddb
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: krnese
ms.openlocfilehash: bcc5f11afbecac8fe63935f3401dd3e2d767e8aa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-the-mobility-service-with-azure-automation-dsc-for-replication-of-vm"></a>使用 Azure Automation DSC 部署移动服务以复制 VM
Operations Management Suite 提供了全面的备份和灾难恢复解决方案，可方便用户在业务连续性计划中使用。

我们可以通过使用 Hyper-V 副本，先从 Hyper-V 开启这段学习旅程。 但我们在支持异类安装方面已经进行了扩展，因为客户在自己的云中拥有多个虚拟机监控程序和平台。

如果目前运行的是 VMware 工作负荷和/或物理服务器，当 Azure 是目标时，管理服务器将运行环境中的所有 Azure Site Recovery 组件来处理与 Azure 之间的通信和数据复制。

## <a name="deploy-the-site-recovery-mobility-service-by-using-automation-dsc"></a>使用 Automation DSC 部署 Site Recovery 移动服务。
下面让我们先对此管理服务器的功能进行一个快速的剖析。

管理服务器运行着多个服务器角色。 其中的一个角色就是*配置*，用于协调通信、管理数据复制和恢复进程。

此外，*进程*角色充当复制网关。 此角色从受保护的源计算机接收复制数据，通过缓存、压缩和加密对数据进行优化，并将数据发送到 Azure 存储帐户。 进程角色的一大功能是，它还可以将移动服务的安装推送到受保护的计算机，并执行自动发现 VMware VM 的操作。

如果有来自 Azure 的故障回复，则*主目标*角色会处理此操作过程中的复制数据。

对于受保护的计算机，我们将依赖于*移动服务*。 此组件部署在要复制到 Azure 的每个计算机（VMware VM 或物理服务器）上。 它捕获计算机上的数据写入操作，并将这些操作转发到管理服务器（进程角色）。

当处理业务连续性时，必须了解工作负荷、基础结构以及所涉及的组件。 这样才能满足对恢复时间目标 (RTO) 和 恢复点目标 (RPO) 的要求。 在这种情况下，移动服务就成为确保工作负荷受到预期保护的关键。

那么，在借助某些 Operations Management Suite 组件的情况下，如何能够通过优化的方式确保获得可靠且受保护的设置呢？

本文提供了一些示例，说明如何将 Azure 自动化所需状态配置 (DSC) 与 Site Recovery 联合使用，以确保：

* 将移动服务和 Azure VM 代理部署到想要保护的 Windows 计算机。
* 当 Azure 是复制目标时，将始终运行移动服务和 Azure VM 代理。

## <a name="prerequisites"></a>先决条件
* 一个存储库，用于存储所需设置
* 一个存储库，用于存储注册到管理服务器时所需的通行短语

  > [!NOTE]
  > 会为每个管理服务器生成一个唯一的通行短语。 要部署多个管理服务器，需确保将正确的通行短语存储在 passphrase.txt 文件中。
  >
  >
* 在计算机上安装 Windows Management Framework (WMF) 5.0，需要启用以获取保护（Automation DSC 的要求）

  > [!NOTE]
  > 如需将 DSC 用于安装了 WMF 4.0 的 Windows 计算机上，请参阅[在断开连接的环境中使用 DSC](## Use DSC in disconnected environments) 部分。
  

移动服务可以通过命令行安装，并可接受多个参数。 因此，在从安装中提取二进制文件后，需要保留这些文件，并将其存储在能够使用 DSC 配置进行检索的某个位置。

## <a name="step-1-extract-binaries"></a>步骤 1：提取二进制文件
1. 若要提取进行此安装所需的文件，请在管理服务器上浏览到以下目录：

    **\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository**

    在此文件夹中，应会看到一个 MSI 文件，其名称为：

    **Microsoft-ASR_UA_version_Windows_GA_date_Release.exe**

    使用以下命令来提取安装程序：

    **.\Microsoft-ASR_UA_9.1.0.0_Windows_GA_02May2016_release.exe /q /x:C:\Users\Administrator\Desktop\Mobility_Service\Extract**
2. 选择所有文件，将其发送到压缩的文件夹 (zip)。

现在，已拥有了所需的二进制文件，可以使用 Automation DSC 自动安装移动服务。

### <a name="passphrase"></a>通行短语
接下来，需要确定在何处放置这个压缩的文件夹。 可以使用如下所示的 Azure 存储帐户，存储进行安装所需的通行短语。 然后，作为进程一部分，代理向管理服务器注册。

部署管理服务器时获得的通行短语可以保存到 txt 文件 (passphrase.txt) 中。

将压缩的文件夹和通行短语都放置在 Azure 存储帐户的专用容器中。

![文件夹位置](./media/site-recovery-automate-mobilitysevice-install/folder-and-passphrase-location.png)

如果想要将这些文件保存在网络的共享文件夹中，也完全没有问题。 只需确保随后会使用的 DSC 资源可以进行访问，并可获取安装程序和通行短语。

## <a name="step-2-create-the-dsc-configuration"></a>步骤 2：创建 DSC 配置
安装程序依赖于 WMF 5.0。 要让计算机成功将配置应用到 Automation DSC，则必须有 WMF 5.0。

该环境使用以下示例 DSC 配置：

```powershell
configuration ASRMobilityService {

    $RemoteFile = 'https://knrecstor01.blob.core.windows.net/asr/ASR.zip'
    $RemotePassphrase = 'https://knrecstor01.blob.core.windows.net/asr/passphrase.txt'
    $RemoteAzureAgent = 'http://go.microsoft.com/fwlink/p/?LinkId=394789'
    $LocalAzureAgent = 'C:\Temp\AzureVmAgent.msi'
    $TempDestination = 'C:\Temp\asr.zip'
    $LocalPassphrase = 'C:\Temp\Mobility_service\passphrase.txt'
    $Role = 'Agent'
    $Install = 'C:\Program Files (x86)\Microsoft Azure Site Recovery'
    $CSEndpoint = '10.0.0.115'
    $Arguments = '/Role "{0}" /InstallLocation "{1}" /CSEndpoint "{2}" /PassphraseFilePath "{3}"' -f $Role,$Install,$CSEndpoint,$LocalPassphrase

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    node localhost {

        File Directory {
            DestinationPath = 'C:\Temp\ASRSetup\'
            Type = 'Directory'            
        }

        xRemoteFile Setup {
            URI = $RemoteFile
            DestinationPath = $TempDestination
            DependsOn = '[File]Directory'
        }

        xRemoteFile Passphrase {
            URI = $RemotePassphrase
            DestinationPath = $LocalPassphrase
            DependsOn = '[File]Directory'
        }

        xRemoteFile AzureAgent {
            URI = $RemoteAzureAgent
            DestinationPath = $LocalAzureAgent
            DependsOn = '[File]Directory'
        }

        Archive ASRzip {
            Path = $TempDestination
            Destination = 'C:\Temp\ASRSetup'
            DependsOn = '[xRemotefile]Setup'
        }

        Package Install {
            Path = 'C:\temp\ASRSetup\ASR\UNIFIEDAGENT.EXE'
            Ensure = 'Present'
            Name = 'Microsoft Azure Site Recovery mobility Service/Master Target Server'
            ProductId = '275197FC-14FD-4560-A5EB-38217F80CBD1'
            Arguments = $Arguments
            DependsOn = '[Archive]ASRzip'
        }

        Package AzureAgent {
            Path = 'C:\Temp\AzureVmAgent.msi'
            Ensure = 'Present'
            Name = 'Windows Azure VM Agent - 2.7.1198.735'
            ProductId = '5CF4D04A-F16C-4892-9196-6025EA61F964'
            Arguments = '/q /l "c:\temp\agentlog.txt'
            DependsOn = '[Package]Install'
        }

        Service ASRvx {
            Name = 'svagents'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service ASR {
            Name = 'InMage Scout Application Service'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service AzureAgentService {
            Name = 'WindowsAzureGuestAgent'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }

        Service AzureTelemetry {
            Name = 'WindowsAzureTelemetryService'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }
    }
}
```
配置将执行以下操作：

* 变量将告诉配置会在何处获取移动服务和 Azure VM 代理的二进制文件、在何处获取通行短语，以及在何处存储输出。
* 配置将导入 xPSDesiredStateConfiguration DSC 资源，以便使用 `xRemoteFile` 下载存储库中的文件。
* 配置将创建需存储二进制文件的目录。
* 存档资源将从已压缩文件夹中提取文件。
* 包安装资源将使用特定参数安装 UNIFIEDAGENT.EXE 安装程序的移动服务。 （需要根据实际环境对构造参数的变量进行更改。）
* 包 AzureAgent 资源将安装 Azure VM 代理（推荐在 Azure 中运行的每个 VM 上安装此代理）。 Azure VM 代理还可以在故障转移后向 VM 添加扩展。
* 服务资源或资源将确保相关的移动服务和 Azure 服务始终运行。

将配置保存为 **ASRMobilityService**。

> [!NOTE]
> 请记住，需根据实际的管理服务器替换配置中的 CSIP，这样代理才会正确进行连接并使用正确的通行短语。
>
>

## <a name="step-3-upload-to-automation-dsc"></a>步骤 3：上传到 Automation DSC
由于所进行的 DSC 配置会导入所需的 DSC 资源模块 (xPSDesiredStateConfiguration)，因此，需要先将该模块导入 Automation 中，然后才能上传 DSC 配置。

登录到 Automation 帐户，浏览到“**资产**” > “**模块**”，然后单击“**浏览库**”。

可以在此处搜索模块，并将其导入到帐户中。

![导入模块](./media/site-recovery-automate-mobilitysevice-install/search-and-import-module.png)

完成此操作后，转到安装了 Azure Resource Manager 模块的计算机，并导入新建的 DSC 配置。

### <a name="import-cmdlets"></a>导入 cmdlet
在 PowerShell 中，登录到 Azure 订阅。 修改 Cmdlet 以反映环境，并捕获变量中的 Automation 帐户信息：

```powershell
$AAAccount = Get-AzureRmAutomationAccount -ResourceGroupName 'KNOMS' -Name 'KNOMSAA'
```

使用以下 cmdlet 将配置上传到 Automation DSC：

```powershell
$ImportArgs = @{
    SourcePath = 'C:\ASR\ASRMobilityService.ps1'
    Published = $true
    Description = 'DSC Config for Mobility Service'
}
$AAAccount | Import-AzureRmAutomationDscConfiguration @ImportArgs
```

### <a name="compile-the-configuration-in-automation-dsc"></a>在 Automation DSC 中编译配置
接下来，需在 Automation DSC 中编译配置，以便注册节点。 为此，需运行以下 cmdlet：

```powershell
$AAAccount | Start-AzureRmAutomationDscCompilationJob -ConfigurationName ASRMobilityService
```

这可能需要数分钟的时间，因为基本上是将配置部署到托管的 DSC 拉取服务。

编译配置完成后，即可使用 PowerShell (Get-AzureRmAutomationDscCompilationJob) 或 [Azure 门户](https://portal.azure.com/)来检索作业信息。

![检索作业](./media/site-recovery-automate-mobilitysevice-install/retrieve-job.png)

现在，已成功发布 DSC 配置并将其上传到 Automation DSC。

## <a name="step-4-onboard-machines-to-automation-dsc"></a>步骤 4 ：将计算机加入 Automation DSC
> [!NOTE]
> 完成此方案的一个先决条件是，Windows 计算机已使用最新版的 WMF 进行了更新。 可以从[下载中心](https://www.microsoft.com/download/details.aspx?id=50395)下载和安装正确版本的平台。
>
>

现在将创建需应用到节点的 DSC 元配置。 要成功，需要检索适用于 Azure 中所选自动化帐户的终结点 URL 和主密钥。 可以在 Automation 帐户的“**所有设置**”边栏选项卡上的“**密钥**”下找到这些值。

![密钥值](./media/site-recovery-automate-mobilitysevice-install/key-values.png)

在该示例中，有一个 Windows Server 2012 R2 物理服务器，需使用 Site Recovery 对其进行保护。

### <a name="check-for-any-pending-file-rename-operations-in-the-registry"></a>查看注册表中是否有待完成的文件重命名操作
在开始将服务器与 Automation DSC 终结点进行关联前，我们建议检查在注册表中是否有待完成的文件重命名操作。 由于等待重新启动，它们可能会禁止安装的完成。

运行以下 cmdlet，确保服务器上没有待完成的重新启动操作：

```powershell
Get-ItemProperty 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\' | Select-Object -Property PendingFileRenameOperations
```
如果显示的结果为空，则可继续操作。 否则，应在维护时段内重新启动服务器以解决此问题。

若要在服务器上应用配置，请启动 PowerShell 集成脚本环境 (ISE) 并运行以下脚本。 这实质上是一个 DSC 本地配置，将指示本地配置管理器引擎注册到 Automation DSC 服务并检索特定的配置 (ASRMobilityService.localhost)。

```powershell
[DSCLocalConfigurationManager()]
configuration metaconfig {
    param (
        $URL,
        $Key
    )
    node localhost {
        Settings {
            RefreshFrequencyMins = '30'
            RebootNodeIfNeeded = $true
            RefreshMode = 'PULL'
            ActionAfterReboot = 'ContinueConfiguration'
            ConfigurationMode = 'ApplyAndMonitor'
            AllowModuleOverwrite = $true
        }

        ResourceRepositoryWeb AzureAutomationDSC {
            ServerURL = $URL
            RegistrationKey = $Key
        }

        ConfigurationRepositoryWeb AzureAutomationDSC {
            ServerURL = $URL
            RegistrationKey = $Key
            ConfigurationNames = 'ASRMobilityService.localhost'
        }

        ReportServerWeb AzureAutomationDSC {
            ServerURL = $URL
            RegistrationKey = $Key
        }
    }
}
metaconfig -URL 'https://we-agentservice-prod-1.azure-automation.net/accounts/<YOURAAAccountID>' -Key '<YOURAAAccountKey>'

Set-DscLocalConfigurationManager .\metaconfig -Force -Verbose
```

此配置将使本地配置管理器引擎自行注册到 Automation DSC。 它还将确定引擎应如何运行、如果存在配置漂移 (ApplyAndAutoCorrect) 应执行什么操作，以及如果需要重新启动应如何继续进行配置。

运行此脚本后，节点就会启动注册到 Automation DSC 的过程。

![节点注册进行中](./media/site-recovery-automate-mobilitysevice-install/register-node.png)

如果返回到 Azure 门户，就可以看到新注册的节点已出现在门户中。

![在门户中注册的节点](./media/site-recovery-automate-mobilitysevice-install/registered-node.png)

在服务器上，可以运行以下 PowerShell cmdlet 以验证节点是否已正确注册：

```powershell
Get-DscLocalConfigurationManager
```

拉取配置并将其应用到服务器后，可以通过运行以下 cmdlet 对此进行验证：

```powershell
Get-DscConfigurationStatus
```

输出显示服务器已成功拉取其配置：

![输出](./media/site-recovery-automate-mobilitysevice-install/successful-config.png)

此外，移动服务安装程序自带的日志位于 *SystemDrive*\ProgramData\ASRSetupLogs 中。

就这么简单。 现在，已使用 Site Recovery 在想要保护的计算机上成功部署和注册了移动服务。 DSC 将确保所需的服务始终运行。

![成功部署](./media/site-recovery-automate-mobilitysevice-install/successful-install.png)

管理服务器检测到成功部署后，即可使用 Site Recovery 配置保护措施并允许在计算机上进行复制。

## <a name="use-dsc-in-disconnected-environments"></a>在断开连接的环境中使用 DSC
即使计算机未连接到 Internet，仍可以通过 DSC 在需要保护的工作负荷上部署和配置移动服务。

可以在环境中实例化自己的 DSC 拉取服务器，以从本质上提供从 Automation DSC 获取的相同功能。 即，客户端将请求到 DSC 终结点的配置（在其注册后）。 但是，也可以手动将 DSC 配置推送到本地或远程计算机。

请注意，在此示例中，在计算机名称中有一个添加的参数。 远程文件现在位于远程共享上（可由想要保护的计算机访问）。 脚本的末尾启用配置，并开始将 DSC 配置应用于目标计算机。

### <a name="prerequisites"></a>先决条件
请确保已安装 xPSDesiredStateConfiguration PowerShell 模块。 对于安装了 WMF 5.0 的 Windows 计算机，可以在目标计算机上通过运行以下 cmdlet 来安装 xPSDesiredStateConfiguration 模块：

```powershell
Find-Module -Name xPSDesiredStateConfiguration | Install-Module
```

还可以下载并保存模块，以便在需要时将其分发到安装了 WMF 4.0 的 Windows 计算机。 在安装了 PowerShellGet (WMF 5.0) 的计算机上运行此 cmdlet：

```powershell
Save-Module -Name xPSDesiredStateConfiguration -Path <location>
```

此外，对于 WMF 4.0，请确保已在计算机上安装了 [Windows 8.1 更新程序 KB2883200](https://www.microsoft.com/download/details.aspx?id=40749)。

可将以下配置推送到安装了 WMF 5.0 和 WMF 4.0 的 Windows 计算机：

```powershell
configuration ASRMobilityService {
    param (
        [Parameter(Mandatory=$true)]
        [ValidateNotNullOrEmpty()]
        [System.String] $ComputerName
    )

    $RemoteFile = '\\myfileserver\share\asr.zip'
    $RemotePassphrase = '\\myfileserver\share\passphrase.txt'
    $RemoteAzureAgent = '\\myfileserver\share\AzureVmAgent.msi'
    $LocalAzureAgent = 'C:\Temp\AzureVmAgent.msi'
    $TempDestination = 'C:\Temp\asr.zip'
    $LocalPassphrase = 'C:\Temp\Mobility_service\passphrase.txt'
    $Role = 'Agent'
    $Install = 'C:\Program Files (x86)\Microsoft Azure Site Recovery'
    $CSEndpoint = '10.0.0.115'
    $Arguments = '/Role "{0}" /InstallLocation "{1}" /CSEndpoint "{2}" /PassphraseFilePath "{3}"' -f $Role,$Install,$CSEndpoint,$LocalPassphrase

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    node $ComputerName {      
        File Directory {
            DestinationPath = 'C:\Temp\ASRSetup\'
            Type = 'Directory'            
        }

        xRemoteFile Setup {
            URI = $RemoteFile
            DestinationPath = $TempDestination
            DependsOn = '[File]Directory'
        }

        xRemoteFile Passphrase {
            URI = $RemotePassphrase
            DestinationPath = $LocalPassphrase
            DependsOn = '[File]Directory'
        }

        xRemoteFile AzureAgent {
            URI = $RemoteAzureAgent
            DestinationPath = $LocalAzureAgent
            DependsOn = '[File]Directory'
        }

        Archive ASRzip {
            Path = $TempDestination
            Destination = 'C:\Temp\ASRSetup'
            DependsOn = '[xRemotefile]Setup'
        }

        Package Install {
            Path = 'C:\temp\ASRSetup\ASR\UNIFIEDAGENT.EXE'
            Ensure = 'Present'
            Name = 'Microsoft Azure Site Recovery mobility Service/Master Target Server'
            ProductId = '275197FC-14FD-4560-A5EB-38217F80CBD1'
            Arguments = $Arguments
            DependsOn = '[Archive]ASRzip'
        }

        Package AzureAgent {
            Path = 'C:\Temp\AzureVmAgent.msi'
            Ensure = 'Present'
            Name = 'Windows Azure VM Agent - 2.7.1198.735'
            ProductId = '5CF4D04A-F16C-4892-9196-6025EA61F964'
            Arguments = '/q /l "c:\temp\agentlog.txt'
            DependsOn = '[Package]Install'
        }

        Service ASRvx {
            Name = 'svagents'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service ASR {
            Name = 'InMage Scout Application Service'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service AzureAgentService {
            Name = 'WindowsAzureGuestAgent'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }

        Service AzureTelemetry {
            Name = 'WindowsAzureTelemetryService'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }
    }
}
ASRMobilityService -ComputerName 'MyTargetComputerName'

Start-DscConfiguration .\ASRMobilityService -Wait -Force -Verbose
```

若要在公司网络中实例化自己的 DSC 拉取服务器，以模拟可从 Automation DSC 获取的功能，请参阅[设置 DSC Web 拉取服务器](https://msdn.microsoft.com/powershell/dsc/pullserver?f=255&MSPPError=-2147217396)。

## <a name="optional-deploy-a-dsc-configuration-by-using-an-azure-resource-manager-template"></a>可选：使用 Azure Resource Manager 模板部署 DSC 配置
本文目前重点介绍的是，如何通过创建自己的 DSC 配置来自动部署移动服务和 Azure VM 代理，以及如何确保它们在要保护的计算机上始终处于运行状态。 我们还有将此 DSC 配置部署到新的或现有 Azure 自动化帐户的 Azure Resource Manager 模板。 该模板将使用输入参数来创建包含环境变量的 Automation 资产。

部署模板后，要加入计算机，可直接参阅本指南中的步骤 4。

该模板将执行以下操作：

1. 使用现有 Automation 帐户或创建一个新帐户
2. 设置输入参数：
   * ASRRemoteFile--存储移动服务安装程序的位置
   * ASRPassphrase--存储 passphrase.txt 文件的位置
   * ASRCSEndpoint--管理服务器的 IP 地址
3. 导入 xPSDesiredStateConfiguration PowerShell 模块
4. 创建并编译 DSC 配置

所有上述步骤均需按正确顺序操作，以开始加入要保护的计算机。

附带部署说明的模板位于 [GitHub](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/DSC)。

使用 PowerShell 部署模板：

```powershell
$RGDeployArgs = @{
    Name = 'DSC3'
    ResourceGroupName = 'KNOMS'
    TemplateFile = 'https://raw.githubusercontent.com/krnese/AzureDeploy/master/OMS/MSOMS/DSC/azuredeploy.json'
    OMSAutomationAccountName = 'KNOMSAA'
    ASRRemoteFile = 'https://knrecstor01.blob.core.windows.net/asr/ASR.zip'
    ASRRemotePassphrase = 'https://knrecstor01.blob.core.windows.net/asr/passphrase.txt'
    ASRCSEndpoint = '10.0.0.115'
    DSCJobGuid = [System.Guid]::NewGuid().ToString()
}
New-AzureRmResourceGroupDeployment @RGDeployArgs -Verbose
```

## <a name="next-steps"></a>后续步骤
部署移动服务代理以后，即可为虚拟机[启用复制](site-recovery-vmware-to-azure.md)。
