---
title: "配置 Azure 运行方式帐户 | Microsoft Docs"
description: "本教程引导你在 Azure 自动化中创建、测试安全主体，并示范如何使用安全主体进行身份验证。"
services: automation
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: 
keywords: "服务主体名称, setspn, azure 身份验证"
ms.assetid: 2f783441-15c7-4ea0-ba27-d7daa39b1dd3
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/27/2017
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: fbca3d195290551d37606e231b997a40a602351f
ms.lasthandoff: 03/29/2017


---

# <a name="authenticate-runbooks-with-an-azure-run-as-account"></a>使用 Azure 运行方式帐户进行 Runbook 身份验证
本文介绍如何在 Azure 门户中配置 Azure 自动化帐户。 为此，可以使用运行方式帐户功能配置自动化帐户，对在 Azure Resource Manager 或 Azure 服务管理中管理资源的 Runbook 进行身份验证。

在 Azure 门户中创建自动化帐户时，会自动创建两个帐户：

* 运行方式帐户。 此帐户在 Azure Active Directory (Azure AD) 中创建一个服务主体，并创建一个证书。 此外，它还向参与者分配基于角色的访问控制 (RBAC)，以便使用 Runbook 管理 Resource Manager 资源。
* 经典运行方式帐户。 此帐户上载一个管理证书用于通过 Runbook 来管理服务管理资源或经典资源。

创建自动化帐户可以简化操作过程，帮助你快速开始构建和部署 Runbook 来支持自动化需求。

使用运行方式帐户和经典运行方式帐户时，可以：

* 在 Azure 门户中通过 Runbook 管理 Resource Manager 或服务管理资源时，提供标准化 Azure 身份验证方式。
* 自动使用在 Azure 警报中配置的全局 Runbook。

> [!NOTE]
> 使用自动化全局 Runbook 的 Azure [警报集成功能](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)需要已配置了运行方式帐户和经典运行方式帐户的自动化帐户。 可以选择包含定义的运行方式帐户和经典运行方式帐户的自动化帐户，也可以选择创建新的自动化帐户。
>  

本文介绍如何从 Azure 门户创建自动化帐户、如何使用 Azure PowerShell 更新自动化帐户、如何管理帐户配置，以及如何在 Runbook 中进行身份验证。

在开始创建自动化帐户之前，最好是了解并考虑以下事项：

* 创建自动化帐户不会影响在经典或 Resource Manager 部署模型中已创建的自动化帐户。
* 该过程只适用于在 Azure 门户中创建的自动化帐户。 如果尝试从 Azure 经典门户创建帐户，将不会复制运行方式帐户配置。
* 如果你已创建 Runbook 和资产（例如计划、变量等）来管理经典资源，并希望这些 Runbook 使用新的经典运行方式帐户进行身份验证，请执行以下操作之一：

  * 若要创建经典运行方式帐户，请遵循“管理运行方式帐户”部分中的说明。 
  * 若要更新现有帐户，请使用“使用 PowerShell 更新自动化帐户”部分中的 PowerShell 脚本。
