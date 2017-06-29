---
title: "创建 Azure 自动化运行方式帐户 | Microsoft Docs"
description: "本文介绍如何使用 PowerShell 或通过门户更新自动化帐户并创建运行方式帐户。"
services: automation
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/01/2017
ms.author: magoedte
ms.translationtype: Human Translation
ms.sourcegitcommit: 43aab8d52e854636f7ea2ff3aae50d7827735cc7
ms.openlocfilehash: 431049c714a58f85ebb73165fe338d927d27039a
ms.contentlocale: zh-cn
ms.lasthandoff: 06/03/2017

---

# <a name="update-your-automation-account-authentication-with-run-as-accounts"></a>使用运行方式帐户更新自动化帐户身份验证 
在以下情况下，可以通过门户或使用 PowerShell 更新现有自动化帐户：

* 你创建了自动化帐户，但不想要创建运行方式帐户。
* 已使用一个自动化帐户来管理 Resource Manager 资源，并且想要更新该帐户，以包含可用于 Runbook 身份验证的运行方式帐户
* 已使用一个自动化帐户来管理经典资源，并且想要对它进行更新以使用经典运行方式帐户，而不是创建新的帐户并将 Runbook 和资产迁移到该帐户。   
* 你想要使用企业证书颁发机构 (CA) 颁发的证书创建运行方式帐户和经典运行方式帐户。

## <a name="prerequisites"></a>先决条件

* 该脚本只能在装有 Azure Resource Manager 模块 3.0.0 和更高版本的 Windows 10 与 Windows Server 2016 上运行。 在 Windows 的早期版本中不受支持。
* Azure PowerShell 1.0 和更高版本。 有关 PowerShell 1.0 版本的信息，请参阅[如何安装和配置 Azure PowerShell](/powershell/azureps-cmdlets-docs)。
* 在以下 PowerShell 脚本中作为 *–AutomationAccountName* 和 *-ApplicationDisplayName* 参数值引用的自动化帐户。

若要获取脚本的必需参数 SubscriptionID、ResourceGroup 和 AutomationAccountName 的值，请执行以下操作：

1. 在 Azure 门户上的“自动化帐户”边栏选项卡中选择你的自动化帐户，然后选择“所有设置”。  
2. 在“所有设置”边栏选项卡中，选择“帐户设置”下面的“属性”。 
3. 请记下“属性”边栏选项卡中的值。<br><br> ![自动化帐户的“属性”边栏选项卡](media/automation-create-runas-account/automation-account-properties.png)  

## <a name="create-run-as-account-from-the-portal"></a>通过门户创建运行方式帐户
在本部分中，请执行以下步骤，通过 Azure 门户更新 Azure 自动化帐户。  分别创建运行方式帐户和经典运行方式帐户，如果不需要管理经典 Azure 门户中的资源，可只创建 Azure 运行方式帐户。  

此进程在自动化帐户中创建以下各项。

**对于运行方式帐户：**

* 将创建使用自签名证书的 Azure AD 应用程序，在 Azure AD 中为此应用程序创建服务主体帐户，并在当前订阅中为此帐户分配“参与者”角色。 可将此项设置更改为“所有者”或其他任何角色。 有关详细信息，请参阅 [Azure 自动化中基于角色的访问控制](automation-role-based-access-control.md)。
* 在指定的自动化帐户中创建名为 *AzureRunAsCertificate* 的自动化证书资产。 该证书资产保存 Azure AD 应用程序使用的证书私钥。
* 在指定的自动化帐户中创建名为 *AzureRunAsConnection* 的自动化连接资产。 该连接资产保存 applicationId、tenantId、subscriptionId 和证书指纹。

**对于经典运行方式帐户：**

* 在指定的自动化帐户中创建名为 *AzureClassicRunAsCertificate* 的自动化证书资产。 该证书资产保存管理证书使用的证书私钥。
* 在指定的自动化帐户中创建名为 *AzureClassicRunAsConnection* 的自动化连接资产。 该连接资产保存订阅名称、subscriptionId 和证书资产名称。


1. 以订阅管理员角色成员和订阅共同管理员的帐户登录 Azure 门户。
2. 在“自动化帐户”边栏选项卡上的“帐户设置”部分下，选择“运行方式帐户”。  
3. 根据所需帐户，选择“Azure 运行方式帐户”或“Azure 经典运行方式帐户”。  选择后，便会出现“添加 Azure 运行方式帐户”或“添加 Azure 经典运行方式帐户”边栏选项卡，查看概述信息后，单击“创建”，继续创建运行方式帐户。  
4. Azure 创建运行方式帐户时，用户可以在菜单的“通知”下跟踪进度，此时会显示一个横幅，指示正在创建帐户。  此过程可能需要几分钟才能完成。  

## <a name="create-run-as-account-using-powershell-script"></a>使用 PowerShell 脚本创建运行方式帐户
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
> 如果选择任一选项来创建经典运行方式帐户，则在执行脚本后，请将公共证书（.cer 文件扩展名）上传到创建自动化帐户的订阅的管理存储中。
> 

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
        $KeyCredential.EndDate = $KeyCredential.EndDate.AddDays(-1)
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
        if (!(($AzureRMProfileVersion.Major -ge 3 -and $AzureRMProfileVersion.Minor -ge 0) -or ($AzureRMProfileVersion.Major -gt 3)))
        {
            Write-Error -Message "Please install the latest Azure PowerShell and retry. Relevant doc url : https://docs.microsoft.com/powershell/azureps-cmdlets-docs/ "
            return
        }

        Login-AzureRmAccount -Environment $EnvironmentName 
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
        $SubscriptionName = $subscription.Subscription.Name
        $ClassicRunAsAccountConnectionFieldValues = @{"SubscriptionName" = $SubscriptionName; "SubscriptionId" = $SubscriptionId; "CertificateAssetName" = $ClassicRunAsAccountCertifcateAssetName}

        # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
        CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountConnectionAssetName $ClassicRunAsAccountConnectionTypeName $ClassicRunAsAccountConnectionFieldValues

        Write-Host -ForegroundColor red $UploadMessage
        }

2. 在计算机上，从“开始”屏幕以提升的用户权限启动 **Windows PowerShell**。
3. 在提升权限的命令行外壳中，转到包含步骤 1 所创建脚本的文件夹。  
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
* 如果使用企业公共证书（.cer 文件）创建了经典运行方式帐户，则使用此证书。 遵循有关[将管理 API 证书上传到 Azure 经典门户](../azure-api-management-certs.md)的说明，然后参考[用于通过 Azure 经典部署资源进行身份验证的示例代码](automation-verify-runas-authentication.md#classic-run-as-authentication)，使用经典部署资源来验证凭据配置。 
* 如果*未*创建经典运行方式帐户，请参考[用于通过服务管理资源进行身份验证的示例代码](automation-verify-runas-authentication.md#automation-run-as-authentication)，使用 Resource Manager 资源进行身份验证并验证凭据配置。

## <a name="next-steps"></a>后续步骤
* 有关服务主体的详细信息，请参阅 [Application Objects and Service Principal Objects](../active-directory/active-directory-application-objects.md)（应用程序对象和服务主体对象）。
* 有关证书和 Azure 服务的详细信息，请参阅 [Azure 云服务证书概述](../cloud-services/cloud-services-certs-create.md)。
