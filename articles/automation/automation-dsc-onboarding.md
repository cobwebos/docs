---
title: 加入 Azure Automation State Configuration 管理的计算机
description: 如何设置通过 Azure Automation State Configuration 进行管理的计算机
services: automation
ms.service: automation
ms.component: dsc
author: DCtheGeek
ms.author: dacoulte
ms.topic: conceptual
ms.date: 08/08/2018
manager: carmonm
ms.openlocfilehash: 0c34950a9a851fdfd7fdd71d8bd1fe07c02b61aa
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2018
ms.locfileid: "40004338"
---
# <a name="onboarding-machines-for-management-by-azure-automation-state-configuration"></a>加入 Azure Automation State Configuration 管理的计算机

## <a name="why-manage-machines-with-azure-automation-state-configuration"></a>为何要使用 Azure 自动化状态配置来管理计算机？

与 [PowerShell Desired State Configuration ](/powershell/dsc/overview)一样，Azure Automation State Configuration 在任何云或本地数据中心都是一个简单但又强大的 DSC 节点（物理机和虚拟机）配置管理服务。 它可让你从中心的安全位置快速轻松地扩展到数千台计算机。 可以轻松登记计算机、为其分配声明性配置并查看显示每台计算机是否符合指定的所需状态的报告。 Azure Automation State Configuration 管理层的 DSC 如同 Azure 自动化管理层的 PowerShell 脚本。 换句话说，Azure 自动化以帮助你管理 PowerShell 脚本的相同方式帮助你管理 DSC 配置。 若要详细了解使用 Azure Automation State Configuration 的好处，请参阅 [Azure Automation State Configuration 概述](automation-dsc-overview.md)。

Azure Automation State Configuration 可用于管理各种不同的计算机：

- Azure 虚拟机（在经典和 Azure 资源管理器部署模型中部署）
- Amazon Web Services (AWS) EC2 实例
- 位于本地或 Azure 以外的云中的物理/虚拟 Windows 计算机/AWS
- 位于本地、Azure 或 Azure 以外的云中的物理/虚拟 Linux 计算机

此外，如果未准备好从云管理计算机配置，Azure Automation State Configuration 也可用作仅限报告的终结点。 这样，便可以通过 DSC 本地设置（推送）所需配置，以及查看符合 Azure 自动化中的所需状态匹配节点的丰富报告详细信息。

