---
author: erhopf
ms.author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 07/23/2019
ms.openlocfilehash: 3a6807cc204a5f8a6957bb03cf4dcbaf3611c17c
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/19/2019
ms.locfileid: "71148371"
---
## <a name="authenticate-with-azure-active-directory"></a>使用 Azure Active Directory 进行身份验证

> [!IMPORTANT]
> 目前，**只有**计算机视觉 API、人脸 API、文本分析 API 和沉浸式读取器支持使用 AZURE ACTIVE DIRECTORY （AAD）进行身份验证。

在前面的部分中，我们介绍了如何使用单个服务或多服务订阅密钥对 Azure 认知服务进行身份验证。 虽然这些密钥提供了快速且简单的开始开发的途径，但它们在需要基于角色的访问控制的更复杂方案中会短暂。 让我们看看使用 Azure Active Directory （AAD）进行身份验证所需的操作。

在以下部分中，你将使用 Azure Cloud Shell 环境或 Azure CLI 来创建子域、分配角色并获取持有者令牌以调用 Azure 认知服务。 如果遇到问题，每个部分都提供了链接，其中每个命令在 Azure Cloud Shell/Azure CLI 中可用。

### <a name="create-a-resource-with-a-custom-subdomain"></a>使用自定义子域创建资源

第一步是创建自定义子域。

1. 首先打开 Azure Cloud Shell。 然后[选择订阅](https://docs.microsoft.com/powershell/module/servicemanagement/azure/select-azuresubscription?view=azuresmps-4.0.0#description)：

   ```azurecli-interactive
   Select-AzureSubscription -SubscriptionName <YOUR_SUBCRIPTION>
   ```

2. 接下来，使用自定义子域[创建认知服务资源](https://docs.microsoft.com/powershell/module/az.cognitiveservices/new-azcognitiveservicesaccount?view=azps-1.8.0)。 子域名称需要全局唯一，并且不能包含特殊字符，如： "."、"！"、"，"。

   ```azurecli-interactive
   New-AzCognitiveServicesAccount -ResourceGroupName <RESOURCE_GROUP_NAME> -name <ACCOUNT_NAME> -Type <ACCOUNT_TYPE> -SkuName <SUBSCRIPTION_TYPE> -Location <REGION> -CustomSubdomainName <UNIQUE_SUBDOMAIN>
   ```

3. 如果成功，则**终结点**应显示资源独有的子域名称。


### <a name="assign-a-role-to-a-service-principal"></a>向服务主体分配角色

现在，你有了与资源关联的自定义子域，接下来需要为服务主体分配一个角色。

> [!NOTE]
> 请记住，AAD 角色分配可能需要长达五分钟才能传播。

1. 首先，让我们注册一个[AAD 应用程序](https://docs.microsoft.com/powershell/module/Az.Resources/New-AzADApplication?view=azps-1.8.0)。

   ```azurecli-interactive
   $SecureStringPassword = ConvertTo-SecureString -String <YOUR_PASSWORD> -AsPlainText -Force

   New-AzADApplication -DisplayName <APP_DISPLAY_NAME> -IdentifierUris <APP_URIS> -Password $SecureStringPassword
   ```

   下一步将需要**ApplicationId** 。

2. 接下来，需要为 AAD 应用程序[创建服务主体](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal?view=azps-1.8.0)。

   ```azurecli-interactive
   New-AzADServicePrincipal -ApplicationId <APPLICATION_ID>
   ```

   >[!NOTE]
   > 如果在 Azure 门户中注册应用程序，则会为你完成此步骤。

3. 最后一步是[将 "认知服务用户" 角色分配](https://docs.microsoft.com/powershell/module/az.Resources/New-azRoleAssignment?view=azps-1.8.0)给服务主体（作用域为资源）。 通过分配角色，你将授予服务主体对此资源的访问权限。 可以向同一服务主体授予对订阅中多个资源的访问权限。
   >[!NOTE]
   > 使用服务主体的 ObjectId，而不是应用程序的 ObjectId。
   > ACCOUNT_ID 将为你创建的认知服务帐户的 Azure 资源 Id。 可以从 Azure 门户中资源的 "属性" 中找到 Azure 资源 Id。

   ```azurecli-interactive
   New-AzRoleAssignment -ObjectId <SERVICE_PRINCIPAL_OBJECTID> -Scope <ACCOUNT_ID> -RoleDefinitionName "Cognitive Services User"
   ```

### <a name="sample-request"></a>示例请求

在此示例中，使用了一个密码对服务主体进行身份验证。 然后，将使用提供的令牌来调用计算机视觉 API。

1. 获取**TenantId**：
   ```azurecli-interactive
   $context=Get-AzContext
   $context.Tenant.Id
   ```

2. 获取令牌：
   ```azurecli-interactive
   $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList "https://login.windows.net/<TENANT_ID>"
   $secureSecretObject = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.SecureClientSecret" -ArgumentList $SecureStringPassword   
   $clientCredential = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential" -ArgumentList $app.ApplicationId, $secureSecretObject
   $token=$authContext.AcquireTokenAsync("https://cognitiveservices.azure.com/", $clientCredential).Result
   $token
   ```
3. 调用计算机视觉 API：
   ```azurecli-interactive
   $url = $account.Endpoint+"vision/v1.0/models"
   $result = Invoke-RestMethod -Uri $url  -Method Get -Headers @{"Authorization"=$token.CreateAuthorizationHeader()} -Verbose
   $result | ConvertTo-Json
   ```

或者，可以使用证书对服务主体进行身份验证。 除了服务主体以外，还支持通过其他 AAD 应用程序委派权限的用户主体。 在这种情况下，在获取令牌时，系统将提示用户提供双因素身份验证，而不是密码或证书。
