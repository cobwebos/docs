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
ms.openlocfilehash: d4e008a0bd43f10b01d78a1c388f1ca6fee983ab
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81457733"
---
# <a name="onboarding-machines-for-management-by-azure-automation-state-configuration"></a>加入 Azure Automation State Configuration 管理的计算机

## <a name="why-manage-machines-with-azure-automation-state-configuration"></a>为何要使用 Azure 自动化状态配置来管理计算机？

Azure Automation State Configuration 是一个配置管理服务，适用于任何云或本地数据中心内的 Desired State Configuration (DSC) 节点。 可以通过在 "**配置管理**" 下选择 "**状态配置（DSC）** "，在 Azure 门户中访问它。 

通过此服务，可从一个中心安全位置快速轻松地跨数千台计算机。 你可以轻松地载入计算机，为其分配声明性配置，并查看显示每台计算机的符合性所指定的所需状态的报告。

适用于 DSC 的 Azure Automation State Configuration 服务类似于 PowerShell 脚本中的 Azure 自动化 Runbook。 换句话说，Azure 自动化以帮助你管理 PowerShell 脚本的相同方式帮助你管理 DSC 配置。 若要详细了解使用 Azure Automation State Configuration 的好处，请参阅 [Azure Automation State Configuration 概述](automation-dsc-overview.md)。

Azure Automation State Configuration 可用于管理各种不同的计算机：

- Azure 虚拟机
- Azure 虚拟机（经典）
- 本地或 Azure 以外的云中的物理/虚拟 Windows 计算机（包括 AWS EC2 实例）
- 位于本地、Azure 或 Azure 以外的云中的物理/虚拟 Linux 计算机

如果你未准备好从云管理计算机配置，则可以使用 Azure 自动化状态配置作为仅限报告的终结点。 此功能允许通过 DSC 设置（推送）配置并在 Azure 自动化中查看报表详细信息。

