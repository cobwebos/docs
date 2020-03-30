---
title: 管理 Azure 自动化运行方式帐户
description: 本文介绍如何使用 PowerShell 或门户管理运行方式帐户。
services: automation
ms.subservice: shared-capabilities
ms.date: 05/24/2019
ms.topic: conceptual
ms.openlocfilehash: 8d7d0baacd5f702e8f435ab440eaf0338a60f4cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79500769"
---
# <a name="manage-azure-automation-run-as-accounts"></a>管理 Azure 自动化运行方式帐户

在 Azure 自动化中运行为帐户提供使用 Azure cmdlet 管理 Azure 中的资源的身份验证。 创建"运行为"帐户时，它会在 Azure 活动目录 （AD） 中创建新的服务主体用户，并在订阅级别将参与者角色分配给此用户。 对于在 Azure 虚拟机上使用混合 Runbook 辅助角色的 Runbook，可以使用 [Azure 资源托管标识](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources)而不是运行方式帐户来对 Azure 资源进行身份验证。

默认情况下，"作为帐户运行"的服务主体没有读取 Azure AD 的权限。 如果要添加读取或管理 Azure AD 的权限，则需要在**API 权限**下授予服务主体的权限。 若要了解详细信息，请参阅[添加用于访问 Web API 的权限](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)。

