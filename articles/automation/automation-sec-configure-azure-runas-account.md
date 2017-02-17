---
title: "配置 Azure 运行方式帐户 | Microsoft Docs"
description: "本教程引导你在 Azure 自动化中创建、测试安全主体，并示范如何使用安全主体进行身份验证。"
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: 
keywords: "服务主体名称, setspn, azure 身份验证"
ms.assetid: 2f783441-15c7-4ea0-ba27-d7daa39b1dd3
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/05/2017
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: aec8fd057bd31fc933d19996567437b2a897623b
ms.openlocfilehash: 889c9a53e3ce454ee9ac9fc0f24b2ff8244e87c6


---
# <a name="authenticate-runbooks-with-azure-run-as-account"></a>使用 Azure 运行方式帐户进行 Runbook 身份验证
本主题说明如何从 Azure 门户使用运行方式帐户功能配置自动化帐户，对在 Azure Resource Manager 或 Azure 服务管理中管理资源的 Runbook 进行身份验证。

在 Azure 门户中创建新的自动化帐户时，还会自动创建以下帐户：

* 运行方式帐户，用于在 Azure Active Directory 中创建新的服务主体、证书以及分配基于参与者角色的访问控制 (RBAC)（用于使用 Runbook 管理 Resource Manager 资源）。   
* 经典运行方式帐户（通过上载管理证书），用于使用 Runbook 管理 Azure 服务管理或经典资源。  

这样可以简化操作过程，并帮助你快速开始构建和部署 Runbook 来支持自动化需求。      

使用运行方式帐户和经典运行方式帐户时，可以：

* 在 Azure 门户中通过 Runbook 管理 Azure Resource Manager 或 Azure 服务管理资源时，提供标准化 Azure 身份验证方式。  
* 自动使用 Azure 警报中配置的全局 Runbook。

> [!NOTE]
> 使用自动化全局 Runbook 的 Azure [警报集成功能](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)需要已配置了运行方式帐户和经典运行方式帐户的自动化帐户。 可以选择已定义运行方式帐户和经典运行方式帐户的自动化帐户，也可以创建新的自动化帐户。
> 
> 

我们将说明如何从 Azure 门户创建自动化帐户、如何使用 PowerShell 更新自动化帐户，以及演示如何在 Runbook 中进行身份验证。

在此之前，应该先了解并考虑一些事项，然后再继续。

1. 这不会影响经典或 Resource Manager 部署模型中已创建的现有自动化帐户。  
2. 这只适用于通过 Azure 门户创建的自动化帐户。  如果尝试从经典门户创建帐户，将不会复制运行方式帐户配置。
3. 如果目前使用之前创建的 Runbook 和资产（即计划、变量等）来管理经典资源，并希望这些 Runbook 使用新的经典运行方式帐户进行身份验证，则需要将它们迁移到新的自动化帐户，或使用以下 PowerShell 脚本更新现有帐户。  
4. 若要使用新的运行方式帐户和经典运行方式自动化帐户进行身份验证，需要使用下面的示例代码修改现有的 Runbook。  **请注意** ，运行方式帐户使用基于证书的服务主体针对 Resource Manager 资源进行身份验证，而经典运行方式帐户则使用管理证书针对服务管理资源进行身份验证。     

## <a name="create-a-new-automation-account-from-the-azure-portal"></a>从 Azure 门户创建新的自动化帐户
在本部分中，将执行以下步骤从 Azure 门户创建一个新的 Azure 自动化帐户。  这会同时创建运行方式帐户和经典运行方式帐户。  

> [!NOTE]
> 执行这些步骤的用户必须是“服务管理员”角色的成员，或者是向用户授予订阅访问权限的订阅共同管理员。 此外，该用户还必须添加为订阅默认 Active Directory 的用户；不需要向该帐户分配特权角色。 在添加到订阅订阅“共同管理员”角色之前不属于订阅 Active Directory 成员的用户将作为来宾添加到 Active Directory，并且 会在“添加自动化帐户”边栏选项卡中看到“你无权创建...”警告。 可以先从订阅 Active Directory 中删除已添加到共同管理员角色的用户，然后重新添加，使其成为 Active Directory 中的完整用户。 若要验证这种情况，可在 Azure 门户的“Azure Active Directory”窗格中选择“用户和组”，选择“所有用户”，在选择特定的用户后再选择“配置文件”。  用户配置文件下的“用户类型”属性值不应等于“来宾”。  
> 
> 

