---
title: 启用 Azure Automation State Configuration
description: 本文介绍如何设置通过 Azure Automation State Configuration 进行管理的计算机。
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 12/10/2019
manager: carmonm
ms.openlocfilehash: ae268534a18a921cca012881fa172261c7ba1063
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86186395"
---
# <a name="enable-azure-automation-state-configuration"></a>启用 Azure Automation State Configuration

本主题介绍如何设置通过 Azure Automation State Configuration 进行管理的计算机。 有关该服务的详细信息，请参阅 [Azure Automation State Configuration 概述](automation-dsc-overview.md)。

## <a name="enable-azure-vms"></a>启用 Azure VM

Azure Automation State Configuration 让你能够使用 Azure 门户、Azure 资源管理器模板或 PowerShell 轻松启用 Azure VM 以进行配置管理。 在后台，在不需要管理员远程连接到 VM 的情况下，Azure VM Desired State Configuration 扩展会在 Azure Automation State Configuration 中注册 VM。 由于 Azure 扩展以异步方式运行，因此[检查 VM 设置状态](#check-status-of-vm-setup)中提供了跟踪其进度的步骤。

> [!NOTE]
>将 DSC 部署到 Linux 节点时，要使用 /tmp 文件夹。 临时下载 `nxautomation` 等模块进行验证，然后再将它们安装到适当的位置。 为确保正确安装模块，适用于 Linux 的 Log Analytics 代理需要具有 /tmp 文件夹的读/写权限。<br><br>
>适用于 Linux 的 Log Analytics 代理以 `omsagent` 用户身份运行。 若要向 `omsagent` 用户授予 >写入权限，请运行命令 `setfacl -m u:omsagent:rwx /tmp`。

### <a name="enable-a-vm-using-azure-portal"></a>使用 Azure 门户启用 VM

若要使 Azure VM 能够通过 [Azure 门户](https://portal.azure.com/)进行状态配置，请执行以下操作：

1. 导航至要在其中启用 VM 的 Azure 自动化帐户。 

2. 在“State Configuration”页中，选择“节点”选项卡，然后单击“添加” 。

3. 选择要启用的 VM。

4. 如果虚拟机未安装 PowerShell 所需状态扩展且电源状态为“正在运行”，请单击“连接”。

5. 在“注册”下，输入用例所需的 [PowerShell DSC 本地配置管理器值](/powershell/scripting/dsc/managing-nodes/metaConfig)。 可以选择性地输入要分配给 VM 的节点配置。

![启用 VM](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="enable-a-vm-using-azure-resource-manager-templates"></a>使用 Azure 资源管理器模板启用 VM

可以使用 Azure 资源管理器模板安装和启用 VM 以用于 State Configuration。 有关启用现有 VM 以用于 State Configuration 的示例模板，请参阅 [Desired State Configuration 服务管理的服务器](https://azure.microsoft.com/resources/templates/101-automation-configuration/)。 如果要管理虚拟机规模集，请参阅 [Azure 自动化管理虚拟机规模集配置](https://azure.microsoft.com/resources/templates/201-vmss-automation-dsc/)中的示例模板。

### <a name="enable-machines-using-powershell"></a>使用 PowerShell 启用虚拟机

可以在 PowerShell 中使用 [Register-AzAutomationDscNode](/powershell/module/az.automation/register-azautomationdscnode) cmdlet，以启用用于 State Configuration 的 VM。 

> [!NOTE]
>当前仅为运行 Windows 的计算机实施 `Register-AzAutomationDscNode` cmdlet，因为它仅触发 Windows 扩展。

### <a name="register-vms-across-azure-subscriptions"></a>在各 Azure 订阅中注册 VM

从其他 Azure 订阅中注册 VM 的最佳方式是使用 Azure 资源管理器部署模板中的 DSC 扩展。 [Desired State Configuration 扩展与 Azure 资源管理器模板](../virtual-machines/extensions/dsc-template.md)中提供了相关示例。

若要查找注册密钥和注册 URL 以作为模板中的参数，请参阅[使用注册安全启用计算机](#enable-machines-securely-using-registration)。

## <a name="enable-physicalvirtual-windows-machines"></a>启用 Windows 物理/虚拟计算机

可以启用在本地或其他云环境（包括 AWS EC2 实例）中运行的 Windows 服务器，以便 Azure Automation State Configuration。 服务器必须具有 [Azure 的出站访问权限](automation-dsc-overview.md#network-planning)。

1. 确保在为 State Configuration 启用的计算机上安装了新版本的 [WMF 5](https://aka.ms/wmf5latest)。 此外，必须在用于启用 Windows 物理/虚拟计算机的计算机上安装 WMF 5。
1. 请按照[生成 DSC 元配置](#generate-dsc-metaconfigurations)中的说明创建一个包含所需 DSC 元配置的文件夹。 
1. 使用以下 cmdlet 将 PowerShell DSC 元配置远程应用于要启用的计算机。 

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. 如果无法从远程应用 PowerShell DSC 元配置，请将“元配置”文件夹复制到要启用的计算机。 然后添加代码以在计算机上本地调用 [Set-DscLocalConfigurationManager](/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager?view=powershell-5.1)。
1. 使用 Azure 门户或 cmdlet，验证计算机是否显示为在 Azure 自动化帐户中注册的“State Configuration”节点。

## <a name="enable-physicalvirtual-linux-machines"></a>启用 Linux 物理/虚拟计算机

可以为 State Configuration 启用在本地或其他云环境中运行的 Linux 服务器。 服务器必须具有 [Azure 的出站访问权限](automation-dsc-overview.md#network-planning)。

1. 确保在为 State Configuration 启用的计算机上安装了[适用于 Linux 的 PowerShell Desired State Configuration](https://github.com/Microsoft/PowerShell-DSC-for-Linux)的最新版本。
2. 如果 [PowerShell DSC 本地配置管理器默认值](/powershell/scripting/dsc/managing-nodes/metaConfig4)与用例匹配，并且想要启用计算机以便从 State Configuration 提取信息并向其报告：

   - 在要启用的每个 Linux 计算机上，使用 `Register.py` 启用使用 PowerShell DSC 本地配置管理器默认值的计算机。

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   - 若要查找自动化帐户的注册密钥和注册 URL，请参阅[使用注册安全启用计算机](#enable-machines-securely-using-registration)。

3. 如果 PowerShell DSC 本地配置管理器 (LCM) 默认值与用例不匹配，或者想要启用仅向 Azure Automation State Configuration 报告的计算机，请按照步骤 4-7 进行操作。 否则，请直接跳到步骤 7。

4. 请按照[生成 DSC 元配置](#generate-dsc-metaconfigurations)部分中的说明创建一个包含所需 DSC 元配置的文件夹。

5. 确保在计算机上安装了新版本的 [WMF 5](https://aka.ms/wmf5latest)，该计算机用于为 State Configuration 启用你的虚拟机。

6. 添加如下代码，将 PowerShell DSC 元配置远程应用于要启用的计算机。

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

7. 如果无法远程应用 PowerShell DSC 元配置，请将与远程计算机相对应的元配置从步骤 4 中所述的文件夹复制到 Linux 计算机。

8. 添加代码，在为 State Configuration 启用的每台 Linux 计算机上本地调用 `Set-DscLocalConfigurationManager.py`。

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

9. 使用 Azure 门户或 cmdlet，确保要启用的计算机当前在 Azure 自动化帐户中显示为注册的 DSC 节点。

## <a name="generate-dsc-metaconfigurations"></a>生成 DSC 元配置

若要启用任何计算机以用于 State Configuration，可以生成 [DSC 元配置](/powershell/scripting/dsc/managing-nodes/metaConfig)。 此配置通知 DSC 代理从 Azure Automation State Configuration 提取信息和/或向其报告。 Azure Automation State Configuration 的 DSC 元配置可以使用 PowerShell DSC 配置或 Azure 自动化 PowerShell cmdlet 生成。

> [!NOTE]
> DSC 元配置包含在自动化帐户中启用计算机以便进行管理所需的机密。 请务必适当保护所创建的任何 DSC 元配置，或者在使用后将其删除。

元配置的代理支持由[本地配置管理器](/powershell/scripting/dsc/managing-nodes/metaconfig?view=powershell-7)（即 Windows PowerShell DSC 引擎）控制。 LCM 在所有的目标节点上运行，负责调用 DSC 元配置脚本中包含的配置资源。 通过在 `ConfigurationRepositoryWeb`、`ResourceRepositoryWeb` 和 `ReportServerWeb` 块中根据需要包含 `ProxyURL` 和 `ProxyCredential` 属性的定义，可以在元配置中包含代理支持。 例如，URL 设置为 `ProxyURL = "http://172.16.3.6:3128";`。 `ProxyCredential` 属性设置为 `PSCredential` 对象，如[在 Azure 自动化中管理凭据](shared-resources/credentials.md)中所述。 

### <a name="generate-dsc-metaconfigurations-using-a-dsc-configuration"></a>使用 DSC 配置生成 DSC 元配置

1. 在本地环境中，以计算机管理员身份打开 VSCode（或偏好的编辑器）。 计算机上必须已安装最新版本的 [WMF 5](https://aka.ms/wmf5latest)。
1. 在本地复制以下脚本。 此脚本包含用于创建元配置的 PowerShell DSC 配置，以及用于开始执行元配置创建操作的命令。

    > [!NOTE]
    > State Configuration 节点配置名称在 Azure 门户中区分大小写。 如果大小写不匹配，节点将不会显示在“节点”选项卡下。

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

1. 填写自动化帐户的注册密钥和 URL，以及要启用的计算机名称。 所有其他参数都是可选的。 若要查找自动化帐户的注册密钥和注册 URL，请参阅[使用注册安全启用计算机](#enable-machines-securely-using-registration)。

1. 如果希望计算机向 Azure Automation State Configuration 报告 DSC 状态信息但不提取配置或 PowerShell 模块，请将 `ReportOnly` 参数设置为 true。

1. 如果未将 `ReportOnly` 设置为 true，则计算机向 Azure Automation State Configuration 报告 DSC 状态信息并提取配置或 PowerShell 模块。 在 `ConfigurationRepositoryWeb`、`ResourceRepositoryWeb` 和 `ReportServerWeb` 块中相应地设置参数。

1. 运行该脚本。 现在，应有一个名为 DscMetaConfigs 的工作目录文件夹，其中包含要启用的计算机的 PowerShell DSC 元配置（作为管理员）。

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="generate-dsc-metaconfigurations-using-azure-automation-cmdlets"></a>使用 Azure 自动化 cmdlet 生成 DSC 元配置

如果 PowerShell DSC LCM 默认值与用例匹配，并且你希望计算机能同时从 Azure Automation State Configuration 提取配置并向其报告状态信息，则可以使用 Azure 自动化 cmdlet 更轻松地生成所需的 DSC 元配置。

1. 在本地环境中，以计算机管理员身份打开 PowerShell 控制台或 VSCode。
2. 使用 [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0) 连接到 Azure 资源管理器。
3. 从要设置节点的目标自动化帐户下载想要启用的计算机的 PowerShell DSC 元配置。

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

1. 现在，应有一个名为 DscMetaConfigs 的文件夹，其中包含要启用的计算机的 PowerShell DSC 元配置（作为管理员）。

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="enable-machines-securely-using-registration"></a>使用注册安全启用计算机

可以通过 WMF 5 DSC 注册协议为 Azure 自动化帐户安全地启用计算机。 此协议允许 DSC 节点向 PowerShell DSC 提取或报告服务器（包括 Azure Automation State Configuration）进行身份验证。 节点将注册到位于注册 URL 的服务器，并使用注册密钥进行身份验证。 在注册期间，DSC 节点和 DSC 提取服务器/报告服务器将协商唯一证书，以便在注册后使用此节点向服务器进行身份验证。 此过程可防止启用的节点彼此模拟，例如当节点遭到入侵并且具有恶意行为时。 注册之后，注册密钥不再用于身份验证，而是从节点中删除。

可以从 Azure 门户中“帐户设置”下的“密钥”中获取 State Configuration 注册协议所需的信息。 

![Azure 自动化密钥和 URL](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- “注册 URL”是“密钥”页中的“URL”字段。
- 注册密钥是“密钥”页上的“主访问密钥”或“辅助访问密钥”字段值。 可以使用其中的任一密钥。

为了增加安全性，可以随时在“密钥”页上重新生成自动化帐户的主访问密钥和辅助访问密钥。 密钥重新生成阻止日后的节点注册使用以前的密钥。

## <a name="re-register-a-node"></a>重新注册节点

在将计算机注册为 Azure Automation State Configuration 中的 DSC 节点之后，有多种原因可能促使你需要将来重新注册该节点。

- 证书续订。 对于 Windows Server 2019 之前的 Windows Server 版本，每个节点会自动协商唯一的身份验证证书，该证书于一年之后过期。 如果证书过期但未续订，则该节点将无法与 Azure 自动化通信，并将标记为 `Unresponsive`。 目前，当证书即将过期时，PowerShell DSC 注册协议无法自动续订证书，因此需要在一年之后重新注册这些节点。 重新注册之前，请确保每个节点运行的是 WMF 5 RTM。 

    与证书过期时间相距 90 天或更短时间内执行的重新注册，或者在证书过期时间之后任何时间点执行的重新注册，将生成新的证书并提供使用。 Windows Server 2019 和更高版本提供了对此问题的解决方法。

- **DSC LCM 值的更改。** 可能需要更改在节点初始注册期间设置的 [PowerShell DSC LCM 值](/powershell/scripting/dsc/managing-nodes/metaConfig4)，例如 `ConfigurationMode`。 目前，只能通过重新注册更改这些 DSC 代理值。 分配给节点的节点配置值是一种例外情况。 可以直接在 Azure 自动化 DSC 中更改此值。

可以使用本文介绍的任何方法重新注册节点，就和最初注册节点一样。 重新注册节点之前，不需要从 Azure Automation State Configuration 中注销节点。

## <a name="check-status-of-vm-setup"></a>检查 VM 设置的状态

State Configuration 可让你轻松启用 Azure Windows VM 以进行配置管理。 在幕后，Azure VM Desired State Configuration 扩展用于向 Azure Automation State Configuration 注册 VM。 由于 Azure VM Desired State Configuration 以异步方式运行，跟踪其进度和排查其执行问题可能很重要。

> [!NOTE]
> 对于使用了 Azure VM Desired State Configuration 扩展的适于 State Configuration 的 Azure Windows VM，任何启用方法可能都需要花费多达一小时的时间，Azure 自动化才将 VM 显示为已注册。 之所以出现上述延迟，是因为 Azure VM Desired State Configuration 扩展在 VM 上安装了 WMF 5，这是启用适于 State Configuration 的 VM 的必要条件。

若要查看 Azure VM Desired State Configuration 扩展的状态，请执行以下操作：

1. 在 Azure 门户中，导航到要启用的 VM。
2. 在“设置”下面，单击“扩展”。  
3. 现在选择 DSC 或 DSCForLinux，具体取决于操作系统。 
4. 有关详细信息，可以单击“查看详细状态”。

## <a name="next-steps"></a>后续步骤

- 有关入门信息，请参阅 [Azure Automation State Configuration 入门](automation-dsc-getting-started.md)。
- 若要了解如何编译 DSC 配置，以便将它们分配给目标节点，请参阅[在 Azure Automation State Configuration 中编译 DSC 配置](automation-dsc-compile.md)。
- 有关 PowerShell cmdlet 参考，请参阅 [Az.Automation](/powershell/module/az.automation/?view=azps-3.7.0#automation)。
- 有关定价信息，请参阅 [Azure Automation State Configuration 定价](https://azure.microsoft.com/pricing/details/automation/)。
- 若要查看在持续部署管道中使用 Azure Automation State Configuration 的示例，请参阅[使用 Chocolatey 设置持续部署](automation-dsc-cd-chocolatey.md)。
- 有关疑难解答信息，请参阅 [Azure Automation State Configuration 疑难解答](./troubleshoot/desired-state-configuration.md)。
