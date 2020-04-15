---
title: 加入 Azure Automation State Configuration 管理的计算机
description: 如何设置通过 Azure Automation State Configuration 进行管理的计算机
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 12/10/2019
manager: carmonm
ms.openlocfilehash: c718b9a66b378044618c8c52eec3a1a498ace83c
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383207"
---
# <a name="onboarding-machines-for-management-by-azure-automation-state-configuration"></a>加入 Azure Automation State Configuration 管理的计算机

## <a name="why-manage-machines-with-azure-automation-state-configuration"></a>为何要使用 Azure 自动化状态配置来管理计算机？

Azure Automation State Configuration 是一个配置管理服务，适用于任何云或本地数据中心内的 Desired State Configuration (DSC) 节点。 通过在**配置管理**下选择**状态配置 （DSC），** 在 Azure 门户中访问它。 

此服务可在中央、安全的位置快速轻松地跨数千台计算机进行可扩展性。 您可以轻松地对计算机进行板载，为其分配声明性配置，并查看显示每台计算机是否符合您指定的所需状态的报告。

适用于 DSC 的 Azure Automation State Configuration 服务类似于 PowerShell 脚本中的 Azure 自动化 Runbook。 换句话说，Azure 自动化以帮助你管理 PowerShell 脚本的相同方式帮助你管理 DSC 配置。 若要详细了解使用 Azure Automation State Configuration 的好处，请参阅 [Azure Automation State Configuration 概述](automation-dsc-overview.md)。

Azure Automation State Configuration 可用于管理各种不同的计算机：

- Azure 虚拟机
- Azure 虚拟机（经典）
- 本地或 Azure 以外的云（包括 AWS EC2 实例）的物理/虚拟 Windows 计算机
- 位于本地、Azure 或 Azure 以外的云中的物理/虚拟 Linux 计算机

如果尚未准备好从云管理计算机配置，则可以使用 Azure 自动化状态配置作为仅报告终结点。 此功能允许您通过 DSC 设置（推送）配置，并在 Azure 自动化中查看报告详细信息。