1. 以订阅管理员角色成员和订阅共同管理员的帐户登录到 Azure 门户。
2. 选择“自动化帐户”。
3. 在“自动化帐户”边栏选项卡中，单击“添加”。<br>![添加自动化帐户](media/automation-sec-configure-azure-runas-account/create-automation-account-properties-b.png)
   
   > [!NOTE]
   > 如果在“添加自动化帐户”边栏选项卡中看到以下警告，是因为帐户不是订阅管理员角色成员和订阅共同管理员。<br>![添加自动化帐户警报](media/automation-sec-configure-azure-runas-account/create-account-without-perms.png)
   > 
   > 
4. 在“添加自动化帐户”边栏选项卡的“名称”框中，键入新自动化帐户的名称。
5. 如果有多个订阅，请为新帐户指定一个订阅，并指定一个新的或现有的**资源组**以及 Azure 数据中心**位置**。
6. 确认为“创建 Azure 运行方式帐户”选项选择了“是”值，然后单击“创建”按钮。  
   
   > [!NOTE]
   > 如果通过选择“否”选项来选择不创建运行方式帐户，则“添加自动化帐户”边栏选项卡中会出现一条警告消息。  尽管该帐户是在 Azure 门户中创建的，但它在经典或 Resource Manager 订阅目录服务中没有对应的身份验证标识，因此，无法访问订阅中的资源。  这会导致引用此帐户的任何 Runbook 无法进行身份验证，也无法针对这些部署模型中的资源执行任务。
   > 
   > ![添加自动化帐户警告](media/automation-sec-configure-azure-runas-account/create-account-decline-create-runas-msg.png)<br>
   > 未创建服务主体时不会分配参与者角色。
   > 
   > 
7. 在 Azure 创建自动化帐户时，可以在菜单的“通知”下面跟踪进度。

### <a name="resources-included"></a>包含的资源
成功创建自动化帐户后，系统将自动为你创建几个资源。  下表汇总了运行方式帐户的资源。<br>

| 资源 | 说明 |
| --- | --- |
| AzureAutomationTutorial Runbook |一个示例 PowerShell Runbook，演示如何使用运行方式帐户进行身份验证并获取所有 Resource Manager 资源。 |
| AzureAutomationTutorialScript Runbook |一个示例 PowerShell Runbook，演示如何使用运行方式帐户进行身份验证并获取所有 Resource Manager 资源。 |
| AzureRunAsCertificate |在创建自动化帐户期间自动创建的，或针对现有帐户使用以下 PowerShell 脚本创建的证书资产。  使用此证书，可以向 Azure 进行身份验证，以便通过 Runbook 管理 Azure Resource Manager 资源。  此证书有一年的有效期。 |
| AzureRunAsConnection |在创建自动化帐户期间自动创建的，或针对现有帐户使用以下 PowerShell 脚本创建的连接资产。 |

下表汇总了经典运行方式帐户的资源。<br>

| 资源 | 说明 |
| --- | --- |
| AzureClassicAutomationTutorial Runbook |一个示例 Runbook，它可以使用经典运行方式帐户（证书）获取订阅中的所有经典 VM，然后输出 VM 名称和状态。 |
| AzureClassicAutomationTutorial 脚本 Runbook |一个示例 Runbook，它可以使用经典运行方式帐户（证书）获取订阅中的所有经典 VM，然后输出 VM 名称和状态。 |
| AzureClassicRunAsCertificate |自动创建的证书资产，用于向 Azure 进行身份验证，以便通过 Runbook 管理 Azure 经典资源。  此证书有一年的有效期。 |
| AzureClassicRunAsConnection |自动创建的连接资产，用于向 Azure 进行身份验证，以便通过 Runbook 管理 Azure 经典资源。 |

## <a name="verify-run-as-authentication"></a>验证运行方式身份验证
接下来，将执行一个小测试，以确认是否能够使用新的运行方式帐户成功进行身份验证。     