>[!NOTE]
>本文进行了更新，以便使用新的 Azure PowerShell Az 模块。 你仍然可以使用 AzureRM 模块，至少在 2020 年 12 月之前，它将继续接收 bug 修补程序。 若要详细了解新的 Az 模块和 AzureRM 兼容性，请参阅[新 Azure Powershell Az 模块简介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有关混合 Runbook 辅助角色上的 Az 模块安装说明，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 对于自动化帐户，可以使用["如何更新 Azure 自动化 中的 Azure PowerShell"模块](automation-update-azure-modules.md)将模块更新到最新版本。

## <a name="types-of-run-as-accounts"></a>作为帐户运行的类型

Azure 自动化使用两种类型的"以帐户形式运行"：

* Azure 作为帐户运行
* Azure 经典作为帐户运行

>[!NOTE]
>Azure 云解决方案提供程序 （CSP） 订阅仅支持 Azure 资源管理器模型。 非 Azure 资源管理器服务在程序中不可用。 使用 CSP 订阅时，不会创建 Azure 经典运行为帐户，而是创建 Azure 运行作为帐户。 若要了解有关 CSP 订阅的详细信息，请参阅 [CSP 订阅中可用的服务](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services)。

### <a name="run-as-account"></a>运行方式帐户

"运行为帐户"管理[资源管理器部署模型](../azure-resource-manager/management/deployment-models.md)资源。 它执行以下任务。

* 将创建使用自签名证书的 Azure AD 应用程序，在 Azure AD 中为此应用程序创建服务主体帐户，并在当前订阅中为此帐户分配“参与者”角色。 您可以将证书设置更改为所有者或任何其他角色。 有关详细信息，请参阅 [Azure 自动化中基于角色的访问控制](automation-role-based-access-control.md)。
  
* 创建指定自动化帐户中指定的`AzureRunAsCertificate`自动化证书资产。 证书资产包含 Azure AD 应用程序使用的证书私钥。
  
* 创建指定自动化帐户中指定的`AzureRunAsConnection`自动化连接资产。 连接资产包含应用程序 ID、租户 ID、订阅 ID 和证书指纹。

### <a name="azure-classic-run-as-account"></a>Azure 经典运行方式帐户

Azure 经典运行作为帐户管理[经典部署模型](../azure-resource-manager/management/deployment-models.md)资源。 您必须是订阅上的共同管理员才能创建或续订此类帐户。

Azure 经典运行为帐户执行以下任务。

  * 在订阅中创建管理证书。

  * 创建指定自动化帐户中指定的`AzureClassicRunAsCertificate`自动化证书资产。 该证书资产保存管理证书使用的证书私钥。

  * 创建指定自动化帐户中指定的`AzureClassicRunAsConnection`自动化连接资产。 连接资产包含订阅名称、订阅 ID 和证书资产名称。

## <a name="run-as-account-permissions"></a><a name="permissions"></a>作为帐户权限运行

本节定义常规"以帐户身份运行"和"经典作为帐户运行"的权限。

### <a name="permissions-to-configure-run-as-accounts"></a>配置运行方式帐户时所需的权限

若要创建或更新运行方式帐户，必须拥有特定的特权和权限。 Azure Active Directory 中的应用程序管理员和订阅中的所有者可以完成所有任务。 在具有职责分离的情况下，下表将显示任务列表、等效 cmdlet 和所需的权限：

|任务|Cmdlet  |最低权限  |设置权限的位置|
|---|---------|---------|---|
|创建 Azure AD 应用程序|[新阿达应用程序](https://docs.microsoft.com/powershell/module/az.resources/new-azadapplication?view=azps-3.5.0)     | 应用程序开发人员角色<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>主页 > Azure AD >应用注册 |
|将凭据添加到应用程序。|[新阿达帕西](https://docs.microsoft.com/powershell/module/az.resources/new-azadappcredential?view=azps-3.5.0)     | 应用程序管理员或全局管理员<sup>1</sup>         |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>主页 > Azure AD >应用注册|
|创建和获取 Azure AD 服务主体|[New-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal?view=azps-3.5.0)</br>[获取 Aad 服务原则](https://docs.microsoft.com/powershell/module/az.resources/get-azadserviceprincipal?view=azps-3.5.0)     | 应用程序管理员或全局管理员<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>主页 > Azure AD >应用注册|
|分配或获取指定主体的 RBAC 角色|[New-AzRoleAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azroleassignment?view=azps-3.5.0)</br>[Get-AzRoleAssignment](https://docs.microsoft.com/powershell/module/Az.Resources/Get-AzRoleAssignment?view=azps-3.5.0)      | 用户访问管理员或所有者，或具有以下权限：</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br> | [订阅](../role-based-access-control/role-assignments-portal.md)</br>“主页”>“订阅”> \<订阅名称\> -“访问控制(IAM)”|
|创建或删除自动化证书|[新阿兹自动化证书](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationCertificate?view=azps-3.5.0)</br>[删除-阿兹自动化证书](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationcertificate?view=azps-3.5.0)     | 资源组的参与者         |自动化帐户资源组|
|创建或删除自动化连接|[新-阿兹自动化连接](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationconnection?view=azps-3.5.0)</br>[删除-阿兹自动化连接](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationconnection?view=azps-3.5.0)|资源组的参与者 |自动化帐户资源组|

<sup>1</sup>如果 Azure AD 租户的用户可以在"用户设置"页上**注册应用程序**选项设置为 **"是**"，则 Azure AD 租户中的 1 个非管理员用户可以[注册 AD 应用程序](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)。 如果应用程序注册设置**为"否**"，则执行此操作的用户必须如此表中的定义。

如果在添加到订阅的全局管理员角色之前，您不是订阅的 Active Directory 实例的成员，则将添加为来宾。 在这种情况下，“添加自动化帐户”页上会显示 `You do not have permissions to create…` 警告。 

如果在分配全局管理员角色时是订阅的活动目录实例的成员，您还可以在"添加自动化帐户"页上收到`You do not have permissions to create…`警告。 在这种情况下，您可以请求从订阅的 Active Directory 实例中删除，然后请求重新添加，以便成为 Active Directory 中的完整用户。

要验证生成错误消息的情况已得到纠正：

1. 从 Azure 门户中的 Azure 活动目录窗格中，选择 **"用户"和"组**"。 
2. 选择**所有用户**。
3. 选择您的姓名，然后选择**配置文件**。 
4. 确保用户配置文件下**的用户类型**属性的值未设置为**来宾**。

### <a name="permissions-to-configure-classic-run-as-accounts"></a><a name="permissions-classic"></a>配置经典运行方式帐户时所需的权限

若要配置或续订经典运行方式帐户，必须在订阅级别具有共同管理员角色。 要了解有关经典订阅权限的更多信息，请参阅[Azure 经典订阅管理员](../role-based-access-control/classic-administrators.md#add-a-co-administrator)。

## <a name="creating-a-run-as-account-in-azure-portal"></a>在 Azure 门户中创建"运行为帐户"帐户

执行以下步骤以在 Azure 门户中更新 Azure 自动化帐户。 单独创建"以"身份运行"和"经典运行为帐户"。 如果不需管理经典资源，可以只创建 Azure 运行方式帐户。

1. 以订阅管理员角色成员和订阅共同管理员的帐户登录到 Azure 门户。
2. 搜索并选择**自动化帐户**。
3. 在"自动化帐户"页上，从列表中选择您的自动化帐户。
4. 在左侧窗格中，在"帐户设置"部分中选择"**以帐户身份运行**"。
5. 根据所需帐户，选择“Azure 运行方式帐户”或“Azure 经典运行方式帐户”********。 
6. 根据感兴趣的帐户，使用 **"添加 Azure 运行为"** 或 **"添加 Azure 经典运行帐户"** 窗格。 查看概述信息后，单击"**创建**"。
6. 在 Azure 创建运行方式帐户时，可以在菜单的“通知”下面跟踪进度****。 还会显示一个横幅，说明正在创建帐户。 此过程可能需要几分钟才能完成。

## <a name="creating-a-run-as-account-using-powershell"></a>使用 PowerShell 创建"作为运行"帐户

以下列表提供了在 PowerShell 中创建"运行为帐户"的要求。 这些要求适用于这两种类型的"运行为帐户"。

* 装有 Azure 资源管理器模块 3.4.1 和更高版本的 Windows 10 或 Windows Server 2016。 PowerShell 脚本不支持早期版本的 Windows。
* Azure PowerShell 1.0 和更高版本。 有关 PowerShell 1.0 版本的信息，请参阅[如何安装和配置 Azure PowerShell](/powershell/azureps-cmdlets-docs)。
* 自动化帐户，该帐户被引用为`AutomationAccountName`和`ApplicationDisplayName`参数的值。
* 权限等效于["必需权限"中列出的用于配置"作为运行帐户"的权限](#permissions)。

要获取`SubscriptionId`PowerShell`ResourceGroupName`脚本所需的 参数 的 的值， 完成后续步骤。

1. 在 Azure 门户中，选择 **"自动化帐户**"。
1. 在"自动化帐户"页上，选择您的自动化帐户。
1. 在"帐户设置"部分中，选择 **"属性**"。
1. 请注意"属性"页上**名称**、**订阅 ID**和**资源组**的值。 这些值分别对应于`AutomationAccountName`、`SubscriptionId`和`ResourceGroupName`PowerShell 脚本参数的值。

   ![自动化帐户属性页](media/manage-runas-account/automation-account-properties.png)

### <a name="powershell-script-to-create-a-run-as-account"></a>创建"运行作为"帐户的 PowerShell 脚本

本节提供一个 PowerShell 脚本来创建"运行即"帐户。 该脚本包括对多种配置的支持。

* 使用自签名证书创建运行方式帐户。
* 使用自签名证书创建运行方式帐户和经典运行方式帐户。
* 使用企业证书颁发机构 (CA) 颁发的证书创建运行方式帐户和经典运行方式帐户。
* 在 Azure 政府版云中使用自签名证书创建运行方式帐户和经典运行方式帐户。

该脚本使用多个 Azure 资源管理器 cmdlet 来创建资源。 有关 cmdlet 及其所需的权限，请参阅[配置"作为帐户运行"的权限](#permissions-to-configure-run-as-accounts)。

使用文件名**New-RunAsAccount.ps1**将脚本保存在计算机上。

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
        [string] $EnterpriseCertPathForRunAsAccount,

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

        # Create an Azure AD application, AD App Credential, AD ServicePrincipal

        # Requires Application Developer Role, but works with Application administrator or GLOBAL ADMIN
        $Application = New-AzADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $applicationDisplayName) -IdentifierUris ("http://" + $keyId)
        # Requires Application administrator or GLOBAL ADMIN
        $ApplicationCredential = New-AzADAppCredential -ApplicationId $Application.ApplicationId -CertValue $keyValue -StartDate $PfxCert.NotBefore -EndDate $PfxCert.NotAfter
        # Requires Application administrator or GLOBAL ADMIN
        $ServicePrincipal = New-AzADServicePrincipal -ApplicationId $Application.ApplicationId
        $GetServicePrincipal = Get-AzADServicePrincipal -ObjectId $ServicePrincipal.Id

        # Sleep here for a few seconds to allow the service principal application to become active (ordinarily takes a few seconds)
        Sleep -s 15
        # Requires User Access Administrator or Owner.
        $NewRole = New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
        $Retries = 0;
        While ($NewRole -eq $null -and $Retries -le 6) {
            Sleep -s 10
            New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
            $NewRole = Get-AzRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
            $Retries++;
        }
        return $Application.ApplicationId.ToString();
    }

    function CreateAutomationCertificateAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $certifcateAssetName, [string] $certPath, [string] $certPlainPassword, [Boolean] $Exportable) {
        $CertPassword = ConvertTo-SecureString $certPlainPassword -AsPlainText -Force
        Remove-AzAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $certifcateAssetName -ErrorAction SilentlyContinue
        New-AzAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Path $certPath -Name $certifcateAssetName -Password $CertPassword -Exportable:$Exportable  | write-verbose
    }

    function CreateAutomationConnectionAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $connectionAssetName, [string] $connectionTypeName, [System.Collections.Hashtable] $connectionFieldValues ) {
        Remove-AzAutomationConnection -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -Force -ErrorAction SilentlyContinue
        New-AzAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -ConnectionTypeName $connectionTypeName -ConnectionFieldValues $connectionFieldValues
    }

    Import-Module AzureRm.Profile
    Import-Module AzureRm.Resources

    $AureRmProfileVersion = (Get-Module AzureRm.Profile).Version
    if (!(($AzureRmProfileVersion.Major -ge 3 -and $AzureRmProfileVersion.Minor -ge 4) -or ($AzureRmProfileVersion.Major -gt 3))) {
        Write-Error -Message "Please install the latest Azure PowerShell and retry. Relevant doc url : https://docs.microsoft.com/powershell/azureps-cmdlets-docs/ "
        return
    }

    # To use the new Az modules to create your Run As accounts, please uncomment the following lines and ensure you comment out the previous 8 lines that import the AzureRM modules to avoid any issues. To learn about about using Az modules in your Automation account see https://docs.microsoft.com/azure/automation/az-modules.

    # Import-Module Az.Automation
    # Enable-AzureRmAlias


    Connect-AzAccount -Environment $EnvironmentName
    $Subscription = Get-AzSubscription -SubscriptionId $SubscriptionId

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
    $SubscriptionInfo = Get-AzSubscription -SubscriptionId $SubscriptionId
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

>[!NOTE]
>`Add-AzAccount`是`Add-AzureRMAccount` [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0)的别名 。 您可以使用这些 cmdlet，也可以将自动化帐户中的[模块更新](automation-update-azure-modules.md)到最新版本。 即使您刚刚创建了新的自动化帐户，您也可能需要更新模块。

### <a name="execute-the-powershell-script"></a>执行 PowerShell 脚本

1. 在计算机上，从“开始”屏幕以提升的用户权限启动 **Windows PowerShell******。
1. 从提升的命令行 shell 转到包含脚本的文件夹。
1. 使用所需的配置的参数值执行脚本。
1. 如果创建经典运行为帐户，则在执行脚本后，请将公共证书 **（.cer**文件名扩展名）上载到创建自动化帐户的订阅的管理存储。

脚本执行后，系统将提示您使用 Azure 进行身份验证。 使用订阅管理员角色的成员和订阅的共同管理员的帐户登录。

#### <a name="create-a-run-as-account-by-using-a-self-signed-certificate"></a>使用自签名证书创建运行方式帐户

```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false
```

#### <a name="create-a-run-as-account-and-a-classic-run-as-account-by-using-a-self-signed-certificate"></a>使用自签名证书创建运行方式帐户和经典运行方式帐户

```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true
```

#### <a name="create-a-run-as-account-and-a-classic-run-as-account-by-using-an-enterprise-certificate"></a>使用企业证书创建运行方式帐户和经典运行方式帐户

```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>
```

如果您已使用企业公共证书 **（.cer**文件）创建了经典运行作为帐户，请使用此证书。 请参阅[将管理 API 证书上载到 Azure 门户](../azure-api-management-certs.md)。

#### <a name="create-a-run-as-account-and-a-classic-run-as-account-by-using-a-self-signed-certificate-in-the-azure-government-cloud"></a>在 Azure 政府版云中使用自签名证书创建运行方式帐户和经典运行方式帐户

```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true  -EnvironmentName AzureUSGovernment
```

如果您已创建具有自签名公共证书 **（.cer**文件） 的经典运行帐户，脚本将创建该帐户并将其保存到计算机上的临时文件文件夹中。 它可以在用户配置文件`%USERPROFILE%\AppData\Local\Temp`中找到 ，用于执行 PowerShell 会话。

## <a name="deleting-a-run-as-or-classic-run-as-account"></a>删除"运行为"或"经典运行为帐户"

本节介绍如何删除"运行为"或"经典运行为帐户"。 执行此操作时，将保留自动化帐户。 删除帐户后，可以在 Azure 门户中重新创建它。

1. 在 Azure 门户中，打开自动化帐户。

2. 在左侧窗格中，在"帐户设置"部分中选择"**以帐户身份运行**"。

3. 在“运行方式帐户”属性页上，选择要删除的运行方式帐户或经典运行方式帐户。 

4. 在所选帐户的"属性"窗格上，单击"**删除**"。

   ![删除运行方式帐户](media/manage-runas-account/automation-account-delete-runas.png)

5. 帐户删除过程中，可以在菜单的“通知”**** 下面跟踪进度。

6. 删除该帐户后，可以通过在“运行方式帐户”属性页中选择创建选项“Azure 运行方式帐户”来重新创建该帐户。****

   ![重新创建自动化运行方式帐户](media/manage-runas-account/automation-account-create-runas.png)

## <a name="renewing-a-self-signed-certificate"></a><a name="cert-renewal"></a>续订自签名证书

您为"运行"帐户创建的自签名证书自创建之日起一年后过期。 在"运行作为"帐户过期之前的某个时间点，必须续订证书。 您可以随时续订它。 

续订自签名证书时，将保留当前有效证书，以确保排队或主动运行以及使用 Run As 帐户进行身份验证的任何 Runbook 不会受到负面影响。 该证书在过期之前将保持有效。

>[!NOTE]
>如果您认为"运行"帐户已泄露，则可以删除并重新创建自签名证书。

>[!NOTE]
>如果已将 Run As 帐户配置为使用企业证书颁发机构颁发的证书，并且使用该选项续订自签名证书选项，则企业证书将被自签名证书替换。

使用以下步骤续订自签名证书。

1. 在 Azure 门户中，打开自动化帐户。

1. 在"帐户设置"部分中选择 **"以帐户身份运行**"。

    ![自动化帐户属性窗格](media/manage-runas-account/automation-account-properties-pane.png)

1. 在"以帐户身份运行"属性页上，选择"以"身份运行"帐户或续订证书的经典"作为"帐户"。

1. 在所选帐户的属性窗格上，单击"**续订证书**"。

    ![续订运行方式帐户的证书](media/manage-runas-account/automation-account-renew-runas-certificate.png)

1. 证书续订过程中，可以在菜单的“通知”**** 下面跟踪进度。

## <a name="setting-up-automatic-certificate-renewal-with-an-automation-runbook"></a><a name="auto-cert-renewal"></a>使用自动化 Runbook 设置自动证书续订

若要自动续订证书，可以使用自动化 Runbook。 [GitHub](https://github.com/ikanni/PowerShellScripts/blob/master/AzureAutomation/RunAsAccount/GrantPermissionToRunAsAccountAADApplication-ToRenewCertificateItself-CreateSchedule.ps1)上的此脚本可在您的自动化帐户中启用此功能。

>[!NOTE]
>您必须是 Azure AD 中的全局管理员或公司管理员才能执行脚本。

此脚本创建每周计划以续订"运行为帐户"证书。 它将**更新-自动化运行证书**运行簿添加到您的自动化帐户。 您可以在 GitHub 上查看运行簿代码，在脚本[更新-自动化RunAsCredential.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AutomationRunAsCredential.ps1)中查看。 您可以根据需要使用文件中的 PowerShell 代码手动续订证书。

使用以下步骤立即测试续订过程。

1. 编辑**更新-自动化RunAsCredential**运行簿，并在 **"退出（1）"** 命令的前面第 122 行放置注释字符 （#）。

   ```powershell
   #Exit(1)
   ```

2. 发布 Runbook。
3. 启动 Runbook。
4. 使用以下代码验证续订是否成功：

   ```powershell
   (Get-AzAutomationCertificate -AutomationAccountName TestAA
                                -Name AzureRunAsCertificate
                                -ResourceGroupName TestAutomation).ExpiryTime.DateTime
   ```
    输出：

   ```Output
   Thursday, November 7, 2019 7:00:00 PM
   ```

5. 测试后，编辑 Runbook，并删除在步骤 1 中添加的注释字符。
6. 发布 Runbook。

## <a name="limiting-run-as-account-permissions"></a><a name="limiting-run-as-account-permissions"></a>限制运行方式帐户权限

要控制针对 Azure 中的资源的自动化目标，可以运行[更新-自动化RunAsAccountRole任务.ps1](https://aka.ms/AA5hug8)脚本。 此脚本更改现有的"运行为帐户服务主体"以创建和使用自定义角色定义。 该角色具有除[密钥保管库](https://docs.microsoft.com/azure/key-vault/)之外的所有资源的权限。

>[!IMPORTANT]
>运行**更新-自动化运行运行会计角色分配.ps1**脚本后，使用"运行为帐户"访问密钥保管库的 Runbook 将不再工作。 在运行脚本之前，应查看帐户中的 Runbook 以访问 Azure 密钥保管库。 要启用从 Azure 自动化运行簿访问密钥保管库，必须[将"运行为"帐户添加到密钥保管库的权限](#add-permissions-to-key-vault)。

如果需要限制运行为服务主体可以执行的其他操作，可以将其他资源类型添加到自定义角色定义`NotActions`的元素。 以下示例限制对 `Microsoft.Compute/*` 的访问。 如果`NotActions`为此资源类型添加到角色定义中，该角色将无法访问任何计算资源。 若要详细了解角色定义，请参阅[了解 Azure 资源的角色定义](../role-based-access-control/role-definitions.md)。

```powershell
$roleDefinition = Get-AzRoleDefinition -Name 'Automation RunAs Contributor'
$roleDefinition.NotActions.Add("Microsoft.Compute/*")
$roleDefinition | Set-AzRoleDefinition
```

您可以确定 Run As 帐户使用的服务主体是"参与者角色"定义还是自定义角色定义。 

1. 转到您的自动化帐户，并在帐户设置部分中选择"**以帐户身份运行**"。
2. 选择**Azure 以帐户身份运行**。 
3. 选择**角色**以查找正在使用的角色定义。

[![](media/manage-runas-account/verify-role.png "Verify the Run As Account role")](media/manage-runas-account/verify-role-expanded.png#lightbox)

您还可以确定"运行为多个订阅或自动化帐户运行为帐户"使用的角色定义。 通过使用 PowerShell 库中的["检查-自动化运行帐户角色分配.ps1"](https://aka.ms/AA5hug5)脚本来执行此操作。

### <a name="add-permissions-to-key-vault"></a>将权限添加到 Key Vault

您可以允许 Azure 自动化验证密钥保管库和运行为帐户服务主体是否使用自定义角色定义。 必须：

* 向密钥保管库授予权限。
* 设置访问策略。

您可以使用 PowerShell 库中的[扩展-自动化运行帐户分配给 KeyVault.ps1](https://aka.ms/AA5hugb)脚本，将您的"作为运行"帐户权限授予密钥保管库。 有关在密钥保管库上设置权限的更多详细信息，请参阅[授予应用程序对密钥保管库](../key-vault/key-vault-group-permissions-for-apps.md)的访问。

## <a name="resolving-misconfiguration-issues-for-run-as-accounts"></a>解决"以帐户身份运行"的配置错误问题

在初始设置期间，运行为"作为"或"经典运行为帐户"帐户所需的某些配置项可能已删除或创建不当。 可能配置错误的情况包括：

* 证书资产
* 连接资产
* 从"参与者"角色中删除"作为帐户运行"
* Azure AD 中的服务主体或应用程序

对于此类配置错误实例，自动化帐户检测更改，并在帐户的"运行为帐户"`Incomplete`属性窗格中显示 状态。

![不完整的运行方式帐户配置状态](media/manage-runas-account/automation-account-runas-incomplete-config.png)

选择"按身份运行"帐户时，帐户属性窗格将显示以下错误消息：

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

可通过删除并重新创建运行方式帐户来快速解决该帐户的问题。

## <a name="next-steps"></a>后续步骤

* 有关服务主体的详细信息，请参阅[应用程序对象和服务主体对象](../active-directory/develop/app-objects-and-service-principals.md)。
* 有关证书和 Azure 服务的详细信息，请参阅 [Azure 云服务证书概述](../cloud-services/cloud-services-certs-create.md)。
