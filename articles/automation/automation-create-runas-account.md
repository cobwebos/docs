---
title: 创建 Azure 自动化运行方式帐户
description: 本文介绍如何使用 PowerShell 或通过门户更新自动化帐户并创建运行方式帐户。
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: b50c9f059d82d9378fba8db663aa2e8dce0273f5
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="update-your-automation-account-authentication-with-run-as-accounts"></a>使用运行方式帐户更新自动化帐户身份验证 
在以下情况下，可以通过 Azure 门户或使用 PowerShell 更新现有自动化帐户：

* 创建了自动化帐户，但不想要创建运行方式帐户。
* 已使用一个自动化帐户来管理 Resource Manager 资源，并且想要更新该帐户，以包含可用于 Runbook 身份验证的运行方式帐户
* 已使用一个自动化帐户来管理经典资源，并且想要对它进行更新以使用经典运行方式帐户，而不是创建新的帐户并将 Runbook 和资产迁移到该帐户。   

此进程在自动化帐户中创建以下各项。

**对于运行方式帐户：**

* 将创建使用自签名证书的 Azure AD 应用程序，在 Azure AD 中为此应用程序创建服务主体帐户，并在当前订阅中为此帐户分配“参与者”角色。 可将此项设置更改为“所有者”或其他任何角色。 有关详细信息，请参阅 [Azure 自动化中基于角色的访问控制](automation-role-based-access-control.md)。
* 在指定的自动化帐户中创建名为 *AzureRunAsCertificate* 的自动化证书资产。 该证书资产保存 Azure AD 应用程序使用的证书私钥。
* 在指定的自动化帐户中创建名为 *AzureRunAsConnection* 的自动化连接资产。 该连接资产保存 applicationId、tenantId、subscriptionId 和证书指纹。

**对于经典运行方式帐户：**

* 在指定的自动化帐户中创建名为 *AzureClassicRunAsCertificate* 的自动化证书资产。 该证书资产保存管理证书使用的证书私钥。
* 在指定的自动化帐户中创建名为 *AzureClassicRunAsConnection* 的自动化连接资产。 该连接资产保存订阅名称、subscriptionId 和证书资产名称。

