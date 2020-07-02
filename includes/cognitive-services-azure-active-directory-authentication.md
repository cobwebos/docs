---
author: erhopf
ms.author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 05/11/2020
ms.openlocfilehash: 8948850734b310d70e7e457f075d13ade0c55619
ms.sourcegitcommit: 32592ba24c93aa9249f9bd1193ff157235f66d7e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2020
ms.locfileid: "85805378"
---
## <a name="authenticate-with-azure-active-directory"></a>使用 Azure Active Directory 进行身份验证

> [!IMPORTANT]
> 1. 目前，**只有**计算机视觉 API、人脸 API、文本分析 API、沉浸式读者、窗体识别器、异常探测器和所有必应服务（必应自定义搜索使用 AZURE ACTIVE DIRECTORY （AAD）支持身份验证除外）。
> 2. AAD 身份验证必须始终与 Azure 资源的自定义子域名一起使用。 [区域终结点](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains#is-there-a-list-of-regional-endpoints)不支持 AAD 身份验证。

在前面的部分中，我们演示了如何使用单一服务或多服务订阅密钥完成 Azure 认知服务的身份验证。 虽然这些密钥提供了一种快速简便的途径实现快速开始开发，但还不足以支持需要基于角色的访问控制的更复杂的方案。 让我们来看看使用 Azure Active Directory (AAD) 进行身份验证所需的条件。

在以下部分中，你将使用 Azure Cloud Shell 环境或 Azure CLI 来创建子域、分配角色并获取持有者令牌以调用 Azure 认知服务。 如果遇到问题，每个部分都提供了链接，其中每个命令在 Azure Cloud Shell/Azure CLI 中可用。

### <a name="create-a-resource-with-a-custom-subdomain"></a>使用自定义子域创建资源

第一步是创建自定义子域。 如果要使用没有自定义子域名的现有认知服务资源，请按照[认知服务自定义子域](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains#how-does-this-impact-existing-resources)中的说明为资源启用自定义子域。

1. 首先打开 Azure Cloud Shell。 然后[选择一个订阅](https://docs.microsoft.com/powershell/module/az.accounts/set-azcontext?view=azps-3.3.0)：

   ```powershell-interactive
   Set-AzContext -SubscriptionName <SubscriptionName>
   ```

2. 接下来，使用自定义子域[创建认知服务资源](https://docs.microsoft.com/powershell/module/az.cognitiveservices/new-azcognitiveservicesaccount?view=azps-1.8.0)。 子域名需为全局唯一，不能包括特殊字符，例如：“.”、“!”、“,”。

   ```powershell-interactive
   $account = New-AzCognitiveServicesAccount -ResourceGroupName <RESOURCE_GROUP_NAME> -name <ACCOUNT_NAME> -Type <ACCOUNT_TYPE> -SkuName <SUBSCRIPTION_TYPE> -Location <REGION> -CustomSubdomainName <UNIQUE_SUBDOMAIN>
   ```

3. 如果成功，Endpoint 应会显示资源独有的子域名****。


### <a name="assign-a-role-to-a-service-principal"></a>向服务主体分配角色

现在，已有了与资源关联的自定义子域，接着需要将角色分配给服务主体。

> [!NOTE]
> 请记住，AAD 角色分配可能需要最多五分钟的时间进行传播。

1. 首先，注册一个 [AAD 应用程序](https://docs.microsoft.com/powershell/module/Az.Resources/New-AzADApplication?view=azps-1.8.0)。

   ```powershell-interactive
   $SecureStringPassword = ConvertTo-SecureString -String <YOUR_PASSWORD> -AsPlainText -Force

   $app = New-AzADApplication -DisplayName <APP_DISPLAY_NAME> -IdentifierUris <APP_URIS> -Password $SecureStringPassword
   ```

   在下一步中，需要 ApplicationId****。

2. 接下来，需要为 AAD 应用程序[创建服务主体](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal?view=azps-1.8.0)。

   ```powershell-interactive
   New-AzADServicePrincipal -ApplicationId <APPLICATION_ID>
   ```

   >[!NOTE]
   > 如果在 Azure 门户中注册应用程序，将自动为你完成此步骤。

3. 最后一步是向服务主体[分配“认知服务用户”角色](https://docs.microsoft.com/powershell/module/az.Resources/New-azRoleAssignment?view=azps-1.8.0)（范围限定为资源）。 通过分配角色，将向服务主体授予对此资源的访问权限。 可以向服务主体授予对订阅中多个资源的访问权限。
   >[!NOTE]
   > 此过程使用服务主体的 ObjectId，而不是应用程序的 ObjectId。
   > ACCOUNT_ID 是所创建的认知服务帐户的 Azure 资源 ID。 可以从 Azure 门户中资源的“属性”中找到 Azure 资源 ID。

   ```azurecli-interactive
   New-AzRoleAssignment -ObjectId <SERVICE_PRINCIPAL_OBJECTID> -Scope <ACCOUNT_ID> -RoleDefinitionName "Cognitive Services User"
   ```

### <a name="sample-request"></a>示例请求

在此示例中，使用密码对服务主体进行身份验证。 然后，使用提供的令牌调用计算机视觉 API。

1. 获取 TenantId****：
   ```powershell-interactive
   $context=Get-AzContext
   $context.Tenant.Id
   ```

2. 获取令牌：
   > [!NOTE]
   > 如果使用 Azure Cloud Shell，则 `SecureClientSecret` 该类不可用。 

   #### <a name="poweershell"></a>[PoweerShell](#tab/powershell)
   ```powershell-interactive
   $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList "https://login.windows.net/<TENANT_ID>"
   $secureSecretObject = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.SecureClientSecret" -ArgumentList $SecureStringPassword   
   $clientCredential = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential" -ArgumentList $app.ApplicationId, $secureSecretObject
   $token=$authContext.AcquireTokenAsync("https://cognitiveservices.azure.com/", $clientCredential).Result
   $token
   ```
   
   #### <a name="azure-cloud-shell"></a>[Azure Cloud Shell](#tab/azure-cloud-shell)
   ```Azure Cloud Shell
   $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList "https://login.windows.net/<TENANT_ID>"
   $clientCredential = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential" -ArgumentList $app.ApplicationId, <YOUR_PASSWORD>
   $token=$authContext.AcquireTokenAsync("https://cognitiveservices.azure.com/", $clientCredential).Result
   $token
   ``` 

   ---

3. 调用计算机视觉 API：
   ```powershell-interactive
   $url = $account.Endpoint+"vision/v1.0/models"
   $result = Invoke-RestMethod -Uri $url  -Method Get -Headers @{"Authorization"=$token.CreateAuthorizationHeader()} -Verbose
   $result | ConvertTo-Json
   ```

或者，可以使用证书对服务主体进行身份验证。 除了服务主体之外，还通过使用另一个 AAD 应用程序来委派权限的方式提供用户主体支持。 在这种情况下，获取令牌时，将提示用户进行双重身份验证，而不是提供密码或证书。

## <a name="authorize-access-to-managed-identities"></a>授权访问托管标识
 
认知服务支持使用 [Azure 资源的托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)进行 Azure Active Directory (Azure AD) 身份验证。 Azure 资源的托管标识可以从 Azure 虚拟机 (VM)、函数应用、虚拟机规模集和其他服务中运行的应用程序使用 Azure AD 凭据授权对认知服务资源的访问权限。 将 Azure 资源的托管标识与 Azure AD 身份验证结合使用，可避免将凭据随在云中运行的应用程序一起存储。  

### <a name="enable-managed-identities-on-a-vm"></a>在 VM 上启用托管标识

在使用 Azure 资源的托管标识对 VM 中的认知服务资源授予访问权限之前，必须在 VM 上启用 Azure 资源的托管标识。 若要了解如何为 Azure 资源启用托管标识，请参阅：

- [Azure 门户](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm)
- [Azure CLI](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm)
- [Azure Resource Manager 模板](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm)
- [Azure 资源管理器客户端库](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm)

有关托管标识的详细信息，请参阅 [Azure 资源的托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)。