> [!NOTE]
> 如果已安装的 Azure VM 所需状态配置扩展版本大于 2.70，则包含使用 Azure 自动化状态配置管理 Azure VM 不收取额外费用。 有关详细信息，请参阅[**自动化定价页**](https://azure.microsoft.com/pricing/details/automation/)。

本文的以下各节概述了如何将上面列出的计算机板载到 Azure 自动化状态配置。

>[!NOTE]
>本文进行了更新，以便使用新的 Azure PowerShell Az 模块。 你仍然可以使用 AzureRM 模块，至少在 2020 年 12 月之前，它将继续接收 bug 修补程序。 若要详细了解新的 Az 模块和 AzureRM 兼容性，请参阅[新 Azure Powershell Az 模块简介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有关混合 Runbook 辅助角色上的 Az 模块安装说明，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 对于自动化帐户，可以使用["如何更新 Azure 自动化 中的 Azure PowerShell"模块](automation-update-azure-modules.md)将模块更新到最新版本。

## <a name="onboarding-azure-vms"></a>载入 Azure VM

Azure 自动化状态配置允许您使用 Azure 门户、Azure 资源管理器模板或 PowerShell 轻松将 Azure VM 上载进行配置管理。 在引擎盖下，无需管理员远程连接到 VM，Azure VM 所需的状态配置扩展将 VM 注册为 Azure 自动化状态配置。 由于 Azure 扩展以异步方式运行，因此在本文的"[故障排除 Azure 虚拟机载入"](#troubleshooting-azure-virtual-machine-onboarding)部分中提供了跟踪其进度或故障排除的步骤。

> [!NOTE]
>将 DSC 部署到 Linux 节点使用 **/tmp**文件夹。 在将模块`nxautomation`（如模块安装到其适当位置）之前临时下载以进行验证。 为确保模块正确安装，Linux 的日志分析代理需要 **/tmp**文件夹的读/写权限。<br><br>
>Linux 的日志分析代理以用户身份`omsagent`运行。 要向`omsagent`用户授予>写入权限，请运行 命令`setfacl -m u:omsagent:rwx /tmp`。

### <a name="onboard-a-vm-using-azure-portal"></a>使用 Azure 门户的 VM 板载

通过[Azure 门户](https://portal.azure.com/)将 Azure VM 与 Azure 自动化状态配置一起：

1. 导航到 Azure 自动化帐户，该帐户将 VM 板载。 

2. 在"状态配置"页上，选择 **"节点"** 选项卡，然后单击"**添加**"。

3. 选择要上载的 VM。

4. 如果计算机未安装 PowerShell 所需的状态扩展，并且电源状态正在运行，请单击"**连接**"。

5. 在 **"注册"下**，输入用例所需的[PowerShell DSC 本地配置管理器值](/powershell/scripting/dsc/managing-nodes/metaConfig)。 或者，您可以输入要分配给 VM 的节点配置。

![加入](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="onboard-a-vm-using-azure-resource-manager-templates"></a>使用 Azure 资源管理器模板在 VM 上

您可以使用 Azure 资源管理器模板将 VM 部署到 Azure 自动化状态配置并将其重新上载。 有关将现有 VM 加入 Azure Automation State Configuration 的示例模板，请参阅 [Desired State Configuration 管理的服务器](https://azure.microsoft.com/resources/templates/101-automation-configuration/)。 如果要管理 VM 缩放集，请参阅 Azure 自动化[管理的 VM 规模集配置](https://azure.microsoft.com/resources/templates/201-vmss-automation-dsc/)中的示例模板。

### <a name="onboard-machines-using-powershell"></a>使用 PowerShell 的板载计算机

您可以使用 PowerShell 中的[注册-AzAutomationDscNode](/powershell/module/az.automation/register-azautomationdscnode) cmdlet 将 VM 上载到 Azure 自动化状态配置。 

> [!NOTE]
>`Register-AzAutomationDscNode` cmdlet 目前仅为运行 Windows 的计算机实现，因为它仅触发 Windows 扩展。

### <a name="register-vms-across-azure-subscriptions"></a>跨 Azure 订阅注册 VM

从其他 Azure 订阅注册 VM 的最佳方法是在 Azure 资源管理器部署模板中使用 DSC 扩展。 [Desired State Configuration 扩展与 Azure 资源管理器模板](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template)中提供了示例。

要查找在模板中用作参数的注册密钥和注册 URL，请参阅本文中的"[使用注册"部分安全地载入](#onboarding-securely-using-registration)。

## <a name="onboarding-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances"></a>在本地或 Azure 以外的云（包括 AWS EC2 实例）中安装物理/虚拟 Windows 计算机

您可以将在本地或其他云环境中运行的 Windows 服务器连接到 Azure 自动化状态配置。 服务器必须具有[对 Azure 的出站访问权限](automation-dsc-overview.md#network-planning)。

1. 确保最新版本的[WMF 5](https://aka.ms/wmf5latest)安装在要装机到 Azure 自动化状态配置的计算机上。 此外，WMF 5 必须安装在您用于载入操作的计算机上。
1. 按照[生成 DSC 元配置](#generating-dsc-metaconfigurations)部分中的说明创建包含所需 DSC 元配置的文件夹。 
1. 使用以下 cmdlet 将 PowerShell DSC 元配置远程应用于要上载的计算机。 

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. 如果无法远程应用 PowerShell DSC 元配置，请将**元配置**文件夹复制到要载入的计算机。 然后添加代码在计算机上`Set-DscLocalConfigurationManager`本地调用。
1. 使用 Azure 门户或 cmdlet，验证要上载的计算机是否显示为在 Azure 自动化帐户中注册的状态配置节点。

## <a name="onboarding-physicalvirtual-linux-machines-on-premises-or-in-a-cloud-other-than-azure"></a>在本地或 Azure 以外的云中安装物理/虚拟 Linux 计算机

您可以将在本地或其他云环境中运行的 Linux 服务器连接到 Azure 自动化状态配置。 服务器必须具有[对 Azure 的出站访问权限](automation-dsc-overview.md#network-planning)。

1. 确保最新版本的[Linux PowerShell 所需状态配置](https://github.com/Microsoft/PowerShell-DSC-for-Linux)安装在要装机到 Azure 自动化状态配置的计算机上。
2. 如果[PowerShell DSC 本地配置管理器默认值](/powershell/scripting/dsc/managing-nodes/metaConfig4)与您的用例匹配，并且您希望在计算机上板，以便它们同时从计算机中提取并报告到 Azure 自动化状态配置：

   - 在每台 Linux 计算机上，使用 PowerShell DSC`Register.py`本地配置管理器默认值进行板载。

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   - 要查找自动化帐户的注册密钥和注册 URL，请参阅本文的[注册部分的载入安全](#onboarding-securely-using-registration)版。

3. 如果 PowerShell DSC 本地配置管理器 （LCM） 默认值与您的用例不匹配，或者您希望将仅向 Azure 自动化状态配置报告的计算机板载，请按照步骤 4-7 执行。 否则，直接执行步骤 7。

4. 按照[生成 DSC 元配置](#generating-dsc-metaconfigurations)部分的说明生成包含所需 DSC 元配置的文件夹。

5. 确保最新版本的[WMF 5](https://aka.ms/wmf5latest)安装在用于载入的计算机上。

6. 按照如下方式添加代码，将 PowerShell DSC 元配置远程应用于要上载的计算机。

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

7. 如果无法远程应用 PowerShell DSC 元配置，请将与远程计算机对应的元配置从步骤 4 中描述的文件夹中复制到 Linux 计算机。

8. 将要在每个`Set-DscLocalConfigurationManager.py`Linux 计算机上本地调用的代码添加到 Azure 自动化状态配置的板载。

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

9. 使用 Azure 门户或 cmdlet，请确保要上载的计算机现在显示为 Azure 自动化帐户中注册的 DSC 节点。

## <a name="generating-dsc-metaconfigurations"></a>生成 DSC 元配置

要将任何计算机连接到 Azure 自动化状态配置，可以生成[DSC 元配置](/powershell/scripting/dsc/managing-nodes/metaConfig)。 此配置告诉 DSC 代理从和/或向 Azure 自动化状态配置提取和/或报告。 您可以使用 PowerShell DSC 配置或 Azure 自动化 PowerShell cmdlet 为 Azure 自动化状态配置生成 DSC 元配置。

> [!NOTE]
> DSC 元配置包含将计算机登记到进行管理的自动化帐户的机密。 请务必适当保护所创建的任何 DSC 元配置，或者在使用后将其删除。

对元配置的代理支持由 LCM 控制，LCM 是 Windows PowerShell DSC 引擎。 LCM 在所有目标节点上运行，并负责调用 DSC 元配置脚本中包含的配置资源。 通过在`ConfigurationRepositoryWeb`中`ResourceRepositoryWeb`根据需要包括代理 URL 的定义和代理凭据，可以在元配置中包括代理支持。 `ReportServerWeb` 请参阅[配置本地配置管理器](https://docs.microsoft.com/powershell/scripting/dsc/managing-nodes/metaconfig?view=powershell-7)。

### <a name="generate-dsc-metaconfigurations-using-a-dsc-configuration"></a>使用 DSC 配置生成 DSC 元配置

1. 在本地环境中的计算机上以管理员身份打开 VSCode（或您最喜爱的编辑器）。 计算机上必须已安装最新版本的 [WMF 5](https://aka.ms/wmf5latest)。
1. 在本地复制以下脚本。 此脚本包含用于创建元配置的 PowerShell DSC 配置，以及用于开始执行元配置创建操作的命令。

    > [!NOTE]
    > 状态配置节点配置名称在 Azure 门户中区分大小写。 如果情况不匹配，节点将不会显示在 **"节点"** 选项卡下。

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

1. 填写自动化帐户的注册密钥和 URL，以及要登记的计算机名称。 所有其他参数都是可选的。 要查找自动化帐户的注册密钥和注册 URL，请参阅[使用注册功能安全地载入](#onboarding-securely-using-registration)。

1. 如果希望计算机向 Azure 自动化状态配置报告 DSC 状态信息，但不希望拔取配置或 PowerShell`ReportOnly`模块，则将参数设置为 true。

1. 如果未`ReportOnly`设置，计算机将 DSC 状态信息报告给 Azure 自动化状态配置和拉取配置或 PowerShell 模块。 在`ConfigurationRepositoryWeb`中相应地设置 参数`ResourceRepositoryWeb`，`ReportServerWeb`和 块。

1. 运行该脚本。 现在，您应该有一个称为**DscMetaConfigs**的工作目录文件夹，其中包含用于已上载计算机的 PowerShell DSC 元配置（作为管理员）。

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="generate-dsc-metaconfigurations-using-azure-automation-cmdlets"></a>使用 Azure 自动化 cmdlet 生成 DSC 元配置

如果 PowerShell DSC LCM 默认值与您的用例匹配，并且您希望将计算机从 Azure 自动化状态配置中拉出并报告，则可以更简单地使用 Azure 自动化 cmdlet 生成所需的 DSC 元配置。

1. 在本地环境中的计算机上以管理员身份打开 PowerShell 控制台或 VSCode。
2. 使用[连接-AzAccount](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0)连接到 Azure 资源管理器。
3. 从要在其中设置节点的自动化帐户下载要上载的计算机的 PowerShell DSC 元配置。

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

1. 现在，您应该有一个**DscMetaConfigs**文件夹，其中包含用于已上载计算机的 PowerShell DSC 元配置（作为管理员）。

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="onboarding-securely-using-registration"></a>使用注册安全地载入

计算机可以通过 WMF 5 DSC 注册协议安全地登上 Azure 自动化帐户。 此协议允许 DSC 节点对 PowerShell DSC 拉取服务器或报表服务器进行身份验证，包括 Azure 自动化状态配置。 节点在注册 URL 上向服务器注册并使用注册密钥进行身份验证。 在注册期间，DSC 节点和 DSC 拉/报告服务器协商一个唯一的证书，用于节点在注册后对服务器进行身份验证。 此过程可防止板载节点相互模拟，例如，如果节点受到威胁并恶意行为。 注册后，注册密钥不再用于身份验证，并从节点中删除。

可以从 Azure 门户中“帐户设置”**** 下的“密钥”**** 中获取 State Configuration 注册协议所需的信息。 

![Azure 自动化密钥和 URL](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- 注册 URL 是"密钥"页上的 URL 字段。
- 注册键是 **"密钥"页上的主访问键**字段或**辅助访问键**字段的值。 可以使用其中的任一密钥。

为了增加安全性，您可以随时在"密钥"页上重新生成自动化帐户的主访问密钥和辅助访问密钥。 密钥重新生成可防止将来的节点注册使用以前的密钥。

## <a name="re-registering-a-node"></a>重新注册节点

在 Azure 自动化状态配置中将计算机注册为 DSC 节点后，可能需要在将来重新注册该节点有几个原因。

- **证书续订。** 对于 Windows Server 2019 之前的 Windows 服务器版本，每个节点会自动协商唯一的身份验证证书，该证书将在一年后过期。 如果证书过期而不续订，则节点无法与 Azure 自动化通信并标记为`Unresponsive`。 目前，PowerShell DSC 注册协议无法在证书即将到期时自动续订证书，您必须在一年后重新注册节点。 在重新注册之前，请确保每个节点都运行 WMF 5 RTM。 

    重新注册在证书过期时间 90 天或更短的时间执行，或在证书过期时间之后的任何时间，导致生成和使用新证书。 Windows Server 2019 和更高版本中提供了此问题的解决方法。

- **对 DSC LCM 值的更改。** 您可能需要更改在节点初始注册期间设置的[PowerShell DSC LCM 值](/powershell/scripting/dsc/managing-nodes/metaConfig4)，例如。 `ConfigurationMode` 目前，您只能通过重新注册来更改这些 DSC 代理值。 一个例外是分配给节点的节点配置值。 您可以在 Azure 自动化 DSC 中直接更改此更改。

您可以使用本文档中描述的任何载入方法重新注册节点，就像最初注册节点一样。 重新注册节点之前，不需要从 Azure Automation State Configuration 中注销节点。

## <a name="troubleshooting-azure-virtual-machine-onboarding"></a>排查 Azure 虚拟机登记问题

Azure Automation State Configuration 可让你轻松加入 Azure Windows VM 以进行配置管理。 在幕后，Azure VM Desired State Configuration 扩展用于向 Azure Automation State Configuration 注册 VM。 由于 Azure VM 所需的状态配置扩展以异步方式运行，因此跟踪其进度并排除其执行故障可能很重要。

> [!NOTE]
> 任何使用 Azure VM 所需状态配置扩展将 Azure Windows VM 载入 Azure 自动化状态配置的方法最多可能需要一个小时才能将其显示为已注册。 此延迟是由于 Azure VM 所需状态配置扩展在 VM 上安装 WMF 5，这是将 VM 装载到 Azure 自动化状态配置所必需的。

要排除故障或查看 Azure VM 所需状态配置扩展的状态，请使用以下操作：

1. 在 Azure 门户中，导航到正在上载的 VM。
2. 单击 **"设置"** 下的 **"扩展**"。 
3. 现在选择**DSC**或**DSCForLinux，** 这取决于你的操作系统。 
4. 有关详细信息，可以单击“查看详细状态”****。

有关故障排除的详细信息，请参阅[排查 Azure 自动化 Desired State Configuration (DSC) 问题](./troubleshoot/desired-state-configuration.md)。

## <a name="next-steps"></a>后续步骤

- 要开始，请参阅[开始使用 Azure 自动化状态配置](automation-dsc-getting-started.md)。
- 要了解如何编译 DSC 配置以便将它们分配给目标节点，请参阅[在 Azure 自动化状态配置中编译配置](automation-dsc-compile.md)。
- 有关 PowerShell cmdlet 引用，请参阅[Az.自动化](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
)。
- 有关定价信息，请参阅[Azure 自动化状态配置定价](https://azure.microsoft.com/pricing/details/automation/)。
- 有关在连续部署管道中使用 Azure 自动化状态配置的示例，请参阅[使用示例：使用 Azure 自动化状态配置对虚拟机的连续部署和巧克力](automation-dsc-cd-chocolatey.md)。