> [!NOTE]
> 如果已安装的 Azure VM Desired State Configuration 扩展版本大于2.70，则不需要额外付费地管理 Azure Vm。 有关详细信息，请参阅[**自动化定价页**](https://azure.microsoft.com/pricing/details/automation/)。

本文的以下部分概述了如何将上面列出的计算机内置于 Azure 自动化状态配置。

>[!NOTE]
>本文进行了更新，以便使用新的 Azure PowerShell Az 模块。 你仍然可以使用 AzureRM 模块，至少在 2020 年 12 月之前，它将继续接收 bug 修补程序。 若要详细了解新的 Az 模块和 AzureRM 兼容性，请参阅[新 Azure Powershell Az 模块简介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有关混合 Runbook 辅助角色上的 Az 模块安装说明，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 对于自动化帐户，可参阅[如何更新 Azure 自动化中的 Azure PowerShell 模块](automation-update-azure-modules.md)，将模块更新到最新版本。

## <a name="onboarding-azure-vms"></a>加入 Azure Vm

使用 azure 自动化状态配置，可以使用 Azure 门户、Azure 资源管理器模板或 PowerShell 轻松将 Azure Vm 载入配置管理。 在这种情况下，如果没有管理员远程登录到 VM，Azure VM Desired State Configuration 扩展将使用 Azure 自动化状态配置注册 VM。 由于 Azure 扩展以异步方式运行，因此在本文[疑难解答 azure 虚拟机载入](#troubleshooting-azure-virtual-machine-onboarding)部分中提供了跟踪其进度或解决问题的步骤。

> [!NOTE]
>将 DSC 部署到 Linux 节点将使用 **/tmp**文件夹。 在`nxautomation`将模块安装到适当的位置之前，将暂时下载这些模块进行验证。 为确保正确安装模块，适用于 Linux 的 Log Analytics 代理需要 **/tmp**文件夹的读/写权限。<br><br>
>适用于 Linux 的 Log Analytics 代理以`omsagent`用户身份运行。 若要向`omsagent`用户授予 >写入权限，请运行命令`setfacl -m u:omsagent:rwx /tmp`。

### <a name="onboard-a-vm-using-azure-portal"></a>使用 Azure 门户载入 VM

通过[Azure 门户](https://portal.azure.com/)将 azure VM 集成到 Azure 自动化状态配置：

1. 导航到要在其中载入 Vm 的 Azure 自动化帐户。 

2. 在 "状态配置" 页上，选择 "**节点**" 选项卡，然后单击 "**添加**"。

3. 选择要载入的虚拟机。

4. 如果计算机未安装 PowerShell 所需状态扩展且电源状态为 "正在运行"，请单击 "**连接**"。

5. 在 "**注册**" 下，输入用例所需的[PowerShell DSC 本地 Configuration Manager 值](/powershell/scripting/dsc/managing-nodes/metaConfig)。 还可以输入要分配给 VM 的节点配置。

![加入](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="onboard-a-vm-using-azure-resource-manager-templates"></a>使用 Azure 资源管理器模板载入 VM

可以使用 Azure 资源管理器模板将 VM 部署并载入 Azure 自动化状态配置。 有关将现有 VM 加入 Azure Automation State Configuration 的示例模板，请参阅 [Desired State Configuration 管理的服务器](https://azure.microsoft.com/resources/templates/101-automation-configuration/)。 如果要管理虚拟机规模集，请参阅[Azure Automation 管理的虚拟机规模集配置](https://azure.microsoft.com/resources/templates/201-vmss-automation-dsc/)中的示例模板。

### <a name="onboard-machines-using-powershell"></a>使用 PowerShell 加入计算机

可以在 PowerShell 中使用[AzAutomationDscNode](/powershell/module/az.automation/register-azautomationdscnode) Cmdlet 将 Vm 加入 Azure 自动化状态配置。 

> [!NOTE]
>此`Register-AzAutomationDscNode` cmdlet 仅在运行 windows 的计算机上实现，因为它仅触发 windows 扩展。

### <a name="register-vms-across-azure-subscriptions"></a>跨 Azure 订阅注册 Vm

从其他 Azure 订阅注册 Vm 的最佳方式是使用 Azure 资源管理器部署模板中的 DSC 扩展。 [Desired State Configuration 扩展与 Azure 资源管理器模板](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template)中提供了示例。

若要在模板中查找要用作参数的注册密钥和注册 URL，请参阅本文中的[使用注册安全加入](#onboarding-securely-using-registration)部分。

## <a name="onboarding-physicalvirtual-windows-machines"></a>加入物理/虚拟 Windows 计算机

你可以将在本地或其他云环境（包括 AWS EC2 实例）中运行的 Windows server 内置于 Azure 自动化状态配置。 服务器必须具有[对 Azure 的出站访问权限](automation-dsc-overview.md#network-planning)。

1. 请确保计算机上已安装最新版本的[WMF 5](https://aka.ms/wmf5latest) ，以载入 Azure 自动化状态配置。 此外，必须在用于载入操作的计算机上安装 WMF 5。
1. 按照[生成 DSC 元配置](#generating-dsc-metaconfigurations)部分中的说明创建包含所需 DSC 元配置的文件夹。 
1. 使用以下 cmdlet 将 PowerShell DSC 元配置远程应用到想要登记的计算机。 

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. 如果无法远程应用 PowerShell DSC 元配置，请将**元配置**文件夹复制到要载入的计算机。 然后添加代码以在计算机上本地调用[set-dsclocalconfigurationmanager](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager?view=powershell-5.1) 。
1. 使用 Azure 门户或 cmdlet，验证要内置的计算机是否显示为在 Azure 自动化帐户中注册的状态配置节点。

## <a name="onboarding-physicalvirtual-linux-machines"></a>载入物理/虚拟 Linux 计算机

可以将在本地或其他云环境中运行的 Linux 服务器载入 Azure 自动化状态配置。 服务器必须具有[对 Azure 的出站访问权限](automation-dsc-overview.md#network-planning)。

1. 请确保已在计算机上安装[适用于 Linux 的 PowerShell Desired State configuration](https://github.com/Microsoft/PowerShell-DSC-for-Linux)的最新版本，并将其载入 Azure 自动化状态配置。
2. 如果[POWERSHELL DSC 本地 Configuration Manager 默认值](/powershell/scripting/dsc/managing-nodes/metaConfig4)与用例匹配，并且你想要将计算机加入计算机，以便它们都从 Azure 自动化状态配置中提取和报告，请执行以下操作：

   - 在每台 Linux 计算机上，使用`Register.py` PowerShell DSC 本地 Configuration Manager 默认值进行载入。

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   - 若要查找自动化帐户的注册密钥和注册 URL，请参阅本文的[使用注册安全加入](#onboarding-securely-using-registration)部分。

3. 如果 PowerShell DSC 本地 Configuration Manager （LCM）默认值与用例不匹配，或你想要将仅向 Azure 自动化状态配置报告的计算机加入，请执行步骤4-7。 否则，请直接转到步骤7。

4. 按照[生成 DSC 元配置](#generating-dsc-metaconfigurations)部分中的说明生成包含所需 DSC 元配置的文件夹。

5. 请确保在用于载入的计算机上安装了最新版本的[WMF 5](https://aka.ms/wmf5latest) 。

6. 按如下所示添加代码，将 PowerShell DSC 元配置远程应用到想要登记的计算机。

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

7. 如果无法远程应用 PowerShell DSC 元配置，请将步骤4中描述的文件夹中对应于远程计算机的元配置复制到 Linux 计算机。

8. 添加代码以在`Set-DscLocalConfigurationManager.py`每台 Linux 计算机上本地调用，以载入 Azure 自动化状态配置。

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

9. 使用 Azure 门户或 cmdlet，确保要集成的计算机现在显示为在 Azure 自动化帐户中注册的 DSC 节点。

## <a name="generating-dsc-metaconfigurations"></a>生成 DSC 元配置

若要将任何计算机登记到 Azure 自动化状态配置，可以生成[DSC 元](/powershell/scripting/dsc/managing-nodes/metaConfig)配置。 此配置通知 DSC 代理从和/或报表请求到 Azure 自动化状态配置。 可以使用 PowerShell DSC 配置或 Azure 自动化 PowerShell cmdlet 为 Azure 自动化状态配置生成 DSC 元配置。

> [!NOTE]
> DSC 元配置包含将计算机登记到进行管理的自动化帐户的机密。 请务必适当保护所创建的任何 DSC 元配置，或者在使用后将其删除。

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

1. 填写自动化帐户的注册密钥和 URL，以及要登记的计算机名称。 所有其他参数都是可选的。 若要查找自动化帐户的注册密钥和注册 URL，请参阅[使用注册安全加入](#onboarding-securely-using-registration)部分。

1. 如果希望计算机向 Azure 自动化状态配置报告 DSC 状态信息而不是请求配置或 PowerShell 模块，请将`ReportOnly`参数设置为 true。

1. 如果`ReportOnly`未设置，则计算机会将 DSC 状态信息报告给 Azure 自动化状态配置和请求配置或 PowerShell 模块。 在`ConfigurationRepositoryWeb`、 `ResourceRepositoryWeb`和`ReportServerWeb`块中设置相应的参数。

1. 运行该脚本。 你现在应具有名为 "**为 dscmetaconfigs**" 的工作目录文件夹，其中包含要加入的计算机的 PowerShell DSC 元配置（以管理员身份）。

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="generate-dsc-metaconfigurations-using-azure-automation-cmdlets"></a>使用 Azure 自动化 cmdlet 生成 DSC 元配置

如果 PowerShell DSC LCM 默认值与用例匹配，并且你想要将计算机登记为从和报告到 Azure 自动化状态配置，则可以使用 Azure 自动化 cmdlet 来更轻松地生成所需的 DSC 元配置。

1. 在本地环境中，以计算机管理员身份打开 PowerShell 控制台或 VSCode。
2. 使用[AzAccount](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0)连接到 Azure 资源管理器。
3. 从设置节点的自动化帐户中，下载要加入的计算机的 PowerShell DSC 元配置。

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

1. 你现在应具有一个**为 dscmetaconfigs**文件夹，其中包含要加入的计算机的 PowerShell DSC 元配置（以管理员身份）。

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="onboarding-securely-using-registration"></a>使用注册安全地加入

计算机可以通过 WMF 5 DSC 注册协议安全地集成到 Azure 自动化帐户。 此协议允许 DSC 节点向 PowerShell DSC 拉取或 Report Server （包括 Azure 自动化状态配置）进行身份验证。 节点在注册 URL 处向服务器注册，并使用注册密钥进行身份验证。 在注册期间，DSC 节点和 DSC 请求/Report Server 会协商用于向服务器进行身份验证的节点的唯一证书。 此过程可防止载入的节点彼此模拟，例如，如果某个节点被泄露并进行恶意处理。 注册后，注册密钥不再用于身份验证，并从节点中删除。

可以从 Azure 门户中“帐户设置”**** 下的“密钥”**** 中获取 State Configuration 注册协议所需的信息。 

![Azure 自动化密钥和 URL](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- "注册 URL" 是 "密钥" 页上的 "URL" 字段。
- "注册密钥" 是 "密钥" 页上的 "**主访问密钥**" 字段或 "**辅助访问密钥**" 字段的值。 可以使用其中的任一密钥。

为了增加安全性，你可以随时在 "密钥" 页上重新生成自动化帐户的主访问密钥和辅助访问密钥。 密钥重新生成阻止将来的节点注册使用以前的密钥。

## <a name="re-registering-a-node"></a>重新注册节点

在将计算机注册为 Azure 自动化状态配置中的 DSC 节点之后，有几个原因可能导致将来需要重新注册该节点。

- **证书续订。** 对于 Windows server 2019 之前的 Windows Server 版本，每个节点会自动协商唯一的身份验证证书，该证书将在一年后过期。 如果证书过期但未续订，则该节点将无法与 Azure 自动化通信，并将`Unresponsive`其标记为。 目前，当证书即将过期时，PowerShell DSC 注册协议无法自动续订证书，你必须在一年之后重新注册这些节点。 重新注册之前，请确保每个节点正在运行 WMF 5 RTM。 

    重新注册在90天或更短时间内从证书过期时间执行，或者在证书过期时间之后的任何时间，将生成新的证书并使用。 Windows Server 2019 和更高版本中提供了此问题的解决方法。

- **DSC LCM 值的更改。** 例如， `ConfigurationMode`你可能需要更改在初始注册节点期间设置的[PowerShell DSC LCM 值](/powershell/scripting/dsc/managing-nodes/metaConfig4)。 目前只能通过重新注册来更改这些 DSC 代理值。 一种例外情况是分配给节点的节点配置值。 可以直接在 Azure Automation DSC 中更改此项。

你可以使用本文档中所述的任何载入方法，重新注册节点，就像刚注册节点一样。 重新注册节点之前，不需要从 Azure Automation State Configuration 中注销节点。

## <a name="troubleshooting-azure-virtual-machine-onboarding"></a>排查 Azure 虚拟机登记问题

Azure Automation State Configuration 可让你轻松加入 Azure Windows VM 以进行配置管理。 在幕后，Azure VM Desired State Configuration 扩展用于向 Azure Automation State Configuration 注册 VM。 由于 Azure VM 所需状态配置扩展以异步方式运行，因此跟踪其进度和排查其执行问题可能很重要。

> [!NOTE]
> 将 Azure Windows VM 加入到使用 Azure VM 所需状态配置扩展的 Azure 自动化状态配置的任何方法最多可能需要一小时，Azure 自动化才能将其显示为已注册。 此延迟的原因是，Azure VM 所需状态配置扩展会在 VM 上安装 WMF 5，这是将 VM 加入 Azure 自动化状态配置所必需的。

若要排查或查看 Azure VM 所需状态配置扩展的状态，请执行以下操作：

1. 在 Azure 门户中，导航到要载入的 VM。
2. 单击 "**设置**" 下的 "**扩展**"。 
3. 现在，选择**DSC**或**DSCForLinux**，具体取决于你的操作系统。 
4. 有关详细信息，可以单击“查看详细状态”****。

有关故障排除的详细信息，请参阅[排查 Azure 自动化 Desired State Configuration (DSC) 问题](./troubleshoot/desired-state-configuration.md)。

## <a name="next-steps"></a>后续步骤

- 若要开始，请参阅[Azure 自动化状态配置](automation-dsc-getting-started.md)入门。
- 若要了解如何编译 DSC 配置，以便可以将其分配给目标节点，请参阅[在 Azure 自动化状态配置中编译配置](automation-dsc-compile.md)。
- 有关 PowerShell cmdlet 参考，请参阅 [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
)。
- 有关定价信息，请参阅[Azure 自动化状态配置定价](https://azure.microsoft.com/pricing/details/automation/)。
- 有关在持续部署管道中使用 Azure 自动化状态配置的示例，请参阅使用[示例：使用 Azure 自动化状态配置将持续部署到虚拟机和 Chocolatey](automation-dsc-cd-chocolatey.md)。
