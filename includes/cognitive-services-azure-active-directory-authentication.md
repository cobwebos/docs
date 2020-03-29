---
author: erhopf
ms.author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 07/23/2019
ms.openlocfilehash: 8754504655cdd08c9bf9f89311cb6c5d1057f0e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78262721"
---
## <a name="authenticate-with-azure-active-directory"></a>使用 Azure Active Directory 进行身份验证

> [!IMPORTANT]
> 1. 目前，**只有**计算机视觉 API、人脸 API、文本分析 API、沉浸式读取器、表单识别器、异常检测器以及除必应自定义搜索支持使用 Azure 活动目录 （AAD） 身份验证之外的所有必应服务。
> 2. AAD 身份验证必须始终与 Azure 资源的自定义子域名一起使用。 [区域终结点](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains#is-there-a-list-of-regional-endpoints)不支持 AAD 身份验证。

在前面的章节中，我们演示如何使用单一服务或多服务订阅密钥对 Azure 认知服务进行身份验证。 虽然这些密钥提供了快速简便的开始开发路径，但它们在需要基于角色的访问控制的更复杂的方案中不足。 让我们来看看使用 Azure 活动目录 （AAD） 进行身份验证所需的内容。

在以下部分中，您将使用 Azure 云外壳环境或 Azure CLI 创建子域、分配角色并获取无记名令牌来调用 Azure 认知服务。 如果遇到问题，每个部分都会提供链接，并带有 Azure 云外壳/Azure CLI 中每个命令的所有可用选项。

### <a name="create-a-resource-with-a-custom-subdomain"></a>使用自定义子域创建资源

第一步是创建自定义子域。 如果要使用没有自定义子域名的现有认知服务资源，请按照[认知服务自定义子域](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains#how-does-this-impact-existing-resources)中的说明为资源启用自定义子域。

1. 首先打开 Azure 云外壳。 然后[选择订阅](https://docs.microsoft.com/powershell/module/az.accounts/set-azcontext?view=azps-3.3.0)：

   ```powershell-interactive
   Set-AzContext -SubscriptionName <SubscriptionName>
   ```

2. 接下来，使用自定义子域[创建认知服务资源](https://docs.microsoft.com/powershell/module/az.cognitiveservices/new-azcognitiveservicesaccount?view=azps-1.8.0)。 子域名需要全局唯一，不能包括特殊字符，例如："."，"！"

   ```powershell-interactive
   New-AzCognitiveServicesAccount -ResourceGroupName <RESOURCE_GROUP_NAME> -name <ACCOUNT_NAME> -Type <ACCOUNT_TYPE> -SkuName <SUBSCRIPTION_TYPE> -Location <REGION> -CustomSubdomainName <UNIQUE_SUBDOMAIN>
   ```

3. 如果成功，**终结点**应显示资源独有的子域名。


### <a name="assign-a-role-to-a-service-principal"></a>向服务主体分配角色

现在，您拥有了与资源关联的自定义子域，您需要将角色分配给服务主体。

> [!NOTE]
> 请记住，AAD 角色分配可能需要长达五分钟才能传播。

1. 首先，让我们注册一个[AAD应用程序](https://docs.microsoft.com/powershell/module/Az.Resources/New-AzADApplication?view=azps-1.8.0)。

   ```powershell-interactive
   $SecureStringPassword = ConvertTo-SecureString -String <YOUR_PASSWORD> -AsPlainText -Force

   New-AzADApplication -DisplayName <APP_DISPLAY_NAME> -IdentifierUris <APP_URIS> -Password $SecureStringPassword
   ```

   在下一步中，您将需要**应用程序 Id。**

2. 接下来，您需要为 AAD 应用程序[创建服务主体](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal?view=azps-1.8.0)。

   ```powershell-interactive
   New-AzADServicePrincipal -ApplicationId <APPLICATION_ID>
   ```

   >[!NOTE]
   > 如果在 Azure 门户中注册应用程序，则此步骤将完成。

3. 最后一步是将["认知服务用户"角色分配给](https://docs.microsoft.com/powershell/module/az.Resources/New-azRoleAssignment?view=azps-1.8.0)服务主体（范围限定为资源）。 通过分配角色，您将授予对此资源的服务主体访问权限。 您可以授予对订阅中的多个资源的相同服务主体访问权限。
   >[!NOTE]
   > 使用服务主体的 ObjectID，而不是应用程序的 ObjectId。
   > ACCOUNT_ID将是您创建的认知服务帐户的 Azure 资源 ID。 可以从 Azure 门户中资源的"属性"中找到 Azure 资源 ID。

   ```azurecli-interactive
   New-AzRoleAssignment -ObjectId <SERVICE_PRINCIPAL_OBJECTID> -Scope <ACCOUNT_ID> -RoleDefinitionName "Cognitive Services User"
   ```

### <a name="sample-request"></a>示例请求

在此示例中，密码用于验证服务主体。 然后，提供的令牌用于调用计算机视觉 API。

1. 获取**租户 Id**：
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

或者，可以使用证书对服务主体进行身份验证。 除了服务主体之外，用户主体还通过通过另一个 AAD 应用程序委派权限来提供支持。 在这种情况下，在获取令牌时，将提示用户进行双重身份验证，而不是密码或证书。
