---
title: 创建 Azure Stack 的服务主体 | Microsoft Docs
description: 介绍如何创建新的服务主体，并在 Azure 资源管理器中将此服务主体与基于角色的访问控制配合使用以管理对资源的访问权限。
services: azure-resource-manager
documentationcenter: na
author: mattbriggs
manager: femila
ms.assetid: 7068617b-ac5e-47b3-a1de-a18c918297b6
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/21/2018
ms.author: mabrigg
ms.openlocfilehash: 0db3f19c99b786d7f32f126ad7bd70efc999a751
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2018
ms.locfileid: "37444255"
---
# <a name="provide-applications-access-to-azure-stack"></a>提供对 Azure Stack 的应用程序访问权限

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

当应用程序需要在 Azure Stack 通过Azure 资源管理器部署或配置资源的访问权限时，请创建服务主体（它是应用程序的凭据）。  然后可以仅将必需的权限委派给该服务主体。  

例如，你可能有一个使用 Azure 资源管理器来清点 Azure 资源的配置管理工具。  在此方案中，可以创建服务主体，向该服务主体授予读者角色，并将配置管理工具限制为只能进行只读访问。 

与使用自己的凭据运行应用相比，服务主体更优，原因在于：

* 可以向服务主体分配不同于自己的帐户权限的权限。 通常情况下，这些权限仅限于应用需执行的操作。
* 职责变化时，无需更改应用的凭据。
* 执行无人参与的脚本时，可使用证书自动进行身份验证。  

## <a name="getting-started"></a>入门