* 若要使用新的运行方式帐户和经典运行方式自动化帐户进行身份验证，需要使用在[身份验证代码示例](#authentication-code-examples)部分提供的示例代码修改现有的 Runbook。

    >[!NOTE]
    >运行方式帐户用于通过基于证书的服务主体对 Resource Manager 资源进行身份验证。 经典运行方式帐户用于通过管理证书对服务管理资源进行身份验证。

## <a name="create-an-automation-account-from-the-azure-portal"></a>从 Azure 门户创建自动化帐户
在本部分，你将从 Azure 门户创建一个 Azure 自动化帐户，然后使用该帐户创建运行方式帐户和经典运行方式帐户。

>[!NOTE]
>若要创建自动化帐户，你必须是“服务管理员”角色的成员，或者是授予订阅访问权限的订阅共同管理员。 此外，必须将某个用户添加到该订阅的默认 Active Directory 实例。 不需要将该帐户分配到特权角色。
>
>如果你在被添加到订阅共同管理员角色之前不是订阅 Active Directory 实例的成员，你将作为来宾添加到 Active Directory。 在此实例中，“添加自动化帐户”边栏选项卡中会显示 “你无权创建...”警告。
>
>可以先从订阅的 Active Directory 实例中删除已添加到共同管理员角色的用户，然后重新添加，使其成为 Active Directory 中的完整用户。 若要验证这种情况，可在 Azure 门户的“Azure Active Directory”窗格中选择“用户和组”，选择“所有用户”，在选择特定的用户后再选择“配置文件”。 用户配置文件下的“用户类型”属性值不应等于“来宾”。
>

1. 以订阅管理员角色成员和订阅共同管理员的帐户登录到 Azure 门户。

2. 选择“自动化帐户”。

3. 在“自动化帐户”边栏选项卡中，单击“添加”。
此时将打开“添加自动化帐户”边栏选项卡。

 ![“添加自动化帐户”边栏选项卡](media/automation-sec-configure-azure-runas-account/create-automation-account-properties-b.png)

   > [!NOTE]
   > 如果你的帐户不是订阅管理员角色成员和订阅的共同管理员，“添加自动化帐户”边栏选项卡中会显示以下警告：
   >
   >![添加自动化帐户警报](media/automation-sec-configure-azure-runas-account/create-account-without-perms.png)
   >
   >

4. 在“添加自动化帐户”边栏选项卡的“名称”框中，键入新自动化帐户的名称。

5. 如果你有多个订阅，请执行以下操作：

    a. 在“订阅”下，为新帐户指定一个订阅。

    b. 在“资源组”下，单击“新建”或“使用现有”。

    c. 在“位置”下，指定 Azure 数据中心。

6. 在“创建 Azure 运行方式帐户”下，选择“是”，然后单击“创建”。

   > [!NOTE]
   > 如果选择“否”以选择不创建运行方式帐户，“添加自动化帐户”边栏选项卡中会显示一条警告消息。 尽管该帐户是在 Azure 门户中创建的，但它在经典或 Resource Manager 订阅目录服务中没有对应的身份验证标识。 因此，该帐户无法访问订阅中的资源。 这种情况会导致引用此帐户的任何 Runbook 无法进行身份验证，也无法对这些部署模型中的资源执行任务。
   >
   > ![“添加自动化帐户”边栏选项卡中的警告消息](media/automation-sec-configure-azure-runas-account/create-account-decline-create-runas-msg.png)
   >
   > 此外，由于未创建服务主体，因此不会分配参与者角色。
   >

7. 在 Azure 创建自动化帐户时，可以在菜单的“通知”下面跟踪进度。

### <a name="resources"></a>资源
成功创建自动化帐户后，系统将自动为你创建几个资源。 以下两个表格汇总了这些资源：

#### <a name="run-as-account-resources"></a>运行方式帐户资源

| 资源 | 说明 |
| --- | --- |
| AzureAutomationTutorial Runbook | 一个示例图形 Runbook，演示如何使用运行方式帐户进行身份验证并获取所有 Resource Manager 资源。 |
| AzureAutomationTutorialScript Runbook | 一个示例 PowerShell Runbook，演示如何使用运行方式帐户进行身份验证并获取所有 Resource Manager 资源。 |
| AzureRunAsCertificate | 在创建自动化帐户时自动创建的，或针对现有帐户使用以下 PowerShell 脚本创建的证书资产。 使用该证书可向 Azure 进行身份验证，以便通过 Runbook 管理 Azure Resource Manager 资源。 该证书的有效期为一年。 |
| AzureRunAsConnection | 在创建自动化帐户时自动创建的，或针对现有帐户使用 PowerShell 脚本创建的连接资产。 |

#### <a name="classic-run-as-account-resources"></a>经典运行方式帐户资源

| 资源 | 说明 |
| --- | --- |
| AzureClassicAutomationTutorial Runbook | 一个示例图形 Runbook，它使用经典运行方式帐户（证书）获取订阅中使用经典部署模型中创建的所有 VM，然后写入 VM 名称和状态。 |
| AzureClassicAutomationTutorial 脚本 Runbook | 一个示例 PowerShell Runbook，它使用经典运行方式帐户（证书）获取订阅中的所有经典 VM，然后写入 VM 名称和状态。 |
| AzureClassicRunAsCertificate | 自动创建的证书资产，用于向 Azure 进行身份验证，以便从 Runbook 管理 Azure 经典资源。 该证书的有效期为一年。 |
| AzureClassicRunAsConnection | 自动创建的连接资产，用于向 Azure 进行身份验证，以便从 Runbook 管理 Azure 经典资源。 |

## <a name="verify-run-as-authentication"></a>验证运行方式身份验证
执行一个小测试，验证是否能够使用新的运行方式帐户成功进行身份验证。

1. 在 Azure 门户中，打开前面创建的自动化帐户。

2. 单击“Runbook”磁贴打开 Runbook 的列表。

3. 选择 **AzureAutomationTutorialScript** Runbook，然后单击“启动”以启动该 Runbook。 将发生以下事件：
 * 将创建一个 [Runbook 作业](automation-runbook-execution.md)并显示“作业”边栏选项卡，“作业摘要”磁贴中显示作业状态。
 * 作业状态最初为“已排队”，表示它正在等待云中的 Runbook 辅助角色变为可用。
 * 在某个辅助角色声明该作业后，其状态将变为“正在启动”。
 * 当 Runbook 开始运行时，其状态将变为“正在运行”。
 * Runbook 作业完成运行后，状态应显示为“已完成”。

       ![Security Principal Runbook Test](media/automation-sec-configure-azure-runas-account/job-summary-automationtutorialscript.png)
4. 若要查看 Runbook 的详细结果，请单击“输出”磁贴。  
此时将显示“输出”边栏选项卡，其中显示 Runbook 已成功完成身份验证并返回了资源组中所有可用资源的列表。

5. 关闭“输出”边栏选项卡返回“作业摘要”边栏选项卡。

6. 关闭“作业摘要”边栏选项卡和相应的 **AzureAutomationTutorialScript** Runbook 边栏选项卡。

## <a name="verify-classic-run-as-authentication"></a>验证经典运行方式身份验证
执行一个小测试，验证是否能够使用新的经典运行方式帐户成功进行身份验证。

1. 在 Azure 门户中，打开前面创建的自动化帐户。

2. 单击“Runbook”磁贴打开 Runbook 的列表。

3. 选择 **AzureClassicAutomationTutorialScript** Runbook，然后单击“启动”以启动该 Runbook。 将发生以下事件：

 * 将创建一个 [Runbook 作业](automation-runbook-execution.md)并显示“作业”边栏选项卡，“作业摘要”磁贴中显示作业状态。
 * 作业状态最初为“已排队”，表示它正在等待云中的 Runbook 辅助角色变为可用。
 * 在某个辅助角色声明该作业后，其状态将变为“正在启动”。
 * 当 Runbook 开始运行时，其状态将变为“正在运行”。
 * Runbook 作业完成运行后，状态应显示为“已完成”。

    ![安全主体 Runbook 测试](media/automation-sec-configure-azure-runas-account/job-summary-automationclassictutorialscript.png)<br>
4. 若要查看 Runbook 的详细结果，请单击“输出”磁贴。  
此时将显示“输出”边栏选项卡，其中显示 Runbook 已成功完成身份验证并返回了订阅中所有经典 VM 的列表。

5. 关闭“输出”边栏选项卡返回“作业摘要”边栏选项卡。

6. 关闭“作业摘要”边栏选项卡和相应的 **AzureAutomationTutorialScript** Runbook 边栏选项卡。

## <a name="managing-your-run-as-account"></a>管理运行方式帐户
在自动化帐户过期之前的某个时间点，你需要续订证书。 如果你认为运行方式帐户已遭到入侵，可以删除然后重新创建它。 本部分介绍如何执行这些操作。

### <a name="self-signed-certificate-renewal"></a>自签名证书续订
为运行方式帐户创建的自签名证书自创建日期算起的一年后过期。 可以在该证书过期之前的任何时间续订。 续订时，将保留当前的有效证书，确保已排队的或正在运行的且使用运行方式帐户进行身份验证的任何 Runbook 不会受到影响。 该证书在过期之前将保持有效。

> [!NOTE]
> 如果已将自动化运行方式帐户配置为使用企业证书颁发机构颁发的证书并使用此选项，该企业证书将被自签名证书替换。

若要续订证书，请执行以下操作：

1. 在 Azure 门户中，打开自动化帐户。

2. 在“自动化帐户”边栏选项卡上“帐户属性”窗格的“帐户设置”下面，选择“运行方式帐户”。

    ![自动化帐户属性窗格](media/automation-sec-configure-azure-runas-account/automation-account-properties-pane.png)
3. 在“运行方式帐户”属性边栏选项卡上，选择要为其续订证书的运行方式帐户或经典运行方式帐户。

4. 在所选帐户的“属性”边栏选项卡上，单击“续订证书”。

    ![续订运行方式帐户的证书](media/automation-sec-configure-azure-runas-account/automation-account-renew-runas-certificate.png)

5. 证书续订过程中，可以在菜单的“通知”下面跟踪进度。

### <a name="delete-a-run-as-or-classic-run-as-account"></a>删除运行方式帐户或经典运行方式帐户
本部分介绍如何删除并重新创建运行方式帐户或经典运行方式帐户。 执行此操作时，将保留自动化帐户。 删除运行方式帐户或经典运行方式帐户后，可在 Azure 门户中重新创建它。

1. 在 Azure 门户中，打开自动化帐户。

2. 在“自动化帐户”边栏选项卡上的帐户属性窗格中，选择“运行方式帐户”。

3. 在“运行方式帐户”属性边栏选项卡上，选择要删除的运行方式帐户或经典运行方式帐户。 然后，在所选帐户的“属性”边栏选项卡上，单击“删除”。

 ![删除运行方式帐户](media/automation-sec-configure-azure-runas-account/automation-account-delete-runas.png)

4. 帐户删除过程中，可以在菜单的“通知”下面跟踪进度。

5. 删除该帐户后，可以通过在“运行方式帐户”属性边栏选项卡中选择创建选项“Azure 运行方式帐户”来重新创建该帐户。

 ![重新创建自动化运行方式帐户](media/automation-sec-configure-azure-runas-account/automation-account-create-runas.png)

### <a name="misconfiguration"></a>配置错误
在初始设置期间，运行方式帐户或经典运行方式帐户所需的某些配置项可能已被删除或未正确创建。 这些项包括：

* 证书资产
* 连接资产
* 运行方式帐户已从参与者角色中删除
* Azure AD 中的服务主体或应用程序

在上述和其他错误配置的实例中，自动化将检测更改，并在该帐户的“运行方式帐户”属性边栏选项卡中显示“不完整”状态。

![不完整的运行方式帐户配置状态](media/automation-sec-configure-azure-runas-account/automation-account-runas-incomplete-config.png)

选择该运行方式帐户时，该帐户的“属性”窗格中会显示以下错误消息：

![不完整的运行方式配置警告消息](media/automation-sec-configure-azure-runas-account/automation-account-runas-incomplete-config-msg.png)。

可通过删除并重新创建运行方式帐户来快速解决该帐户的问题。

## <a name="update-your-automation-account-by-using-powershell"></a>使用 PowerShell 更新自动化帐户
在以下情况下，可以使用 PowerShell 来更新现有的自动化帐户：

* 你创建了自动化帐户，但不想要创建运行方式帐户。
* 已使用一个自动化帐户来管理 Resource Manager 资源，并且想要更新该帐户，以包含可用于 Runbook 身份验证的运行方式帐户
* 已使用一个自动化帐户来管理经典资源，并且想要对它进行更新以使用经典运行方式帐户，而不是创建新的帐户并将 Runbook 和资产迁移到该帐户。   
* 你想要使用企业证书颁发机构 (CA) 颁发的证书创建运行方式帐户和经典运行方式帐户。

该脚本要求满足以下先决条件：

* 该脚本只能在装有 Azure Resource Manager 模块 2.01 和更高版本的 Windows 10 与 Windows Server 2016 上运行。 在 Windows 的早期版本中不受支持。
* Azure PowerShell 1.0 和更高版本。 有关 PowerShell 1.0 版本的信息，请参阅[如何安装和配置 Azure PowerShell](/powershell/azureps-cmdlets-docs)。
* 在以下 PowerShell 脚本中作为 *–AutomationAccountName* 和 *-ApplicationDisplayName* 参数值引用的自动化帐户。

若要获取 *SubscriptionID*、*ResourceGroup* 和 *AutomationAccountName* 的值（脚本的必需参数），请执行以下操作：
1. 在 Azure 门户上的“自动化帐户”边栏选项卡中选择你的自动化帐户，然后选择“所有设置”。 
2. 在“所有设置”边栏选项卡中，选择“帐户设置”下面的“属性”。 
3. 请记下“属性”边栏选项卡中的值。

 ![自动化帐户的“属性”边栏选项卡](media/automation-sec-configure-azure-runas-account/automation-account-properties.png)  

### <a name="create-a-run-as-account-powershell-script"></a>创建运行方式帐户 PowerShell 脚本
此 PowerShell 脚本包括对以下配置的支持：

* 使用自签名证书创建运行方式帐户。
* 使用自签名证书创建运行方式帐户和经典运行方式帐户。
* 使用企业证书创建运行方式帐户和经典运行方式帐户。
* 在 Azure 政府版云中使用自签名证书创建运行方式帐户和经典运行方式帐户。

根据选择的配置选项，该脚本将创建以下项。

**对于运行方式帐户：**

* 将创建一个要使用自签名证书或企业证书公钥导出的 Azure AD 应用程序，在 Azure AD 中为此应用程序创建服务主体帐户，并在当前订阅中为此帐户分配“参与者”角色。 可将此项设置更改为“所有者”或其他任何角色。 有关详细信息，请参阅 [Azure 自动化中基于角色的访问控制](automation-role-based-access-control.md)。
* 在指定的自动化帐户中创建名为 *AzureRunAsCertificate* 的自动化证书资产。 该证书资产保存 Azure AD 应用程序使用的证书私钥。
* 在指定的自动化帐户中创建名为 *AzureRunAsConnection* 的自动化连接资产。 该连接资产保存 applicationId、tenantId、subscriptionId 和证书指纹。

**对于经典运行方式帐户：**

* 在指定的自动化帐户中创建名为 *AzureClassicRunAsCertificate* 的自动化证书资产。 该证书资产保存管理证书使用的证书私钥。
* 在指定的自动化帐户中创建名为 *AzureClassicRunAsConnection* 的自动化连接资产。 该连接资产保存订阅名称、subscriptionId 和证书资产名称。

>[!NOTE]
> 如果选择任一选项来创建经典运行方式帐户，则在执行脚本后，请将公共证书（.cer 文件扩展名）上载到创建自动化帐户的订阅的管理存储中。
> 

若要执行脚本并上载证书，请执行以下操作：

1. 将以下脚本保存到计算机。 在本示例中，请使用文件名 *New-RunAsAccount.ps1* 保存。

        #Requires -RunAsAdministrator
         Param (
        [Parameter(Mandatory=$true)]
        [String] $ResourceGroup,

        [Parameter(Mandatory=$true)]
        [String] $AutomationAccountName,

        [Parameter(Mandatory=$true)]
        [String] $ApplicationDisplayName,

        [Parameter(Mandatory=$true)]
        [String] $SubscriptionId,

        [Parameter(Mandatory=$true)]
        [Boolean] $CreateClassicRunAsAccount,

        [Parameter(Mandatory=$true)]
        [String] $SelfSignedCertPlainPassword,

        [Parameter(Mandatory=$false)]
        [String] $EnterpriseCertPathForRunAsAccount,

        [Parameter(Mandatory=$false)]
        [String] $EnterpriseCertPlainPasswordForRunAsAccount,

        [Parameter(Mandatory=$false)]
        [String] $EnterpriseCertPathForClassicRunAsAccount,

        [Parameter(Mandatory=$false)]
        [String] $EnterpriseCertPlainPasswordForClassicRunAsAccount,

        [Parameter(Mandatory=$false)]
        [ValidateSet("AzureCloud","AzureUSGovernment")]
        [string]$EnvironmentName="AzureCloud",

        [Parameter(Mandatory=$false)]
        [int] $SelfSignedCertNoOfMonthsUntilExpired = 12
        )

        function CreateSelfSignedCertificate([string] $keyVaultName, [string] $certificateName, [string] $selfSignedCertPlainPassword,
                                      [string] $certPath, [string] $certPathCer, [string] $selfSignedCertNoOfMonthsUntilExpired ) {
        $Cert = New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation cert:\LocalMachine\My `
           -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" `
           -NotAfter (Get-Date).AddMonths($selfSignedCertNoOfMonthsUntilExpired)

        $CertPassword = ConvertTo-SecureString $selfSignedCertPlainPassword -AsPlainText -Force
        Export-PfxCertificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPath -Password $CertPassword -Force | Write-Verbose
        Export-Certificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPathCer -Type CERT | Write-Verbose
        }

        function CreateServicePrincipal([System.Security.Cryptography.X509Certificates.X509Certificate2] $PfxCert, [string] $applicationDisplayName) {  
        $CurrentDate = Get-Date
        $keyValue = [System.Convert]::ToBase64String($PfxCert.GetRawCertData())
        $KeyId = (New-Guid).Guid

        $KeyCredential = New-Object  Microsoft.Azure.Commands.Resources.Models.ActiveDirectory.PSADKeyCredential
        $KeyCredential.StartDate = $CurrentDate
        $KeyCredential.EndDate= [DateTime]$PfxCert.GetExpirationDateString()
        $KeyCredential.KeyId = $KeyId
        $KeyCredential.CertValue  = $keyValue

        # Use key credentials and create an Azure AD application
        $Application = New-AzureRmADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $applicationDisplayName) -IdentifierUris ("http://" + $KeyId) -KeyCredentials $KeyCredential
        $ServicePrincipal = New-AzureRMADServicePrincipal -ApplicationId $Application.ApplicationId
        $GetServicePrincipal = Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id

        # Sleep here for a few seconds to allow the service principal application to become active (ordinarily takes a few seconds)
        Sleep -s 15
        $NewRole = New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
        $Retries = 0;
        While ($NewRole -eq $null -and $Retries -le 6)
        {
           Sleep -s 10
           New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
           $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
           $Retries++;
        }
           return $Application.ApplicationId.ToString();
        }

        function CreateAutomationCertificateAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $certifcateAssetName,[string] $certPath, [string] $certPlainPassword, [Boolean] $Exportable) {
        $CertPassword = ConvertTo-SecureString $certPlainPassword -AsPlainText -Force   
        Remove-AzureRmAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $certifcateAssetName -ErrorAction SilentlyContinue
        New-AzureRmAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Path $certPath -Name $certifcateAssetName -Password $CertPassword -Exportable:$Exportable  | write-verbose
        }

        function CreateAutomationConnectionAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $connectionAssetName, [string] $connectionTypeName, [System.Collections.Hashtable] $connectionFieldValues ) {
        Remove-AzureRmAutomationConnection -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -Force -ErrorAction SilentlyContinue
        New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -ConnectionTypeName $connectionTypeName -ConnectionFieldValues $connectionFieldValues
        }

        Import-Module AzureRM.Profile
        Import-Module AzureRM.Resources

        $AzureRMProfileVersion= (Get-Module AzureRM.Profile).Version
        if (!(($AzureRMProfileVersion.Major -ge 2 -and $AzureRMProfileVersion.Minor -ge 1) -or ($AzureRMProfileVersion.Major -gt 2)))
        {
           Write-Error -Message "Please install the latest Azure PowerShell and retry. Relevant doc url : https://docs.microsoft.com/powershell/azureps-cmdlets-docs/ "
           return
        }

        Login-AzureRmAccount -EnvironmentName $EnvironmentName
        $Subscription = Select-AzureRmSubscription -SubscriptionId $SubscriptionId

        # Create a Run As account by using a service principal
        $CertifcateAssetName = "AzureRunAsCertificate"
        $ConnectionAssetName = "AzureRunAsConnection"
        $ConnectionTypeName = "AzureServicePrincipal"

        if ($EnterpriseCertPathForRunAsAccount -and $EnterpriseCertPlainPasswordForRunAsAccount) {
        $PfxCertPathForRunAsAccount = $EnterpriseCertPathForRunAsAccount
        $PfxCertPlainPasswordForRunAsAccount = $EnterpriseCertPlainPasswordForRunAsAccount
        } else {
          $CertificateName = $AutomationAccountName+$CertifcateAssetName
          $PfxCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".pfx")
          $PfxCertPlainPasswordForRunAsAccount = $SelfSignedCertPlainPassword
          $CerCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".cer")
          CreateSelfSignedCertificate $KeyVaultName $CertificateName $PfxCertPlainPasswordForRunAsAccount $PfxCertPathForRunAsAccount $CerCertPathForRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
        }

        # Create a service principal
        $PfxCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($PfxCertPathForRunAsAccount, $PfxCertPlainPasswordForRunAsAccount)
        $ApplicationId=CreateServicePrincipal $PfxCert $ApplicationDisplayName

        # Create the Automation certificate asset
        CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $CertifcateAssetName $PfxCertPathForRunAsAccount $PfxCertPlainPasswordForRunAsAccount $true

        # Populate the ConnectionFieldValues
        $SubscriptionInfo = Get-AzureRmSubscription -SubscriptionId $SubscriptionId
        $TenantID = $SubscriptionInfo | Select TenantId -First 1
        $Thumbprint = $PfxCert.Thumbprint
        $ConnectionFieldValues = @{"ApplicationId" = $ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Thumbprint; "SubscriptionId" = $SubscriptionId}


        # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
        CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ConnectionAssetName $ConnectionTypeName $ConnectionFieldValues

        if ($CreateClassicRunAsAccount) {
            # Create a Run As account by using a service principal
            $ClassicRunAsAccountCertifcateAssetName = "AzureClassicRunAsCertificate"
            $ClassicRunAsAccountConnectionAssetName = "AzureClassicRunAsConnection"
            $ClassicRunAsAccountConnectionTypeName = "AzureClassicCertificate "
            $UploadMessage = "Please upload the .cer format of #CERT# to the Management store by following the steps below." + [Environment]::NewLine +
                    "Log in to the Microsoft Azure Management portal (https://manage.windowsazure.com) and select Settings -> Management Certificates." + [Environment]::NewLine +
                    "Then click Upload and upload the .cer format of #CERT#"

             if ($EnterpriseCertPathForClassicRunAsAccount -and $EnterpriseCertPlainPasswordForClassicRunAsAccount ) {
             $PfxCertPathForClassicRunAsAccount = $EnterpriseCertPathForClassicRunAsAccount
             $PfxCertPlainPasswordForClassicRunAsAccount = $EnterpriseCertPlainPasswordForClassicRunAsAccount
             $UploadMessage = $UploadMessage.Replace("#CERT#", $PfxCertPathForClassicRunAsAccount)
        } else {
             $ClassicRunAsAccountCertificateName = $AutomationAccountName+$ClassicRunAsAccountCertifcateAssetName
             $PfxCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".pfx")
             $PfxCertPlainPasswordForClassicRunAsAccount = $SelfSignedCertPlainPassword
             $CerCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".cer")
             $UploadMessage = $UploadMessage.Replace("#CERT#", $CerCertPathForClassicRunAsAccount)
             CreateSelfSignedCertificate $KeyVaultName $ClassicRunAsAccountCertificateName $PfxCertPlainPasswordForClassicRunAsAccount $PfxCertPathForClassicRunAsAccount $CerCertPathForClassicRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
        }

        # Create the Automation certificate asset
        CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountCertifcateAssetName $PfxCertPathForClassicRunAsAccount $PfxCertPlainPasswordForClassicRunAsAccount $false

        # Populate the ConnectionFieldValues
        $SubscriptionName = $subscription.Subscription.SubscriptionName
        $ClassicRunAsAccountConnectionFieldValues = @{"SubscriptionName" = $SubscriptionName; "SubscriptionId" = $SubscriptionId; "CertificateAssetName" = $ClassicRunAsAccountCertifcateAssetName}

        # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
        CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountConnectionAssetName $ClassicRunAsAccountConnectionTypeName $ClassicRunAsAccountConnectionFieldValues

        Write-Host -ForegroundColor red $UploadMessage
        }