1. 在 Azure 门户中，打开前面创建的自动化帐户。  
2. 单击“Runbook”  磁贴打开 Runbook 的列表。
3. 选择 **AzureAutomationTutorialScript** Runbook，然后单击“启动”以启动该 Runbook。  系统会提示用户确认要启动该 Runbook。
4. 将创建一个 [Runbook 作业](automation-runbook-execution.md)并显示“作业”边栏选项卡，“作业摘要”磁贴中显示作业状态。  
5. 作业状态最初为“排队”，表示它正在等待云中的 Runbook 辅助角色变为可用  。 在某个辅助角色认领该作业后，该作业状态将变为“正在启动”，然后当 Runbook 实际开始运行时，该作业状态将变为“正在运行”。  
6. Runbook 作业完成时，应会看到状态变为“已完成”。<br> ![安全主体 Runbook 测试](media/automation-sec-configure-azure-runas-account/job-summary-automationtutorialscript.png)<br>
7. 若要查看 Runbook 的详细结果，请单击“输出”磁贴。
8. 在“输出”边栏选项卡中，应会看到帐户已成功完成身份验证并返回资源组中所有可用资源的列表。
9. 关闭“输出”边栏选项卡返回“作业摘要”边栏选项卡。
10. 关闭“作业摘要”和相应的 **AzureAutomationTutorialScript** Runbook 边栏选项卡。

## <a name="verify-classic-run-as-authentication"></a>验证经典运行方式身份验证
接下来，将执行一个小测试，以确认是否能够使用新的经典运行方式帐户成功进行身份验证。     

1. 在 Azure 门户中，打开前面创建的自动化帐户。  
2. 单击“Runbook”  磁贴打开 Runbook 的列表。
3. 选择 **AzureClassicAutomationTutorialScript** Runbook，然后单击“启动”以启动该 Runbook。  系统会提示用户确认要启动该 Runbook。
4. 将创建一个 [Runbook 作业](automation-runbook-execution.md)并显示“作业”边栏选项卡，“作业摘要”磁贴中显示作业状态。  
5. 作业状态最初为“排队”，表示它正在等待云中的 Runbook 辅助角色变为可用  。 在某个辅助角色认领该作业后，该作业状态将变为“正在启动”，然后当 Runbook 实际开始运行时，该作业状态将变为“正在运行”。  
6. Runbook 作业完成时，应会看到状态变为“已完成”。<br> ![安全主体 Runbook 测试](media/automation-sec-configure-azure-runas-account/job-summary-automationclassictutorialscript.png)<br>
7. 若要查看 Runbook 的详细结果，请单击“输出”磁贴。
8. 在“输出”边栏选项卡中，应会看到帐户已成功完成身份验证并返回订阅中所有经典 VM 的列表。
9. 关闭“输出”边栏选项卡返回“作业摘要”边栏选项卡。
10. 关闭“作业摘要”和相应的 **AzureClassicAutomationTutorialScript** Runbook 边栏选项卡。

## <a name="update-an-automation-account-using-powershell"></a>使用 PowerShell 更新自动化帐户
我们在此为你提供了使用 PowerShell 来更新现有自动化帐户的选项，但前提是：

1. 已创建一个自动化帐户，但拒绝创建运行方式帐户
2. 已有一个自动化帐户用于管理 Resource Manager 资源，并且想要对它进行更新以包含可供 Runbook 身份验证的运行方式帐户
3. 已有一个自动化帐户用于管理经典资源，并且想要对它进行更新以使用经典运行方式，而不是创建新的帐户并将 Runbook 和资产迁移到该帐户   

在继续之前，请确认以下事项：