根据部署 Azure Stack 的方式，可以首先创建服务主体。  本文档介绍如何为 [Azure Active Directory (Azure AD)](azure-stack-create-service-principals.md#create-service-principal-for-azure-ad) 和 [Active Directory 联合身份验证服务 (AD FS)](azure-stack-create-service-principals.md#create-service-principal-for-ad-fs) 创建服务主体。  创建服务主体后，将使用普遍适用于 AD FS 和 Azure Active Directory 的一组步骤向角色[委派权限](azure-stack-create-service-principals.md#assign-role-to-service-principal)。     

## <a name="create-service-principal-for-azure-ad"></a>为 Azure AD 创建服务主体

如果已使用 Azure AD 部署 Azure Stack 作为标识存储，则可以创建服务主体，就像对 Azure 所做的那样。  本部分演示如何通过门户执行这些步骤。  在开始之前，请检查是否具有[所需的 Azure AD 权限](../azure-resource-manager/resource-group-create-service-principal-portal.md#required-permissions)。

### <a name="create-service-principal"></a>创建服务主体
在本部分中，将在 Azure AD 中创建表示你的应用程序的应用程序（服务主体）。

1. 通过 [Azure 门户](https://portal.azure.com)登录到 Azure 帐户。
2. 选择“Azure Active Directory” > “应用注册” > “添加”   
3. 为应用提供名称和 URL。 选择“Web 应用/API”或“本机”作为要创建的应用程序的类型。 设置这些值后，选择“创建”。

已为应用程序创建服务主体。

### <a name="get-credentials"></a>获取凭据
以编程方式登录时，需要使用应用程序、Web 应用/API 的 ID 和身份验证密钥。 若要获取这些值，请使用以下步骤：

1. 从 Active Directory 中的“应用注册”，选择应用程序。

2. 复制“应用程序 ID”并将其存储在应用程序代码中。 [示例应用程序](#sample-applications)部分的应用程序引用此值作为客户端 ID。

     ![客户端 ID](./media/azure-stack-create-service-principal/image12.png)
3. 若要为 Web 应用/API 生成身份验证密钥，请选择“设置” > “密钥”。 

4. 提供密钥说明和密钥持续时间。 完成后，选择“保存”。

保存密钥后, 会显示密钥的值。 复制此值，因为稍后不能检索密钥。 提供密钥值及应用程序 ID 登录为该应用程序。 将密钥值存储在应用程序可检索的位置。

![保存的密钥](./media/azure-stack-create-service-principal/image15.png)


完成后，继续执行[为应用程序分配角色](azure-stack-create-service-principals.md#assign-role-to-service-principal)。

## <a name="create-service-principal-for-ad-fs"></a>为 AD FS 创建服务主体
如果已使用 AD FS 部署 Azure Stack，则可以使用 PowerShell 创建服务主体、为角色分配访问权限以及使用该标识从 PowerShell 登录。

从 ERCS 虚拟机上的特权终结点运行脚本。


要求：
- 需要证书。

**参数**

以下信息是作为自动化参数的输入所必需的：


|参数|说明|示例|
|---------|---------|---------|
|名称|SPN 帐户的名称|MyAPP|
|ClientCertificates|证书对象的数组|X509 证书|
|ClientRedirectUris<br>(可选)|应用程序重定向 URI|         |

**示例**

1. 打开权限提升的 Windows PowerShell 会话，并运行以下命令：

   > [!NOTE]
   > 此示例创建一个自签名证书。 在生产部署中运行这些命令时，使用 Get-Certificate 检索要使用的证书的证书对象。

   ```PowerShell  
    # Credential for accessing the ERCS PrivilegedEndpoint typically domain\cloudadmin
    $creds = Get-Credential

    # Creating a PSSession to the ERCS PrivilegedEndpoint
    $session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $creds

    # This produces a self signed cert for testing purposes.  It is prefered to use a managed certificate for this.
    $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<yourappname>" -KeySpec KeyExchange

    $ServicePrincipal = Invoke-Command -Session $session -ScriptBlock { New-GraphApplication -Name '<yourappname>' -ClientCertificates $using:cert}
    $AzureStackInfo = Invoke-Command -Session $session -ScriptBlock { get-azurestackstampinformation }
    $session|remove-pssession

    # For Azure Stack development kit, this value is set to https://management.local.azurestack.external. We will read this from the AzureStackStampInformation output of the ERCS VM.
    $ArmEndpoint = $AzureStackInfo.TenantExternalEndpoints.TenantResourceManager

    # For Azure Stack development kit, this value is set to https://graph.local.azurestack.external/. We will read this from the AzureStackStampInformation output of the ERCS VM.
    $GraphAudience = "https://graph." + $AzureStackInfo.ExternalDomainFQDN + "/"

    # TenantID for the stamp. We will read this from the AzureStackStampInformation output of the ERCS VM.
    $TenantID = $AzureStackInfo.AADTenantID

    # Register an AzureRM environment that targets your Azure Stack instance
    Add-AzureRMEnvironment `
    -Name "AzureStackUser" `
    -ArmEndpoint $ArmEndpoint

    # Set the GraphEndpointResourceId value
    Set-AzureRmEnvironment `
    -Name "AzureStackUser" `
    -GraphAudience $GraphAudience `
    -EnableAdfsAuthentication:$true

    Add-AzureRmAccount -EnvironmentName "azurestackuser" `
    -ServicePrincipal `
    -CertificateThumbprint $ServicePrincipal.Thumbprint `
    -ApplicationId $ServicePrincipal.ClientId `
    -TenantId $TenantID
   ```

2. 自动化完成后，它将显示使用该 SPN 所需的详细信息。 

   例如：

   ```
   ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
   ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
   Thumbprint            : 30202C11BE6864437B64CE36C8D988442082A0F1
   ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
   PSComputerName        : azs-ercs01
   RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
   ```

### <a name="assign-a-role"></a>分配角色
创建服务主体后，必须[将它分配到角色](azure-stack-create-service-principals.md#assign-role-to-service-principal)

### <a name="sign-in-through-powershell"></a>通过 PowerShell 登录
分配角色后，可以在以下命令中使用服务主体登录到 Azure Stack：

```powershell
Add-AzureRmAccount -EnvironmentName "<AzureStackEnvironmentName>" `
 -ServicePrincipal `
 -CertificateThumbprint $servicePrincipal.Thumbprint `
 -ApplicationId $servicePrincipal.ClientId ` 
 -TenantId $directoryTenantId
```

## <a name="assign-role-to-service-principal"></a>为服务主体分配角色
要访问订阅中的资源，必须将应用程序分配到角色。 决定哪个角色表示应用程序的相应权限。 若要了解有关可用角色的信息，请参阅 [RBAC：内置角色](../role-based-access-control/built-in-roles.md)。

可将作用域设置为订阅、资源组或资源级别。 较低级别的作用域将继承权限。 例如，将某个应用程序添加到资源组的“读取者”角色意味着该应用程序可以读取该资源组及其包含的所有资源。

1. 在 Azure Stack 门户中，导航到要将应用程序分配到的作用域级别。 例如，若要在订阅范围内分配角色，选择“订阅”。 可改为选择资源组或资源。

2. 选择特定订阅（资源组或资源），向其中分配应用程序。

     ![选择进行分配的订阅](./media/azure-stack-create-service-principal/image16.png)

3. 选择“访问控制 (IAM)”。

     ![选择访问权限](./media/azure-stack-create-service-principal/image17.png)

4. 选择 **添加** 。

5. 选择要分配到应用程序的角色。

6. 搜索你的应用程序，并选择它。

7. 选择“确定”完成角色分配。 该应用程序会显示在分配到该范围角色的用户列表中。

既然已创建服务主体并已分配角色，可以开始在应用程序中使用此服务主体访问 Azure Stack 资源。  

## <a name="next-steps"></a>后续步骤

[为 ADFS 添加用户](azure-stack-add-users-adfs.md)
[管理用户权限](azure-stack-manage-permissions.md)