> [!NOTE]
> 如果安装了高于 2.70 版的虚拟机 DSC 扩展，则包含使用 State Configuration 管理 Azure VM 的功能而无需额外付费。 有关更多详细信息，请参阅[**自动化定价页**](https://azure.microsoft.com/pricing/details/automation/)。

以下部分概述了如何将每种类型的计算机加入 Azure Automation State Configuration。

## <a name="azure-virtual-machines-classic"></a>Azure 虚拟机（经典）

利用 Azure Automation State Configuration 可以轻松加入 Azure 虚拟机（经典），以使用 Azure 门户或 PowerShell 进行配置管理。 在幕后，在不需要管理员远程连接到 VM 的情况下，Azure VM Desired State Configuration 扩展会在 Azure Automation State Configuration 中注册 VM。 因为 Azure VM 所需状态配置扩展以异步方式运行，可在以下[**排查 Azure 虚拟机登记问题**](#troubleshooting-azure-virtual-machine-onboarding)部分了解跟踪注册进度或故障排除的步骤。

### <a name="azure-portal"></a>Azure 门户

在 [Azure 门户](http://portal.azure.com/)中，单击“浏览” -> “虚拟机(经典)”。 选择要登记的 Windows VM。 在虚拟机的仪表板边栏选项卡上，单击“所有设置” -> “扩展” -> “添加” -> “Azure 自动化 DSC” -> “创建”。
输入用例所需的“[PowerShell DSC 本地配置管理器值](/powershell/dsc/metaconfig4)”、自动化帐户的注册密钥和注册 URL，并选择性地输入要分配给 VM 的节点配置。

![DSC 的 Azure VM 扩展](./media/automation-dsc-onboarding/DSC_Onboarding_1.png)

若要查找计算机所要登记到的自动化帐户的注册 URL 与密钥，请参阅以下[**安全注册**](#secure-registration)部分：

### <a name="powershell"></a>PowerShell

```powershell
# log in to both Azure Service Management and Azure Resource Manager
Add-AzureAccount
Connect-AzureRmAccount

# fill in correct values for your VM/Automation account here
$VMName = ''
$ServiceName = ''
$AutomationAccountName = ''
$AutomationAccountResourceGroup = ''

# fill in the name of a Node Configuration in Azure Automation State Configuration, for this VM to conform to
# NOTE: DSC Node Configuration names are case sensitive in the portal.
$NodeConfigName = ''

# get Azure Automation State Configuration registration info
$Account = Get-AzureRmAutomationAccount -ResourceGroupName $AutomationAccountResourceGroup -Name $AutomationAccountName
$RegistrationInfo = $Account | Get-AzureRmAutomationRegistrationInfo

# use the DSC extension to onboard the VM for management with Azure Automation State Configuration
$VM = Get-AzureVM -Name $VMName -ServiceName $ServiceName

$PublicConfiguration = ConvertTo-Json -Depth 8 @{
    SasToken = ''
    ModulesUrl = 'https://eus2oaasibizamarketprod1.blob.core.windows.net/automationdscpreview/RegistrationMetaConfigV2.zip'
    ConfigurationFunction = 'RegistrationMetaConfigV2.ps1\RegistrationMetaConfigV2'

# update these PowerShell DSC Local Configuration Manager defaults if they do not match your use case.
# See https://docs.microsoft.com/powershell/dsc/metaConfig for more details
    Properties = @{
        RegistrationKey = @{
            UserName = 'notused'
            Password = 'PrivateSettingsRef:RegistrationKey'
        }
        RegistrationUrl = $RegistrationInfo.Endpoint
        NodeConfigurationName = $NodeConfigName
        ConfigurationMode = 'ApplyAndMonitor'
        ConfigurationModeFrequencyMins = 15
        RefreshFrequencyMins = 30
        RebootNodeIfNeeded = $False
        ActionAfterReboot = 'ContinueConfiguration'
        AllowModuleOverwrite = $False
    }
}

$PrivateConfiguration = ConvertTo-Json -Depth 8 @{
    Items = @{
        RegistrationKey = $RegistrationInfo.PrimaryKey
    }
}

$VM = Set-AzureVMExtension `
    -VM $vm `
    -Publisher Microsoft.Powershell `
    -ExtensionName DSC `
    -Version 2.76 `
    -PublicConfiguration $PublicConfiguration `
    -PrivateConfiguration $PrivateConfiguration `
    -ForceUpdate

$VM | Update-AzureVM
```

> [!NOTE]
> State Configuration 节点配置名称在门户中区分大小写。 如果大小写不匹配，节点将不会显示在“节点”选项卡下。

## <a name="azure-virtual-machines"></a>Azure 虚拟机

Azure Automation State Configuration 可让你使用 Azure 门户、Azure 资源管理器模板或 PowerShell 轻松加入 Azure 虚拟机以进行配置管理。 在幕后，在不需要管理员远程连接到 VM 的情况下，Azure VM Desired State Configuration 扩展会在 Azure Automation State Configuration 中注册 VM。
因为 Azure VM 所需状态配置扩展以异步方式运行，可在以下[**排查 Azure 虚拟机登记问题**](#troubleshooting-azure-virtual-machine-onboarding)部分了解跟踪注册进度或故障排除的步骤。

### <a name="azure-portal"></a>Azure 门户

在 [Azure 门户](https://portal.azure.com/)中，导航到要在其中登记虚拟机的 Azure 自动化帐户。 在 State Configuration 页上的“节点”选项卡中，单击“+ 添加”。

选择要登记的 Azure 虚拟机。

如果虚拟机未安装 PowerShell 所需状态扩展且电源状态为“正在运行”，请单击“连接”。

在“注册”下，输入用例所需的[“PowerShell DSC 本地配置管理器”值](/powershell/dsc/metaconfig4)，并选择性地输入要分配给 VM 的节点配置。

![](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="azure-resource-manager-templates"></a>Azure 资源管理器模板

可以通过 Azure 资源管理器模板部署 Azure 虚拟机和加入到 Azure Automation State Configuration。 有关将现有 VM 加入 Azure Automation State Configuration 的示例模板，请参阅[通过 DSC 扩展和 Azure Automation DSC 配置 VM](https://azure.microsoft.com/documentation/templates/dsc-extension-azure-automation-pullserver/)。 若要查找作为此模板的输入的注册密钥和注册 URL，请参阅以下[**安全注册**](#secure-registration)部分。

### <a name="powershell"></a>PowerShell

可通过 PowerShell 使用 [Register-AzureRmAutomationDscNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode) cmdlet 在 Azure 门户中登记虚拟机。

## <a name="amazon-web-services-aws-virtual-machines"></a>Amazon Web Services (AWS) 虚拟机

可以轻松地加入 Amazon Web Services 虚拟机，以便通过 Azure Automation State Configuration 使用 AWS DSC Toolkit 进行配置管理。 可在[此处](https://blogs.msdn.microsoft.com/powershell/2016/04/20/aws-dsc-toolkit/)了解更多有关此工具包的信息。

## <a name="physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azureaws"></a>位于本地或 Azure 以外的云中的物理/虚拟 Windows 计算机/AWS

还可以通过几个简单的步骤，将本地 Windows 计算机和非 Azure 云中的 Windows 计算机（例如 Amazon Web 服务）加入 Azure Automation State Configuration，前提是这些计算机可对 Internet 进行出站访问：

1. 确保已在要加入到 Azure Automation State Configuration 的计算机上安装最新版本的 [WMF 5](http://aka.ms/wmf5latest)。
1. 请根据以下[**生成 DSC 元配置**](#generating-dsc-metaconfigurations)部分中的说明生成包含所需 DSC 元配置的文件夹。
1. 从远程将 PowerShell DSC 元配置应用到想要登记的计算机。 **运行此命令的计算机必须已安装最新版本的 [WMF 5](http://aka.ms/wmf5latest)**：

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. 如果无法从远程应用 PowerShell DSC 元配置，请将步骤 2 中元配置的文件夹复制到每一台要登记的计算机。 然后在每台要登记的计算机上本地调用 **Set-DscLocalConfigurationManager**。
1. 使用 Azure 门户或 cmdlet 检查要加入的计算机现在是否在 Azure 自动化帐户中显示为已注册的 State Configuration 节点。

## <a name="physicalvirtual-linux-machines-on-premises-in-azure-or-in-a-cloud-other-than-azure"></a>位于本地、Azure 或 Azure 以外的云中的物理/虚拟 Linux 计算机

还可以通过几个简单的步骤，将本地 Linux 计算机和非 Azure 云中的 Linux 计算机加入 Azure Automation State Configuration，前提是这些计算机可对 Internet 进行出站访问：

1. 确保已在希望加入到 Azure Automation State Configuration 的计算机上安装[适用于 Linux 的 PowerShell Desired State Configuration](https://github.com/Microsoft/PowerShell-DSC-for-Linux) 的最新版本。
1. 如果 [PowerShell DSC 本地 Configuration Manager 默认值](/powershell/dsc/metaconfig4)与用例匹配，并且想要将计算机加入为**同时**从 Azure Automation State Configuration 提取并报告信息，则请执行以下操作：

   - 在要加入到 Azure Automation State Configuration 的每台 Linux 计算机上，使用 `Register.py` 通过 PowerShell DSC 本地 Configuration Manager 默认值进行加入：

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   - 若要查找自动化帐户的注册密钥和注册 URL，请参阅以下[**安全注册**](#secure-registration)部分。

     如果 PowerShell DSC Configuration Manager 默认值与用例**不**匹配，或者希望将计算机加入为只向 Azure Automation State Configuration 报告但不提取配置或 PowerShell 模块，请遵循步骤 3 - 6。 否则，请直接跳到步骤 6。

1. 请根据以下[**生成 DSC 元配置**](#generating-dsc-metaconfigurations)部分中的说明生成包含所需 DSC 元配置的文件夹。
1. 从远程将 PowerShell DSC 元配置应用到想要登记的计算机：

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

运行此命令的计算机必须已安装最新版本的 [WMF 5](http://aka.ms/wmf5latest)。

1. 如果无法从远程应用 PowerShell DSC 元配置，请针对要登记的每台 Linux 计算机，将步骤 5 所述的文件夹中对应于该计算机的元配置复制到 Linux 计算机。 然后，在每台要加入到 Azure Automation State Configuration 的 Linux 计算机上本地调用 `SetDscLocalConfigurationManager.py`：

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

1. 使用 Azure 门户或 cmdlet 检查要登记的计算机现在是否在 Azure 自动化帐户中显示为已注册的 DSC 节点。

## <a name="generating-dsc-metaconfigurations"></a>生成 DSC 元配置

要以一般方式将任何计算机加入 Azure Automation State Configuration，可以生成应用时告知计算机上的 DSC 代理从 Azure Automation State Configuration 提取和/或报告的 [DSC 元配置](/powershell/dsc/metaconfig)。 Azure Automation State Configuration 的 DSC 元配置可以使用 PowerShell DSC 配置或 Azure 自动化 PowerShell cmdlet 来生成。

> [!NOTE]
> DSC 元配置包含将计算机登记到进行管理的自动化帐户的机密。 请务必适当保护所创建的任何 DSC 元配置，或者在使用后将其删除。

### <a name="using-a-dsc-configuration"></a>使用 DSC 配置

1. 在本地环境中，以计算机管理员身份打开 VSCode（或偏好的编辑器）。 计算机上必须已安装最新版本的 [WMF 5](http://aka.ms/wmf5latest)。
1. 在本地复制以下脚本。 此脚本包含用于创建元配置的 PowerShell DSC 配置，以及用于开始执行元配置创建操作的命令。

> [!NOTE]
> State Configuration 节点配置名称在门户中区分大小写。 如果大小写不匹配，节点将不会显示在“节点”选项卡下。

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

1. 填写自动化帐户的注册密钥和 URL，以及要登记的计算机名称。 所有其他参数都是可选的。 若要查找自动化帐户的注册密钥和注册 URL，请参阅以下[**安全注册**](#secure-registration)部分。
1. 如果希望计算机向 Azure Automation State Configuration 报告 DSC 状态信息但不提取配置或 PowerShell 模块，请将 **ReportOnly** 参数设置为 true。
1. 运行该脚本。 现在，工作目录中应有一个名为 **DscMetaConfigs** 的文件夹，其中包含要登记的计算机的 PowerShell DSC 元配置（作为管理员）：

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="using-the-azure-automation-cmdlets"></a>使用 Azure 自动化 cmdlet

如果 PowerShell DSC 本地 Configuration Manager 默认值与用例匹配，并且你想要将计算机加入为同时从 Azure Automation State Configuration 提取并报告信息，Azure 自动化 cmdlet 可提供一种简单的方法来生成所需的 DSC 元配置：

1. 在本地环境中，以计算机管理员身份打开 PowerShell 控制台或 VSCode。
2. 使用 `Connect-AzureRmAccount` 连接到 Azure 资源管理器
3. 从要登记节点的目标自动化帐户下载想要登记的计算机的 PowerShell DSC 元配置：

   ```powershell
   # Define the parameters for Get-AzureRmAutomationDscOnboardingMetaconfig using PowerShell Splatting
   $Params = @{
       ResourceGroupName = 'ContosoResources'; # The name of the Resource Group that contains your Azure Automation Account
       AutomationAccountName = 'ContosoAutomation'; # The name of the Azure Automation Account where you want a node on-boarded to
       ComputerName = @('web01', 'web02', 'sql01'); # The names of the computers that the meta configuration will be generated for
       OutputFolder = "$env:UserProfile\Desktop\";
   }
   # Use PowerShell splatting to pass parameters to the Azure Automation cmdlet being invoked
   # For more info about splatting, run: Get-Help -Name about_Splatting
   Get-AzureRmAutomationDscOnboardingMetaconfig @Params
   ```

1. 现在，应有一个名为 ***DscMetaConfigs*** 的文件夹，其中包含要登记的计算机的 PowerShell DSC 元配置（作为管理员）：

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="secure-registration"></a>安全注册

计算机可以通过 WMF 5 DSC 注册协议安全加入到 Azure 自动化帐户，这样，DSC 节点便可向 PowerShell DSC“拉”服务器或报告服务器（包括 Azure Automation State Configuration）进行身份验证。 节点将注册到位于**注册 URL** 的服务器，并使用**注册密钥**进行身份验证。 在注册期间，DSC 节点和 DSC“拉”服务器/报告服务器将协商唯一证书，以便在注册后使用此节点向服务器进行身份验证。 此过程可防止登记的节点彼此模拟，例如当节点遭到入侵并且具有恶意行为时。 注册之后，注册密钥不再用于身份验证，而是从节点中删除。

可以从 Azure 门户中“帐户设置”下的“密钥”中获取 State Configuration 注册协议所需的信息。 在自动化帐户的“基本信息”面板中单击密钥图标即可打开此边栏选项卡。

![Azure 自动化密钥和 URL](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- “注册 URL”是“管理密钥”边栏选项卡中的“URL”字段。
- “注册密钥”是“管理密钥”边栏选项卡中的主访问密钥或辅助访问密钥。 可以使用其中的任一密钥。

为了提高安全性，可随时重新生成自动化帐户的主密钥和辅助密钥（在“管理密钥”页上），以避免将来的节点注册使用以前的密钥。

## <a name="troubleshooting-azure-virtual-machine-onboarding"></a>排查 Azure 虚拟机登记问题

Azure Automation State Configuration 可让你轻松加入 Azure Windows VM 以进行配置管理。 在幕后，Azure VM Desired State Configuration 扩展用于向 Azure Automation State Configuration 注册 VM。 由于 Azure VM 所需状态配置扩展以异步方式运行，跟踪其进度和排查其执行问题可能很重要。

> [!NOTE]
> 将 Azure Windows VM 加入到使用 Azure VM Desired State Configuration 扩展的 Azure Automation State Configuration 的任何方法最多可能需要一小时，节点才显示为已在 Azure 自动化中注册。 Azure VM DSC 扩展会在 VM 上安装 Windows Management Framework 5.0，以便将 VM 加入到 Azure Automation State Configuration。

若要查看 Azure VM 所需状态配置扩展的状态或对其进行故障排除，请在 Azure 门户中，导航到正在登记的 VM，并单击“设置”下的“扩展”。 然后单击 **DSC** 或 **DSCForLinux**，具体取决于操作系统。 有关详细信息，可以单击“查看详细状态”。

## <a name="certificate-expiration-and-reregistration"></a>证书过期和重新注册

在将计算机注册为 Azure Automation State Configuration 中的 DSC 节点之后，有多种原因可能使需要在将来重新注册该节点：

- 在注册之后，每个节点会自动协商唯一的身份验证证书，该证书于一年之后过期。 目前，当证书即将过期时，PowerShell DSC 注册协议无法自动续订证书，因此需要在一年之后重新注册这些节点。 在重新注册之前，请确保每个节点正在运行 Windows Management Framework 5.0 RTM。 如果节点的身份验证证书过期并且该节点尚未注册，则该节点将无法与 Azure 自动化通信，并将标记为“无响应”。 与证书过期时间相距 90 天或更短时间内执行的注册，或者在证书过期时间之后任何时间点执行的注册，将生成新的证书并提供使用。
- 更改在节点初始注册期间设置的任何 [PowerShell DSC 本地配置管理器值](/powershell/dsc/metaconfig4)，例如 ConfigurationMode。 目前，只可以通过重新注册更改这些 DSC 代理值。 一种例外情况是分配给节点的节点配置 - 可以在 Azure 自动化 DSC 中直接更改。

可以使用本文档中所述的任何登记方法，按照初始注册节点的相同方法执行重新注册。 重新注册节点之前，不需要从 Azure Automation State Configuration 中注销节点。

## <a name="next-steps"></a>后续步骤

- 有关入门信息，请参阅 [Azure Automation State Configuration 入门](automation-dsc-getting-started.md)
- 若要了解如何编译 DSC 配置，以便将它们分配给目标节点，请参阅[在 Azure Automation State Configuration 中编译配置](automation-dsc-compile.md)
- 有关 PowerShell cmdlet 参考，请参阅 [Azure Automation State Configuration cmdlet](/powershell/module/azurerm.automation/#automation)
- 有关定价信息，请参阅 [Azure Automation State Configuration 定价](https://azure.microsoft.com/pricing/details/automation/)
- 若要查看在持续部署管道中使用 Azure Automation State Configuration 的示例，请参阅[使用 Azure Automation State Configuration 和 Chocolatey 进行持续部署](automation-dsc-cd-chocolatey.md)