1. 如果你运行的是 Windows 7，已下载并安装了 [Windows Management Framework (WMF) 4.0](https://www.microsoft.com/download/details.aspx?id=40855) 。   
    如果运行的是 Windows Server 2012 R2、Windows Server 2012、Windows 2008 R2、Windows 8.1 和 Windows 7 SP1， [Windows Management Framework 5.0](https://www.microsoft.com/download/details.aspx?id=50395) 已可供安装。
2. Azure PowerShell 1.0。 有关此版本及其安装方法的信息，请参阅 [How to install and configure Azure PowerShell](/powershell/azureps-cmdlets-docs)（如何安装和配置 Azure PowerShell）。
3. 已创建自动化帐户。  以下两个脚本中的 -AutomationAccountName 和 -ApplicationDisplayName 参数值将引用此帐户。

若要在 Azure 门户中获取 *SubscriptionID*、*ResourceGroup* 和 *AutomationAccountName* 的值（这些都是脚本的必需参数），请从“自动化帐户”边栏选项卡中选择自动化帐户，然后选择“所有设置”。  在“所有设置”边栏选项卡中，选择“帐户设置”下面的“属性”。  在“属性”边栏选项卡中，可以记下这些值。<br> ![自动化帐户属性](media/automation-sec-configure-azure-runas-account/automation-account-properties.png)  

### <a name="create-run-as-account-powershell-script"></a>创建运行方式帐户 PowerShell 脚本
下面的 PowerShell 脚本将配置以下项：

* 一个 Azure AD 应用程序，它将使用自签名证书进行身份验证，创建此应用程序在 Azure AD 中的服务主体帐户，并在当前订阅中为此帐户分配参与者角色（可以将此角色更改为所有者或任何其他角色）。  有关更多信息，请查看 [Role-based access control in Azure Automation](automation-role-based-access-control.md)（Azure 自动化中基于角色的访问控制）一文。
* 指定的自动化帐户中名为 **AzureRunAsCertificate**的自动化证书资产，用于保存服务主体使用的证书。
* 指定的自动化帐户中名为 **AzureRunAsConnection**的自动化连接资产，用于保存 applicationId、tenantId、subscriptionId 和证书指纹。    

以下步骤将引导用户完成执行该脚本的过程。

1. 将以下脚本保存到计算机。  在本示例中，请使用文件名 **New-AzureServicePrincipal.ps1**保存。  
   
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
        [String] $CertPlainPassword,
   
        [Parameter(Mandatory=$false)]
        [int] $NoOfMonthsUntilExpired = 12
        )
   
        Login-AzureRmAccount
        Import-Module AzureRM.Resources
        Select-AzureRmSubscription -SubscriptionId $SubscriptionId
   
        $CurrentDate = Get-Date
        $EndDate = $CurrentDate.AddMonths($NoOfMonthsUntilExpired)
        $KeyId = (New-Guid).Guid
        $CertPath = Join-Path $env:TEMP ($ApplicationDisplayName + ".pfx")
   
        $Cert = New-SelfSignedCertificate -DnsName $ApplicationDisplayName -CertStoreLocation cert:\LocalMachine\My -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider"
   
        $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force
        Export-PfxCertificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $CertPath -Password $CertPassword -Force | Write-Verbose
   
        $PFXCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate -ArgumentList @($CertPath, $CertPlainPassword)
        $KeyValue = [System.Convert]::ToBase64String($PFXCert.GetRawCertData())
   
        $KeyCredential = New-Object  Microsoft.Azure.Commands.Resources.Models.ActiveDirectory.PSADKeyCredential
        $KeyCredential.StartDate = $CurrentDate
        $KeyCredential.EndDate= $EndDate
        $KeyCredential.KeyId = $KeyId
        $KeyCredential.Type = "AsymmetricX509Cert"
        $KeyCredential.Usage = "Verify"
        $KeyCredential.Value = $KeyValue
   
        # Use Key credentials
        $Application = New-AzureRmADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $ApplicationDisplayName) -IdentifierUris ("http://" + $KeyId) -KeyCredentials $keyCredential
   
        New-AzureRMADServicePrincipal -ApplicationId $Application.ApplicationId | Write-Verbose
        Get-AzureRmADServicePrincipal | Where {$_.ApplicationId -eq $Application.ApplicationId} | Write-Verbose
   
        $NewRole = $null
        $Retries = 0;
        While ($NewRole -eq $null -and $Retries -le 6)
        {
           # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
           Sleep 5
           New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
           Sleep 10
           $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
           $Retries++;
        }
   
        # Get the tenant id for this subscription
        $SubscriptionInfo = Get-AzureRmSubscription -SubscriptionId $SubscriptionId
        $TenantID = $SubscriptionInfo | Select TenantId -First 1
   
        # Create the automation resources
        New-AzureRmAutomationCertificate -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Path $CertPath -Name AzureRunAsCertificate -Password $CertPassword -Exportable | write-verbose
   
        # Create a Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
        $ConnectionAssetName = "AzureRunAsConnection"
        Remove-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -Force -ErrorAction SilentlyContinue
        $ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
        New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