2. 在计算机上单击“开始”，然后使用提升的用户权限启动 **Windows PowerShell**。

3. 在提升权限的 PowerShell 命令行外壳中，转到包含步骤 1 所创建脚本的文件夹。

4. 使用所需配置的参数值执行该脚本。

    **使用自签名证书创建运行方式帐户**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false`

    **使用自签名证书创建运行方式帐户和经典运行方式帐户**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true`

    **使用企业证书创建运行方式帐户和经典运行方式帐户**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>`

    **在 Azure 政府版云中使用自签名证书创建运行方式帐户和经典运行方式帐户**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true  -EnvironmentName AzureUSGovernment`

    > [!NOTE]
    > 执行脚本后，系统将提示你在 Azure 上进行身份验证。 请以订阅管理员角色成员和订阅共同管理员的帐户登录。
    >
    >

成功执行脚本后，请注意以下事项：
* 如果使用自签名公共证书（.cer 文件）创建了经典运行方式帐户，该脚本将创建该帐户，并将其保存到计算机上用于执行 PowerShell 会话的用户配置文件下方的临时文件夹（*%USERPROFILE%\AppData\Local\Temp*）。
* 如果使用企业公共证书（.cer 文件）创建了经典运行方式帐户，则使用此证书。 遵循有关[将管理 API 证书上载到 Azure 经典门户](../azure-api-management-certs.md)的说明，然后参考[用于通过服务管理资源进行身份验证的示例代码](#sample-code-to-authenticate-with-service-management-resources)，使用服务管理资源来验证凭据配置。 
* 如果*未*创建经典运行方式帐户，请参考[用于通过服务管理资源进行身份验证的示例代码](#sample-code-to-authenticate-with-resource-manager-resources)，使用 Resource Manager 资源进行身份验证并验证凭据配置。

## <a name="sample-code-to-authenticate-with-resource-manager-resources"></a>用于使用 Resource Manager 资源进行身份验证的示例代码
可以参考以下已更新的示例代码（摘自 *AzureAutomationTutorialScript* 示例 Runbook）使用运行方式帐户进行身份验证，以便使用 Runbook 管理 Resource Manager 资源。

    $connectionName = "AzureRunAsConnection"
    $SubId = Get-AutomationVariable -Name 'SubscriptionId'
    try
    {
       # Get the connection "AzureRunAsConnection "
       $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

       "Signing in to Azure..."
       Add-AzureRmAccount `
         -ServicePrincipal `
         -TenantId $servicePrincipalConnection.TenantId `
         -ApplicationId $servicePrincipalConnection.ApplicationId `
         -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint
       "Setting context to a specific subscription"     
       Set-AzureRmContext -SubscriptionId $SubId              
    }
    catch {
        if (!$servicePrincipalConnection)
        {
           $ErrorMessage = "Connection $connectionName not found."
           throw $ErrorMessage
         } else{
            Write-Error -Message $_.Exception
            throw $_.Exception
         }
    }

为了帮助你轻松地在多个订阅之间执行操作，该脚本包含两行额外的代码来支持引用订阅上下文。 名为 *SubscriptionId* 的变量资产包含订阅的 ID。 在 `Add-AzureRmAccount` cmdlet 语句的后面，使用参数集 *-SubscriptionId* 声明 [`Set-AzureRmContext`](https://msdn.microsoft.com/library/mt619263.aspx) cmdlet。 如果变量名称太过普通，可以修改它，使其包含前缀或使用其他命名约定，让名称更易于识别。 或者，可对相应的变量资产使用参数集 *-SubscriptionName* 而不是 *-SubscriptionId*。

Runbook 中用于身份验证的 cmdlet `Add-AzureRmAccount` 使用 *ServicePrincipalCertificate* 参数集。 它使用服务主体证书而不是用户凭据进行身份验证。

## <a name="sample-code-to-authenticate-with-service-management-resources"></a>用于使用服务管理资源进行身份验证的示例代码
可以参考以下已更新的示例代码（摘自 *AzureClassicAutomationTutorialScript* 示例 Runbook）使用经典运行方式帐户进行身份验证，以便使用 Runbook 管理经典资源。

    $ConnectionAssetName = "AzureClassicRunAsConnection"
    # Get the connection
    $connection = Get-AutomationConnection -Name $connectionAssetName        

    # Authenticate to Azure with certificate
    Write-Verbose "Get connection asset: $ConnectionAssetName" -Verbose
    $Conn = Get-AutomationConnection -Name $ConnectionAssetName
    if ($Conn -eq $null)
    {
       throw "Could not retrieve connection asset: $ConnectionAssetName. Assure that this asset exists in the Automation account."
    }

    $CertificateAssetName = $Conn.CertificateAssetName
    Write-Verbose "Getting the certificate: $CertificateAssetName" -Verbose
    $AzureCert = Get-AutomationCertificate -Name $CertificateAssetName
    if ($AzureCert -eq $null)
    {
       throw "Could not retrieve certificate asset: $CertificateAssetName. Assure that this asset exists in the Automation account."
    }

    Write-Verbose "Authenticating to Azure with certificate." -Verbose
    Set-AzureSubscription -SubscriptionName $Conn.SubscriptionName -SubscriptionId $Conn.SubscriptionID -Certificate $AzureCert
    Select-AzureSubscription -SubscriptionId $Conn.SubscriptionID

## <a name="next-steps"></a>后续步骤
* [Azure AD 中的应用程序对象和服务主体对象](../active-directory/active-directory-application-objects.md)
* [Azure 自动化中基于角色的访问控制](automation-role-based-access-control.md)
* [Azure 云服务证书概述](../cloud-services/cloud-services-certs-create.md)

