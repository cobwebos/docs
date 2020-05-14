---
author: erhopf
ms.author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 05/11/2020
ms.openlocfilehash: ddc61a0d0cb5a630282a9ba0589cef6fda29c4b5
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83343457"
---
## <a name="authenticate-with-azure-active-directory"></a>使用 Azure Active Directory 进行身份验证

> [!IMPORTANT]
> 1. 目前，**只有**计算机视觉 API、人脸 API、文本分析 API、沉浸式读者、窗体识别器、异常探测器和所有必应服务（必应自定义搜索使用 AZURE ACTIVE DIRECTORY （AAD）支持身份验证除外）。
> 2. AAD 身份验证需要始终与 Azure 资源的自定义子域名称一起使用。 [区域终结点](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains#is-there-a-list-of-regional-endpoints)不支持 AAD 身份验证。

在前面的部分中，我们介绍了如何使用单个服务或多服务订阅密钥对 Azure 认知服务进行身份验证。 虽然这些密钥提供了快速且简单的开始开发的途径，但它们在需要基于角色的访问控制的更复杂方案中会短暂。 让我们看看使用 Azure Active Directory （AAD）进行身份验证所需的操作。

在以下部分中，你将使用 Azure Cloud Shell 环境或 Azure CLI 来创建子域、分配角色并获取持有者令牌以调用 Azure 认知服务。 如果遇到问题，每个部分都提供了链接，其中每个命令在 Azure Cloud Shell/Azure CLI 中可用。

### <a name="create-a-resource-with-a-custom-subdomain"></a>使用自定义子域创建资源

第一步是创建自定义子域。 如果要使用没有自定义子域名称的现有认知服务资源，请按照[认知服务自定义子域](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains#how-does-this-impact-existing-resources)中的说明为资源启用自定义子域。

1. 首先打开 Azure Cloud Shell。 然后[选择订阅](https://docs.microsoft.com/powershell/module/az.accounts/set-azcontext?view=azps-3.3.0)：

   ```powershell-interactive
   Set-AzContext -SubscriptionName <SubscriptionName>
   ```

2. 接下来，使用自定义子域[创建认知服务资源](https://docs.microsoft.com/powershell/module/az.cognitiveservices/new-azcognitiveservicesaccount?view=azps-1.8.0)。 子域名称需要全局唯一，并且不能包含特殊字符，如： "."、"！"、"，"。

   ```powershell-interactive
   New-AzCognitiveServicesAccount -ResourceGroupName <RESOURCE_GROUP_NAME> -name <ACCOUNT_NAME> -Type <ACCOUNT_TYPE> -SkuName <SUBSCRIPTION_TYPE> -Location <REGION> -CustomSubdomainName <UNIQUE_SUBDOMAIN>
   ```

3. 如果成功，则**终结点**应显示资源独有的子域名称。


### <a name="assign-a-role-to-a-service-principal"></a>向服务主体分配角色

现在，你有了与资源关联的自定义子域，接下来需要为服务主体分配一个角色。

> [!NOTE]
> 请记住，AAD 角色分配可能需要长达五分钟才能传播。

1. 首先，让我们注册一个[AAD 应用程序](https://docs.microsoft.com/powershell/module/Az.Resources/New-AzADApplication?view=azps-1.8.0)。

   ```powershell-interactive
   $SecureStringPassword = ConvertTo-SecureString -String <YOUR_PASSWORD> -AsPlainText -Force

   New-AzADApplication -DisplayName <APP_DISPLAY_NAME> -IdentifierUris <APP_URIS> -Password $SecureStringPassword
   ```

   下一步将需要**ApplicationId** 。

2. 接下来，需要为 AAD 应用程序[创建服务主体](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal?view=azps-1.8.0)。

   ```powershell-interactive
   New-AzADServicePrincipal -ApplicationId <APPLICATION_ID>
   ```

   >[!NOTE]
   > 如果在 Azure 门户中注册应用程序，则会为你完成此步骤。

3. 最后一步是[将 "认知服务用户" 角色分配](https://docs.microsoft.com/powershell/module/az.Resources/New-azRoleAssignment?view=azps-1.8.0)给服务主体（作用域为资源）。 通过分配角色，你将授予服务主体对此资源的访问权限。 可以向同一服务主体授予对订阅中多个资源的访问权限。
   >[!NOTE]
   > 使用服务主体的 ObjectId，而不是应用程序的 ObjectId。
   > ACCOUNT_ID 将是你创建的认知服务帐户的 Azure 资源 Id。 可以从 Azure 门户中资源的 "属性" 中找到 Azure 资源 Id。

   ```azurecli-interactive
   New-AzRoleAssignment -ObjectId <SERVICE_PRINCIPAL_OBJECTID> -Scope <ACCOUNT_ID> -RoleDefinitionName "Cognitive Services User"
   ```

### <a name="sample-request"></a>示例请求

在此示例中，使用了一个密码对服务主体进行身份验证。 然后，将使用提供的令牌来调用计算机视觉 API。

1. 获取**TenantId**：
   ```powershell-interactive
   $context=Get-AzContext
   $context.Tenant.Id
   ```

2. 获取令牌：
   ```powershell-interactive
   $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList "https://login.windows.net/<TENANT_ID>"
   $secureSecretObject = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.SecureClientSecret" -ArgumentList $SecureStringPassword   
   $clientCredential = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential" -ArgumentList $app.ApplicationId, $secureSecretObject
   $token=$authContext.AcquireTokenAsync("https://cognitiveservices.azure.com/", $clientCredential).Result
   $token
   ```
3. 调用计算机视觉 API：
   ```powershell-interactive
   $url = $account.Endpoint+"vision/v1.0/models"
   $result = Invoke-RestMethod -Uri $url  -Method Get -Headers @{"Authorization"=$token.CreateAuthorizationHeader()} -Verbose
   $result | ConvertTo-Json
   ```

或者，可以使用证书对服务主体进行身份验证。 除了服务主体以外，还支持通过其他 AAD 应用程序委派权限的用户主体。 在这种情况下，在获取令牌时，系统将提示用户提供双因素身份验证，而不是密码或证书。

## <a name="authorize-access-to-managed-identities"></a>授权访问托管标识
 
认知服务支持[对 Azure 资源的托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)进行 Azure Active Directory （Azure AD）身份验证。 Azure 资源的托管标识可以使用 Azure 虚拟机（Vm）、函数应用、虚拟机规模集和其他服务中运行的应用程序的 Azure AD 凭据，授权访问认知服务资源。 将 Azure 资源的托管标识与 Azure AD 身份验证结合使用，可避免将凭据随在云中运行的应用程序一起存储。  

### <a name="enable-managed-identities-on-a-vm"></a>在 VM 上启用托管标识

你必须在 VM 上为 Azure 资源启用托管标识，然后才能使用 Azure 资源的托管标识来授予对虚拟机中的认知服务资源的访问权限。 若要了解如何启用 Azure 资源的托管标识，请参阅：

- [Azure 门户](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm)
- [Azure CLI](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm)
- [Azure Resource Manager 模板](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm)
- [Azure 资源管理器客户端库](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm)

有关托管标识的详细信息，请参阅 [Azure 资源的托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)。