2. 在计算机上，从“开始”屏幕以提升的用户权限启动 **Windows PowerShell**。
3. 从提升的 PowerShell 命令行 shell 导航到包含步骤 1 中创建的脚本的文件夹，并执行脚本来更改 *-ResourceGroup*、*-AutomationAccountName*、*-ApplicationDisplayName*、*-SubscriptionId* 和 *-CertPlainPassword* 参数的值。<br>
   
   > [!NOTE]
   > 执行脚本后，系统将提示你在 Azure 上进行身份验证。 必须以订阅管理员角色成员和订阅共同管理员的帐户登录。
   > 
   > 
   
        .\New-AzureServicePrincipal.ps1 -ResourceGroup <ResourceGroupName>
        -AutomationAccountName <NameofAutomationAccount> `
        -ApplicationDisplayName <DisplayNameofAutomationAccount> `
        -SubscriptionId <SubscriptionId> `
        -CertPlainPassword "<StrongPassword>"  
   <br>

成功完成脚本之后，请参考以下 [示例代码](#sample-code-to-authenticate-with-resource-manager-resources) 使用 Resource Manager 资源进行身份验证并验证凭据配置。

### <a name="create-classic-run-as-account-powershell-script"></a>创建经典运行方式帐户 PowerShell 脚本
下面的 PowerShell 脚本将配置以下项：

* 指定的自动化帐户中名为 **AzureClassicRunAsCertificate**的自动化证书资产，其中保存用于对 Runbook 进行身份验证的证书。
* 指定的自动化帐户中名为 **AzureClassicRunAsConnection**的自动化连接资产，用于保存订阅名称、subscriptionId 和证书资产名称。

脚本将创建自签名管理证书，并将它保存到计算机上用于执行 PowerShell 会话的用户配置文件下的临时文件文件夹中：*%USERPROFILE%\AppData\Local\Temp*。  执行脚本后，必须将 Azure 管理证书上载到创建自动化帐户的订阅的管理存储中。  以下步骤将引导用户完成执行脚本和上载证书的过程。  

1. 将以下脚本保存到计算机。  在本示例中，请使用文件名 **New-AzureClassicRunAsAccount.ps1**保存。
   
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
        [String] $CertPlainPassword,
   
        [Parameter(Mandatory=$false)]
        [int] $NoOfMonthsUntilExpired = 12
        )
   
        Login-AzureRmAccount
        Import-Module AzureRM.Resources
        $Subscription = Select-AzureRmSubscription -SubscriptionId $SubscriptionId
        $SubscriptionName = $subscription.Subscription.SubscriptionName
   
        $CurrentDate = Get-Date
        $EndDate = $CurrentDate.AddMonths($NoOfMonthsUntilExpired)
        $KeyId = (New-Guid).Guid
        $CertPath = Join-Path $env:TEMP ($ApplicationDisplayName + ".pfx")
        $CertPathCer = Join-Path $env:TEMP ($ApplicationDisplayName + ".cer")
   
        $Cert = New-SelfSignedCertificate -DnsName $ApplicationDisplayName -CertStoreLocation cert:\LocalMachine\My -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider"
   
        $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force
        Export-PfxCertificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $CertPath -Password $CertPassword -Force | Write-Verbose
        Export-Certificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $CertPathCer -Type CERT | Write-Verbose
   
        # Create the automation resources
        $ClassicCertificateAssetName = "AzureClassicRunAsCertificate"
        New-AzureRmAutomationCertificate -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Path $CertPath -Name $ClassicCertificateAssetName  -Password $CertPassword -Exportable | write-verbose
   
        # Create a Automation connection asset named AzureClassicRunAsConnection in the Automation account. This connection uses the ClassicCertificateAssetName.
        $ConnectionAssetName = "AzureClassicRunAsConnection"
        Remove-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -Force -ErrorAction SilentlyContinue
        $ConnectionFieldValues = @{"SubscriptionName" = $SubscriptionName; "SubscriptionId" = $SubscriptionId; "CertificateAssetName" = $ClassicCertificateAssetName}
        New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureClassicCertificate -ConnectionFieldValues $ConnectionFieldValues
   
        Write-Host -ForegroundColor red "Please upload the cert $CertPathCer to the Management store by following the steps below."
        Write-Host -ForegroundColor red "Log in to the Microsoft Azure Management portal (https://manage.windowsazure.com) and select Settings -> Management Certificates."
        Write-Host -ForegroundColor red "Then click Upload and upload the certificate $CertPathCer"
2. 在计算机上，从“开始”屏幕以提升的用户权限启动 **Windows PowerShell**。  
3. 从提升的 PowerShell 命令行 shell 导航到包含步骤 1 中创建的脚本的文件夹，并执行脚本来更改 *-ResourceGroup*、*-AutomationAccountName*、*-ApplicationDisplayName*、*-SubscriptionId* 和 *-CertPlainPassword* 参数的值。<br>
   
   > [!NOTE]
   > 执行脚本后，系统将提示你在 Azure 上进行身份验证。 必须以订阅管理员角色成员和订阅共同管理员的帐户登录。
   > 
   > 
   
        .\New-AzureClassicRunAsAccount.ps1 -ResourceGroup <ResourceGroupName>
        -AutomationAccountName <NameofAutomationAccount> `
        -ApplicationDisplayName <DisplayNameofAutomationAccount> `
        -SubscriptionId <SubscriptionId> `
        -CertPlainPassword "<StrongPassword>"