## <a name="prerequisites"></a>先决条件
如果选择[使用 PowerShell 创建运行方式帐户](#create-run-as-account-using-powershell)，则此过程需要：

* 装有 Azure 资源管理器模块 3.4.1 及更高版本的 Windows 10 与 Windows Server 2016。 PowerShell 脚本不支持早期版本的 Windows。
* Azure PowerShell 1.0 和更高版本。 有关 PowerShell 1.0 版本的信息，请参阅[如何安装和配置 Azure PowerShell](/powershell/azureps-cmdlets-docs)。
* 作为 –AutomationAccountName 和 -ApplicationDisplayName 参数的值引用的自动化帐户。

若要获取脚本的必需参数 SubscriptionID、ResourceGroup 和 AutomationAccountName 的值，请执行以下操作：

1. 在 Azure 门户中，单击“所有服务”。 在资源列表中，键入“自动化”。 开始键入时，会根据输入筛选该列表。 选择“自动化帐户”。
2. 在“自动化帐户”页中选择自动化帐户，然后在“帐户设置”下选择“属性”。  
3. 记下“属性”页中的值。<br><br> ![自动化帐户的“属性”边栏选项卡](media/automation-create-runas-account/automation-account-properties.png)  

### <a name="required-permissions-to-update-your-automation-account"></a>更新自动化帐户所需的权限
若要更新自动化帐户，必须具有完成本主题所需的下述特定权限。   
 
* 必须将 AD 用户帐户添加到一个角色，该角色的权限相当于 Microsoft.Automation 资源的参与者角色，如 [Azure 自动化中基于角色的访问控制](automation-role-based-access-control.md#contributor)一文所述。  
* Azure AD 租户中的非管理员用户可以[注册 AD 应用程序](../azure-resource-manager/resource-group-create-service-principal-portal.md#check-azure-subscription-permissions)，前提是 Azure AD 租户的“用户设置”页中的“用户可以注册应用程序”选项已设置为“是”。 如果“应用注册设置”设置为“否”，则执行此操作的用户必须是 Azure AD 中的全局管理员。

如果你在被添加到订阅的全局管理员/共同管理员角色之前不是订阅的 Active Directory 实例的成员，则会将你作为来宾添加到 Active Directory。 在这种情况下，“添加自动化帐户”边栏选项卡中会显示 “你无权创建...”警告。 可以先从订阅的 Active Directory 实例中删除已添加到全局管理员/共同管理员角色的用户，然后重新添加，使其成为 Active Directory 中的完整用户。 若要验证这种情况，可在 Azure 门户的“Azure Active Directory”窗格中选择“用户和组”，选择“所有用户”，在选择特定的用户后再选择“配置文件”。 用户配置文件下的“用户类型”属性值不应等于“来宾”。

## <a name="create-run-as-account-from-the-portal"></a>通过门户创建运行方式帐户
在本部分，请执行以下步骤，在 Azure 门户中更新 Azure 自动化帐户。 可以单独创建运行方式帐户和经典运行方式帐户。 如果不需管理经典资源，可以只创建 Azure 运行方式帐户。  

1. 以订阅管理员角色成员和订阅共同管理员的帐户登录 Azure 门户。
2. 在 Azure 门户中，单击“所有服务”。 在资源列表中，键入“自动化”。 开始键入时，会根据输入筛选该列表。 选择“自动化帐户”。
3. 在“自动化帐户”页的自动化帐户列表中选择自动化帐户。
4. 在左侧窗格的“帐户设置”部分下，选择“运行方式帐户”。  
5. 根据所需帐户，选择“Azure 运行方式帐户”或“Azure 经典运行方式帐户”。 选择后，便会出现“添加 Azure 运行方式帐户”或“添加 Azure 经典运行方式帐户”页。查看概述信息后，单击“创建”，继续创建运行方式帐户。  
6. 在 Azure 创建运行方式帐户时，可以在菜单的“通知”下面跟踪进度。 此外还显示一个横幅，指出正在创建帐户。 此过程可能需要几分钟才能完成。  

## <a name="create-run-as-account-using-powershell-script"></a>使用 PowerShell 脚本创建运行方式帐户
此 PowerShell 脚本包括对以下配置的支持：

* 使用自签名证书创建运行方式帐户。
* 使用自签名证书创建运行方式帐户和经典运行方式帐户。
* 使用企业证书颁发机构 (CA) 颁发的证书创建运行方式帐户和经典运行方式帐户。
* 在 Azure 政府版云中使用自签名证书创建运行方式帐户和经典运行方式帐户。

>[!NOTE]
> 如果选择任一选项来创建经典运行方式帐户，则在执行脚本后，请将公共证书（.cer 文件扩展名）上传到创建自动化帐户的订阅的管理存储中。
> 

1. 将以下脚本保存到计算机。 在本示例中，请使用文件名 *New-RunAsAccount.ps1* 保存。

    ```powershell
    #Requires -RunAsAdministrator
    Param (
        [Parameter(Mandatory = $true)]
        [String] $ResourceGroup,
        
        [Parameter(Mandatory = $true)]
        [String] $AutomationAccountName,
        
        [Parameter(Mandatory = $true)]
        [String] $ApplicationDisplayName,
        
        [Parameter(Mandatory = $true)]
        [String] $SubscriptionId,
        
        [Parameter(Mandatory = $true)]
        [Boolean] $CreateClassicRunAsAccount,
        
        [Parameter(Mandatory = $true)]
        [String] $SelfSignedCertPlainPassword,
        
        [Parameter(Mandatory = $false)]
        [String] $EnterpriseCertPathForRunAsAccount,
        
        [Parameter(Mandatory = $false)]
        [String] $EnterpriseCertPlainPasswordForRunAsAccount,
        
        [Parameter(Mandatory = $false)]
        [String] $EnterpriseCertPathForClassicRunAsAccount,
        
        [Parameter(Mandatory = $false)]
        [String] $EnterpriseCertPlainPasswordForClassicRunAsAccount,
        
        [Parameter(Mandatory = $false)]
        [ValidateSet("AzureCloud", "AzureUSGovernment")]
        [string]$EnvironmentName = "AzureCloud",
        
        [Parameter(Mandatory = $false)]
        [int] $SelfSignedCertNoOfMonthsUntilExpired = 12
    )
        
    function CreateSelfSignedCertificate([string] $certificateName, [string] $selfSignedCertPlainPassword,
        [string] $certPath, [string] $certPathCer, [string] $selfSignedCertNoOfMonthsUntilExpired ) {
        $Cert = New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation cert:\LocalMachine\My `
            -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" `
            -NotAfter (Get-Date).AddMonths($selfSignedCertNoOfMonthsUntilExpired) -HashAlgorithm SHA256
        
        $CertPassword = ConvertTo-SecureString $selfSignedCertPlainPassword -AsPlainText -Force
        Export-PfxCertificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPath -Password $CertPassword -Force | Write-Verbose
        Export-Certificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPathCer -Type CERT | Write-Verbose
    }
        
    function CreateServicePrincipal([System.Security.Cryptography.X509Certificates.X509Certificate2] $PfxCert, [string] $applicationDisplayName) {  
        $keyValue = [System.Convert]::ToBase64String($PfxCert.GetRawCertData())
        $keyId = (New-Guid).Guid
        
        $startDate = Get-Date
        $endDate = (Get-Date $PfxCert.GetExpirationDateString()).AddDays(-1)
        
        #Create an Azure AD application, AD App Credential, AD ServicePrincipal
        $Application = New-AzureRmADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $applicationDisplayName) -IdentifierUris ("http://" + $keyId) 
        $ApplicationCredential = New-AzureRmADAppCredential -ApplicationId $Application.ApplicationId -CertValue $keyValue -StartDate $startDate -EndDate $endDate 
        $ServicePrincipal = New-AzureRMADServicePrincipal -ApplicationId $Application.ApplicationId 
        $GetServicePrincipal = Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id
        
        # Sleep here for a few seconds to allow the service principal application to become active (ordinarily takes a few seconds)
        Sleep -s 15
        $NewRole = New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
        $Retries = 0;
        While ($NewRole -eq $null -and $Retries -le 6) {
            Sleep -s 10
            New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
            $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
            $Retries++;
        }
        return $Application.ApplicationId.ToString();
    }
        
    function CreateAutomationCertificateAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $certifcateAssetName, [string] $certPath, [string] $certPlainPassword, [Boolean] $Exportable) {
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
        
    $AzureRMProfileVersion = (Get-Module AzureRM.Profile).Version
    if (!(($AzureRMProfileVersion.Major -ge 3 -and $AzureRMProfileVersion.Minor -ge 4) -or ($AzureRMProfileVersion.Major -gt 3))) {
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
    }
    else {
        $CertificateName = $AutomationAccountName + $CertifcateAssetName
        $PfxCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".pfx")
        $PfxCertPlainPasswordForRunAsAccount = $SelfSignedCertPlainPassword
        $CerCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".cer")
        CreateSelfSignedCertificate $CertificateName $PfxCertPlainPasswordForRunAsAccount $PfxCertPathForRunAsAccount $CerCertPathForRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
    }
        
    # Create a service principal
    $PfxCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($PfxCertPathForRunAsAccount, $PfxCertPlainPasswordForRunAsAccount)
    $ApplicationId = CreateServicePrincipal $PfxCert $ApplicationDisplayName
        
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
        "Log in to the Microsoft Azure portal (https://portal.azure.com) and select Subscriptions -> Management Certificates." + [Environment]::NewLine +
        "Then click Upload and upload the .cer format of #CERT#"
        
        if ($EnterpriseCertPathForClassicRunAsAccount -and $EnterpriseCertPlainPasswordForClassicRunAsAccount ) {
            $PfxCertPathForClassicRunAsAccount = $EnterpriseCertPathForClassicRunAsAccount
            $PfxCertPlainPasswordForClassicRunAsAccount = $EnterpriseCertPlainPasswordForClassicRunAsAccount
            $UploadMessage = $UploadMessage.Replace("#CERT#", $PfxCertPathForClassicRunAsAccount)
        }
        else {
            $ClassicRunAsAccountCertificateName = $AutomationAccountName + $ClassicRunAsAccountCertifcateAssetName
            $PfxCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".pfx")
            $PfxCertPlainPasswordForClassicRunAsAccount = $SelfSignedCertPlainPassword
            $CerCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".cer")
            $UploadMessage = $UploadMessage.Replace("#CERT#", $CerCertPathForClassicRunAsAccount)
            CreateSelfSignedCertificate $ClassicRunAsAccountCertificateName $PfxCertPlainPasswordForClassicRunAsAccount $PfxCertPathForClassicRunAsAccount $CerCertPathForClassicRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
        }
        
        # Create the Automation certificate asset
        CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountCertifcateAssetName $PfxCertPathForClassicRunAsAccount $PfxCertPlainPasswordForClassicRunAsAccount $false
        
        # Populate the ConnectionFieldValues
        $SubscriptionName = $subscription.Subscription.Name
        $ClassicRunAsAccountConnectionFieldValues = @{"SubscriptionName" = $SubscriptionName; "SubscriptionId" = $SubscriptionId; "CertificateAssetName" = $ClassicRunAsAccountCertifcateAssetName}
        
        # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
        CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountConnectionAssetName $ClassicRunAsAccountConnectionTypeName   $ClassicRunAsAccountConnectionFieldValues
        
        Write-Host -ForegroundColor red       $UploadMessage
    }
    ```

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
    > 执行脚本后，系统会提示在 Azure 上进行身份验证。 请以订阅管理员角色成员和订阅共同管理员的帐户登录。
    >
    >

成功执行脚本后，请注意以下事项：
* 如果使用自签名公共证书（.cer 文件）创建了经典运行方式帐户，该脚本将创建该帐户，并将其保存到计算机上用于执行 PowerShell 会话的用户配置文件下方的临时文件夹（*%USERPROFILE%\AppData\Local\Temp*）。
* 如果使用企业公共证书（.cer 文件）创建了经典运行方式帐户，则使用此证书。 遵循有关[将管理 API 证书上传到 Azure 门户](../azure-api-management-certs.md)的说明，然后参考[用于通过 Azure 经典部署资源进行身份验证的示例代码](automation-verify-runas-authentication.md#classic-run-as-authentication)，使用经典部署资源来验证凭据配置。 
* 如果*未*创建经典运行方式帐户，请参考[用于通过服务管理资源进行身份验证的示例代码](automation-verify-runas-authentication.md#automation-run-as-authentication)，使用 Resource Manager 资源进行身份验证并验证凭据配置。

## <a name="limiting-run-as-account-permissions"></a>限制运行方式帐户权限

为了控制针对 Azure 自动化中资源的自动化目标，默认情况下运行方式帐户会被授予订阅中的参与者权限。 如果需要限制运行方式服务主体可以执行的操作，可以从订阅的参与者角色中删除该帐户并将该帐户添加为要指定的资源组的参与者。

在 Azure 门户中，选择“订阅”并选择自动化帐户的订阅。 选择“访问控制(标识和访问管理)”，然后搜索自动化帐户的服务主体（类似于 \<AutomationAccountName\>_唯一标识符）。 选择该帐户，然后单击“删除”以从订阅中将其删除。

![订阅参与者](media/automation-create-runas-account/automation-account-remove-subscription.png)

若要将服务主体添加到资源组，请在 Azure 门户中选择资源组，然后选择“访问控制(标识和访问管理)”。 选择“添加”，这将打开“添加权限”页。 对于“角色”，选择“参与者”。 在“选择”文本框中，键入运行方式帐户的服务主体名称，并从列表中选择它。 单击“保存”以保存更改。 对要向其授予 Azure 自动化运行方式服务主体访问权限的资源组执行此操作。

## <a name="next-steps"></a>后续步骤
* 有关服务主体的详细信息，请参阅 [Application Objects and Service Principal Objects](../active-directory/active-directory-application-objects.md)（应用程序对象和服务主体对象）。
* 有关证书和 Azure 服务的详细信息，请参阅 [Azure 云服务证书概述](../cloud-services/cloud-services-certs-create.md)。
