---
title: 通过 Azure 自动化状态配置启用计算机以进行管理
description: 如何设置通过 Azure Automation State Configuration 进行管理的计算机
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 12/10/2019
manager: carmonm
ms.openlocfilehash: 52cd72d1144fa2acad993e927d49545d645d596f
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2020
ms.locfileid: "82993740"
---
# <a name="enable-machines-for-management-by-azure-automation-state-configuration"></a>通过 Azure 自动化状态配置启用计算机以进行管理

本主题介绍如何通过 Azure 自动化状态配置来设置计算机以进行管理。 有关此服务的详细信息，请参阅[Azure 自动化状态配置概述](automation-dsc-overview.md)。

## <a name="enable-azure-vms"></a>启用 Azure VM

Azure 自动化状态配置使你可以使用 Azure 门户、Azure 资源管理器模板或 PowerShell 轻松启用用于配置管理的 Azure Vm。 在这种情况下，如果没有管理员远程登录到 VM，Azure VM Desired State Configuration 扩展将使用 Azure 自动化状态配置注册 VM。 由于 Azure 扩展以异步方式运行，因此在对[状态配置进行 VM 设置疑难解答](#troubleshoot-vm-setup-for-state-configuration)中提供了跟踪其进度或排除故障的步骤。

> [!NOTE]
>将 DSC 部署到 Linux 节点将使用 **/tmp**文件夹。 在`nxautomation`将模块安装到适当的位置之前，将暂时下载这些模块进行验证。 为确保正确安装模块，适用于 Linux 的 Log Analytics 代理需要 **/tmp**文件夹的读/写权限。<br><br>
>适用于 Linux 的 Log Analytics 代理以`omsagent`用户身份运行。 若要向`omsagent`用户授予 >写入权限，请运行命令`setfacl -m u:omsagent:rwx /tmp`。

### <a name="enable-a-vm-using-azure-portal"></a>使用 Azure 门户启用 VM

若要使 Azure VM 能够通过[Azure 门户](https://portal.azure.com/)来状态配置：

1. 导航到要在其中启用 Vm 的 Azure 自动化帐户。 

2. 在 "状态配置" 页上，选择 "**节点**" 选项卡，然后单击 "**添加**"。

3. 选择要启用的 VM。

4. 如果计算机未安装 PowerShell 所需状态扩展且电源状态为 "正在运行"，请单击 "**连接**"。

5. 在 "**注册**" 下，输入用例所需的[PowerShell DSC 本地 Configuration Manager 值](/powershell/scripting/dsc/managing-nodes/metaConfig)。 还可以输入要分配给 VM 的节点配置。

![启用 VM](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="enable-a-vm-using-azure-resource-manager-templates"></a>使用 Azure 资源管理器模板启用 VM

可以使用 Azure 资源管理器模板安装和启用用于状态配置的 VM。 请参阅 "[所需状态配置服务管理的服务器](https://azure.microsoft.com/resources/templates/101-automation-configuration/)" 示例模板，该模板启用现有 VM 的状态配置。 如果要管理虚拟机规模集，请参阅[Azure Automation 管理的虚拟机规模集配置](https://azure.microsoft.com/resources/templates/201-vmss-automation-dsc/)中的示例模板。

### <a name="enable-machines-using-powershell"></a>使用 PowerShell 启用计算机

可以在 PowerShell 中使用[AzAutomationDscNode](/powershell/module/az.automation/register-azautomationdscnode) cmdlet 来为状态配置启用 vm。 

> [!NOTE]
>此`Register-AzAutomationDscNode` cmdlet 仅在运行 windows 的计算机上实现，因为它仅触发 windows 扩展。

### <a name="register-vms-across-azure-subscriptions"></a>跨 Azure 订阅注册 Vm

从其他 Azure 订阅注册 Vm 的最佳方式是使用 Azure 资源管理器部署模板中的 DSC 扩展。 [Desired State Configuration 扩展与 Azure 资源管理器模板](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template)中提供了示例。

若要在模板中查找要用作参数的注册密钥和注册 URL，请参阅[使用注册安全地启用计算机](#enable-machines-securely-using-registration)。

## <a name="enable-physicalvirtual-windows-machines"></a>启用物理/虚拟 Windows 计算机

可以允许在本地或其他云环境（包括 AWS EC2 实例）中运行的 Windows 服务器部署到 Azure 自动化状态配置。 服务器必须具有[对 Azure 的出站访问权限](automation-dsc-overview.md#network-planning)。

1. 请确保计算机上已安装最新版本的[WMF 5](https://aka.ms/wmf5latest) ，以便为状态配置启用。 此外，必须在用于启用计算机的计算机上安装 WMF 5。
1. 按照[生成 DSC 元配置](#generate-dsc-metaconfigurations)中的说明创建包含所需 DSC 元配置的文件夹。 
1. 使用以下 cmdlet 将 PowerShell DSC 元配置远程应用于要启用的计算机。 

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. 如果无法远程应用 PowerShell DSC 元配置，请将**元配置**文件夹复制到要启用的计算机。 然后添加代码以在计算机上本地调用[set-dsclocalconfigurationmanager](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager?view=powershell-5.1) 。
1. 使用 Azure 门户或 cmdlet，验证计算机是否显示为在 Azure 自动化帐户中注册的状态配置节点。

## <a name="enable-physicalvirtual-linux-machines"></a>启用物理/虚拟 Linux 计算机

你可以启用在本地或其他云环境中运行的 Linux 服务器进行状态配置。 服务器必须具有[对 Azure 的出站访问权限](automation-dsc-overview.md#network-planning)。

1. 请确保计算机上已安装适用于[Linux 的 PowerShell Desired State Configuration](https://github.com/Microsoft/PowerShell-DSC-for-Linux)的最新版本，以启用状态配置。
2. 如果[POWERSHELL DSC 本地 Configuration Manager 默认值](/powershell/scripting/dsc/managing-nodes/metaConfig4)与用例匹配，并且你想要启用计算机，以便它们都从和报告到状态配置：

   - 在要启用的每台 Linux 计算机`Register.py`上，使用启用具有 PowerShell DSC 本地 Configuration Manager 默认值的计算机。

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   - 若要查找自动化帐户的注册密钥和注册 URL，请参阅[使用注册安全地启用计算机](#enable-machines-securely-using-registration)。

3. 如果 PowerShell DSC 本地 Configuration Manager （LCM）默认值与用例不匹配，或你想要启用仅向 Azure Automation 状态配置报告的计算机，请执行步骤4-7。 否则，请直接转到步骤7。

4. 按照[生成 DSC 元配置](#generate-dsc-metaconfigurations)部分中的说明生成包含所需 DSC 元配置的文件夹。

5. 请确保在用于启用计算机的状态配置的计算机上安装了最新版本的[WMF 5](https://aka.ms/wmf5latest) 。

6. 按如下所示添加代码，将 PowerShell DSC 元配置远程应用于要启用的计算机。

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

7. 如果无法远程应用 PowerShell DSC 元配置，请将步骤4中描述的文件夹中对应于远程计算机的元配置复制到 Linux 计算机。

8. 添加代码以在`Set-DscLocalConfigurationManager.py`每台 Linux 计算机上以本地方式调用，以启用状态配置。

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

9. 使用 Azure 门户或 cmdlet，确保要启用的计算机现在显示为在 Azure 自动化帐户中注册的 DSC 节点。

## <a name="generate-dsc-metaconfigurations"></a>生成 DSC 元配置

若要为任何计算机启用状态配置，可以生成[DSC 元](/powershell/scripting/dsc/managing-nodes/metaConfig)配置。 此配置通知 DSC 代理从和/或报表请求到 Azure 自动化状态配置。 可以使用 PowerShell DSC 配置或 Azure 自动化 PowerShell cmdlet 为 Azure 自动化状态配置生成 DSC 元配置。

> [!NOTE]
> DSC 元配置包含在自动化帐户中启用计算机以进行管理所需的机密。 请务必适当保护所创建的任何 DSC 元配置，或者在使用后将其删除。

元配置的代理支持由 LCM （即 Windows PowerShell DSC 引擎）控制。 LCM 在所有目标节点上运行，负责调用 DSC 元配置脚本中包含的配置资源。 可以通过在`ConfigurationRepositoryWeb`、 `ResourceRepositoryWeb`和`ReportServerWeb`块中包含代理 URL 的定义和代理凭据的定义，在元配置中包含代理支持。 请参阅[配置本地 Configuration Manager](https://docs.microsoft.com/powershell/scripting/dsc/managing-nodes/metaconfig?view=powershell-7)。

### <a name="generate-dsc-metaconfigurations-using-a-dsc-configuration"></a>使用 DSC 配置生成 DSC 元配置

1. 在本地环境中，以计算机上的管理员身份打开 VSCode （或你喜爱的编辑器）。 计算机上必须已安装最新版本的 [WMF 5](https://aka.ms/wmf5latest)。
1. 在本地复制以下脚本。 此脚本包含用于创建元配置的 PowerShell DSC 配置，以及用于开始执行元配置创建操作的命令。

    > [!NOTE]
    > 状态配置节点配置名称在 Azure 门户中区分大小写。 如果大小写不匹配，节点将不会显示在 "**节点**" 选项卡下。

   ```powershell
   # The DSC configuration that will generate metaconfigurations
   [DscLocalConfigurationManager()]
   Configuration DscMetaConfigs
   {
        param
        (
            [Parameter(Mandatory=$True)]
            [String]$RegistrationUrl,

            [Parameter(Mandatory=$True)]
            [String]$RegistrationKey,

            [Parameter(Mandatory=$True)]
            [String[]]$ComputerName,

            [Int]$RefreshFrequencyMins = 30,

            [Int]$ConfigurationModeFrequencyMins = 15,

            [String]$ConfigurationMode = 'ApplyAndMonitor',

            [String]$NodeConfigurationName,

            [Boolean]$RebootNodeIfNeeded= $False,

            [String]$ActionAfterReboot = 'ContinueConfiguration',

            [Boolean]$AllowModuleOverwrite = $False,

            [Boolean]$ReportOnly
        )

        if(!$NodeConfigurationName -or $NodeConfigurationName -eq '')
        {
            $ConfigurationNames = $null
        }
        else
        {
            $ConfigurationNames = @($NodeConfigurationName)
        }

        if($ReportOnly)
        {
            $RefreshMode = 'PUSH'
        }
        else
        {
            $RefreshMode = 'PULL'
        }

        Node $ComputerName
        {
            Settings
            {
                RefreshFrequencyMins           = $RefreshFrequencyMins
                RefreshMode                    = $RefreshMode
                ConfigurationMode              = $ConfigurationMode
                AllowModuleOverwrite           = $AllowModuleOverwrite
                RebootNodeIfNeeded             = $RebootNodeIfNeeded
                ActionAfterReboot              = $ActionAfterReboot
                ConfigurationModeFrequencyMins = $ConfigurationModeFrequencyMins
            }

            if(!$ReportOnly)
            {
            ConfigurationRepositoryWeb AzureAutomationStateConfiguration
                {
                    ServerUrl          = $RegistrationUrl
                    RegistrationKey    = $RegistrationKey
                    ConfigurationNames = $ConfigurationNames
                }

                ResourceRepositoryWeb AzureAutomationStateConfiguration
                {
                    ServerUrl       = $RegistrationUrl
                    RegistrationKey = $RegistrationKey
                }
            }

            ReportServerWeb AzureAutomationStateConfiguration
            {
                ServerUrl       = $RegistrationUrl
                RegistrationKey = $RegistrationKey
            }
        }
   }

    # Create the metaconfigurations
    # NOTE: DSC Node Configuration names are case sensitive in the portal.
    # TODO: edit the below as needed for your use case
   $Params = @{
        RegistrationUrl = '<fill me in>';
        RegistrationKey = '<fill me in>';
        ComputerName = @('<some VM to onboard>', '<some other VM to onboard>');
        NodeConfigurationName = 'SimpleConfig.webserver';
        RefreshFrequencyMins = 30;
        ConfigurationModeFrequencyMins = 15;
        RebootNodeIfNeeded = $False;
        AllowModuleOverwrite = $False;
        ConfigurationMode = 'ApplyAndMonitor';
        ActionAfterReboot = 'ContinueConfiguration';
        ReportOnly = $False;  # Set to $True to have machines only report to AA DSC but not pull from it
   }

   # Use PowerShell splatting to pass parameters to the DSC configuration being invoked
   # For more info about splatting, run: Get-Help -Name about_Splatting
   DscMetaConfigs @Params
   ```

1. 填写自动化帐户的注册密钥和 URL，以及要启用的计算机的名称。 所有其他参数都是可选的。 若要查找自动化帐户的注册密钥和注册 URL，请参阅[使用注册安全地启用计算机](#enable-machines-securely-using-registration)。

1. 如果希望计算机向 Azure 自动化状态配置报告 DSC 状态信息而不是请求配置或 PowerShell 模块，请将`ReportOnly`参数设置为 true。

1. 如果`ReportOnly`未设置，则计算机会将 DSC 状态信息报告给 Azure 自动化状态配置和请求配置或 PowerShell 模块。 在`ConfigurationRepositoryWeb`、 `ResourceRepositoryWeb`和`ReportServerWeb`块中设置相应的参数。

1. 运行该脚本。 你现在应具有名为 "**为 dscmetaconfigs**" 的工作目录文件夹，其中包含要启用的计算机的 PowerShell DSC 元配置（以管理员身份）。

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="generate-dsc-metaconfigurations-using-azure-automation-cmdlets"></a>使用 Azure 自动化 cmdlet 生成 DSC 元配置

如果 PowerShell DSC LCM 默认值与用例匹配，并且你想要允许计算机同时从和报告 Azure 自动化状态配置，则可以使用 Azure 自动化 cmdlet 来更轻松地生成所需的 DSC 元配置。

1. 在本地环境中，以计算机管理员身份打开 PowerShell 控制台或 VSCode。
2. 使用[AzAccount](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0)连接到 Azure 资源管理器。
3. 从设置节点的自动化帐户中，下载要启用的计算机的 PowerShell DSC 元配置。

   ```powershell
   # Define the parameters for Get-AzAutomationDscOnboardingMetaconfig using PowerShell Splatting
   $Params = @{
       ResourceGroupName = 'ContosoResources'; # The name of the Resource Group that contains your Azure Automation account
       AutomationAccountName = 'ContosoAutomation'; # The name of the Azure Automation account where you want a node on-boarded to
       ComputerName = @('web01', 'web02', 'sql01'); # The names of the computers that the metaconfiguration will be generated for
       OutputFolder = "$env:UserProfile\Desktop\";
   }
   # Use PowerShell splatting to pass parameters to the Azure Automation cmdlet being invoked
   # For more info about splatting, run: Get-Help -Name about_Splatting
   Get-AzAutomationDscOnboardingMetaconfig @Params
   ```

1. 你现在应具有一个**为 dscmetaconfigs**文件夹，其中包含要启用的计算机的 PowerShell DSC 元配置（以管理员身份）。

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="enable-machines-securely-using-registration"></a>使用注册安全地启用计算机

可以通过 WMF 5 DSC 注册协议为 Azure 自动化帐户安全地启用计算机。 此协议允许 DSC 节点向 PowerShell DSC 拉取或 Report Server （包括 Azure 自动化状态配置）进行身份验证。 节点在注册 URL 处向服务器注册，并使用注册密钥进行身份验证。 在注册期间，DSC 节点和 DSC 请求/Report Server 会协商用于向服务器进行身份验证的节点的唯一证书。 此过程会阻止已启用的节点相互模拟，例如，如果某个节点被破坏并进行恶意处理。 注册后，注册密钥不再用于身份验证，并从节点中删除。

可以从 Azure 门户中“帐户设置”**** 下的“密钥”**** 中获取 State Configuration 注册协议所需的信息。 

![Azure 自动化密钥和 URL](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- "注册 URL" 是 "密钥" 页上的 "URL" 字段。
- "注册密钥" 是 "密钥" 页上的 "**主访问密钥**" 字段或 "**辅助访问密钥**" 字段的值。 可以使用其中的任一密钥。

为了增加安全性，你可以随时在 "密钥" 页上重新生成自动化帐户的主访问密钥和辅助访问密钥。 密钥重新生成阻止将来的节点注册使用以前的密钥。

## <a name="re-register-a-node"></a>重新注册节点

在将计算机注册为 Azure 自动化状态配置中的 DSC 节点之后，有几个原因可能导致将来需要重新注册该节点。

- **证书续订。** 对于 Windows server 2019 之前的 Windows Server 版本，每个节点会自动协商唯一的身份验证证书，该证书将在一年后过期。 如果证书过期但未续订，则该节点将无法与 Azure 自动化通信，并将`Unresponsive`其标记为。 目前，当证书即将过期时，PowerShell DSC 注册协议无法自动续订证书，你必须在一年之后重新注册这些节点。 重新注册之前，请确保每个节点正在运行 WMF 5 RTM。 

    重新注册在90天或更短时间内从证书过期时间执行，或者在证书过期时间之后的任何时间，将生成新的证书并使用。 Windows Server 2019 和更高版本中提供了此问题的解决方法。

- **DSC LCM 值的更改。** 例如， `ConfigurationMode`你可能需要更改在初始注册节点期间设置的[PowerShell DSC LCM 值](/powershell/scripting/dsc/managing-nodes/metaConfig4)。 目前只能通过重新注册来更改这些 DSC 代理值。 一种例外情况是分配给节点的节点配置值。 可以直接在 Azure Automation DSC 中更改此项。

您可以使用本文档中所述的任何方法，重新注册节点，就像您最初注册节点一样。 重新注册节点之前，不需要从 Azure Automation State Configuration 中注销节点。

## <a name="troubleshoot-vm-setup-for-state-configuration"></a>用于状态配置的 VM 设置疑难解答

状态配置使你能够轻松地为配置管理启用 Azure Windows Vm。 在幕后，Azure VM Desired State Configuration 扩展用于向 Azure Automation State Configuration 注册 VM。 由于 Azure VM 所需状态配置扩展以异步方式运行，因此跟踪其进度和排查其执行问题可能很重要。

> [!NOTE]
> 为使用 azure VM Desired 状态配置扩展的状态配置启用 Azure Windows Vm 的任何方法最多可能需要一小时，Azure 自动化才能将 Vm 显示为已注册。 此延迟的原因是，Azure VM 所需状态配置扩展会在 VM 上安装 WMF 5，这是为状态配置启用 Vm 所必需的。

若要排查或查看 Azure VM 所需状态配置扩展的状态，请执行以下操作：

1. 在 Azure 门户中，导航到要启用的 VM。
2. 单击 "**设置**" 下的 "**扩展**"。 
3. 现在，选择**DSC**或**DSCForLinux**，具体取决于你的操作系统。 
4. 有关详细信息，可以单击“查看详细状态”****。

有关故障排除的详细信息，请参阅[Azure Automation 状态配置故障排除](./troubleshoot/desired-state-configuration.md)。

## <a name="next-steps"></a>后续步骤

- 若要开始，请参阅[Azure 自动化状态配置](automation-dsc-getting-started.md)入门。
- 若要了解如何编译 DSC 配置，以便可以将其分配给目标节点，请参阅[在 Azure 自动化状态配置中编译配置](automation-dsc-compile.md)。
- 有关 PowerShell cmdlet 参考，请参阅 [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
)。
- 有关定价信息，请参阅[Azure 自动化状态配置定价](https://azure.microsoft.com/pricing/details/automation/)。
- 有关在持续部署管道中使用 Azure 自动化状态配置的示例，请参阅使用[示例：使用 Azure 自动化状态配置将持续部署到虚拟机和 Chocolatey](automation-dsc-cd-chocolatey.md)。