成功完成脚本后，必须复制在用户配置文件 **Temp** 文件夹中创建的证书。  遵循将[管理 API 证书上载](../azure-api-management-certs.md)到 Azure 经典门户的步骤，然后参考[示例代码](#sample-code-to-authenticate-with-service-management-resources)使用服务管理资源来验证凭据配置。

## <a name="sample-code-to-authenticate-with-resource-manager-resources"></a>用于使用 Resource Manager 资源进行身份验证的示例代码
可以使用以下已更新的示例代码（取自 **AzureAutomationTutorialScript** 示例 Runbook），以运行方式帐户进行身份验证来使用 Runbook 管理 Resource Manager 资源。   

    $connectionName = "AzureRunAsConnection"
    $SubId = Get-AutomationVariable -Name 'SubscriptionId'
    try
    {
       # Get the connection "AzureRunAsConnection "
       $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

       "Logging in to Azure..."
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


该脚本包含两行额外的代码以支持引用订阅上下文，方便你轻松地在多个订阅之间执行操作。 名为 SubscriptionId 的变量资产包含订阅 ID，并且在 Add-AzureRmAccount cmdlet 语句之后，使用参数集 [-SubscriptionId](https://msdn.microsoft.com/library/mt619263.aspx) 声明 *Set-AzureRmContext cmdlet*。 如果变量名称太过普通，你可以修改变量名称，使其包含前缀或其他命名约定，以便让名称能够更容易地指出你的目的。 或者，你可以将参数集 -SubscriptionName（而非 -SubscriptionId）与相应的变量资产搭配使用。  

请注意，Runbook 中用于身份验证的 cmdlet **Add-AzureRmAccount**使用 *ServicePrincipalCertificate* 参数集。  它使用服务主体证书（而不是凭据）进行身份验证。  

## <a name="sample-code-to-authenticate-with-service-management-resources"></a>用于使用服务管理资源进行身份验证的示例代码
可以使用以下已更新的示例代码（取自 **AzureClassicAutomationTutorialScript** 示例 Runbook），以经典运行方式帐户进行身份验证来使用 Runbook 管理经典资源。

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
* 有关服务主体的详细信息，请参阅 [Application Objects and Service Principal Objects](../active-directory/active-directory-application-objects.md)（应用程序对象和服务主体对象）。
* 有关 Azure 自动化中基于角色的访问控制的详细信息，请参阅 [Role-Based access control in Azure Automation](automation-role-based-access-control.md)（Azure 自动化中基于角色的访问控制）。
* 有关证书和 Azure 服务的详细信息，请参阅 [Certificates overview for Azure Cloud Services](../cloud-services/cloud-services-certs-create.md)




<!--HONumber=Feb17_HO3-->


