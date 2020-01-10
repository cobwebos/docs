---
title: 托管标识
description: 了解托管标识如何在 Azure App Service 和 Azure Functions 中工作，如何配置托管标识并为后端资源生成令牌。
author: mattchenderson
ms.topic: article
ms.date: 10/30/2019
ms.author: mahender
ms.reviewer: yevbronsh
ms.openlocfilehash: 4e2a76e40206e1562d565571dbe22e5d9d0e930e
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834168"
---
# <a name="how-to-use-managed-identities-for-app-service-and-azure-functions"></a>如何使用应用服务和 Azure Functions 的托管标识

> [!Important] 
> 如果应用跨订阅/租户迁移，应用服务和 Azure Functions 的托管标识将不会按预期工作。 应用将需要获取新标识，这可以通过禁用并重新启用该功能来完成。 请参阅下面的[删除标识](#remove)。 下游资源还需要更新访问策略才能使用新标识。

本主题介绍如何为应用服务和 Azure Functions 应用程序创建托管标识，以及如何使用它来访问其他资源。 通过 Azure Active Directory （AAD）中的托管标识，你的应用可以轻松访问其他受 AAD 保护的资源，例如 Azure Key Vault。 标识由 Azure 平台托管，无需设置或转交任何机密。 有关 AAD 中的托管标识的详细信息，请参阅 [Azure 资源的托管标识](../active-directory/managed-identities-azure-resources/overview.md)。

你的应用程序可以被授予两种类型的标识： 
- 系统分配的标识与你的应用程序相绑定，如果删除应用，标识也会被删除。 一个应用只能具有一个系统分配的标识。
- 用户分配的标识是可以分配给应用的独立 Azure 资源。 一个应用可以具有多个用户分配的标识。

## <a name="adding-a-system-assigned-identity"></a>添加系统分配的标识

若要创建带有系统分配的标识的应用，需在应用程序上设置一个额外的属性。

### <a name="using-the-azure-portal"></a>使用 Azure 门户

要在门户中设置托管标识，需先按常规创建应用程序，然后启用该功能。

1. 按常规在门户中创建应用。 在门户中导航到该应用。

2. 如果使用函数应用，请导航到“平台功能”。 对于其他应用类型，请在左侧导航区域向下滚动到“设置”组。

3. 选择 "**标识**"。

4. 在“系统分配的”选项卡中，将“状态”切换为“启用”。 单击“ **保存**”。

    ![应用服务中的托管标识](media/app-service-managed-service-identity/system-assigned-managed-identity-in-azure-portal.png)

### <a name="using-the-azure-cli"></a>使用 Azure CLI

若要使用 Azure CLI 设置托管标识，需要针对现有应用程序使用 `az webapp identity assign` 命令。 运行本部分中的示例有三个选项：

- 在 Azure 门户中使用 [Azure Cloud Shell](../cloud-shell/overview.md)。
- 单击下面每个代码块右上角的“试用”按钮，使用嵌入的 Azure Cloud Shell。
- 如果喜欢使用本地 CLI 控制台，请[安装最新版 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)（2.0.31 或更高版本）。 

以下步骤将指导你完成使用 CLI 创建 Web 应用并为其分配标识的操作：

1. 如果在本地控制台中使用 Azure CLI，首先请使用 [az login](/cli/azure/reference-index#az-login) 登录到 Azure。 使用与要在其下部署应用程序的 Azure 订阅关联的帐户：

    ```azurecli-interactive
    az login
    ```
2. 使用 CLI 创建 Web 应用程序。 有关如何将 CLI 用于应用服务的更多示例，请参阅[应用服务 CLI 示例](../app-service/samples-cli.md)：

    ```azurecli-interactive
    az group create --name myResourceGroup --location westus
    az appservice plan create --name myPlan --resource-group myResourceGroup --sku S1
    az webapp create --name myApp --resource-group myResourceGroup --plan myPlan
    ```

3. 运行 `identity assign` 命令为此应用程序创建标识：

    ```azurecli-interactive
    az webapp identity assign --name myApp --resource-group myResourceGroup
    ```

### <a name="using-azure-powershell"></a>使用 Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

以下步骤将指导你完成使用 Azure PowerShell 创建 Web 应用并为其分配标识的操作：

1. 如果需要，请使用[Azure PowerShell 指南](/powershell/azure/overview)中的说明安装 Azure PowerShell，然后运行 `Login-AzAccount` 以创建与 Azure 的连接。

2. 使用 Azure PowerShell 创建 Web 应用程序。 有关如何将 Azure PowerShell 用于应用服务的更多示例，请参阅[应用服务 PowerShell 示例](../app-service/samples-powershell.md)：

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name myResourceGroup -Location $location
    
    # Create an App Service plan in Free tier.
    New-AzAppServicePlan -Name $webappname -Location $location -ResourceGroupName myResourceGroup -Tier Free
    
    # Create a web app.
    New-AzWebApp -Name $webappname -Location $location -AppServicePlan $webappname -ResourceGroupName myResourceGroup
    ```

3. 运行 `Set-AzWebApp -AssignIdentity` 命令为此应用程序创建标识：

    ```azurepowershell-interactive
    Set-AzWebApp -AssignIdentity $true -Name $webappname -ResourceGroupName myResourceGroup 
    ```

### <a name="using-an-azure-resource-manager-template"></a>使用 Azure 资源管理器模板

Azure 资源管理器模板可以用于自动化 Azure 资源部署。 若要详细了解如何部署到应用服务和 Functions，请参阅[在应用服务中自动执行资源部署](../app-service/deploy-complex-application-predictably.md)和[在 Azure Functions 中自动执行资源部署](../azure-functions/functions-infrastructure-as-code.md)。

在资源定义包括以下属性，可以创建 `Microsoft.Web/sites` 类型的任何有标识资源：
```json
"identity": {
    "type": "SystemAssigned"
}    
```

> [!NOTE] 
> 一个应用程序可以同时具有系统分配的标识和用户分配的标识。 在这种情况下，`type` 属性将为 `SystemAssigned,UserAssigned`

添加系统分配的标识将告知 Azure 为应用程序创建和管理标识。

例如，Web 应用可能如下所示：
```json
{
    "apiVersion": "2016-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('appName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "SystemAssigned"
    },
    "properties": {
        "name": "[variables('appName')]",
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "hostingEnvironment": "",
        "clientAffinityEnabled": false,
        "alwaysOn": true
    },
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"
    ]
}
```

网站创建后，它具有以下附加属性：
```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

其中 `<TENANTID>` 和 `<PRINCIPALID>` 替换为 GUID。 tenantId 属性标识该标识所属的 AAD 租户。 principalId 是应用程序新标识的唯一标识符。 在 AAD 中，服务主体的名称与你为应用服务或 Azure Functions 实例提供的名称相同。


## <a name="adding-a-user-assigned-identity"></a>添加用户分配的标识

创建带有用户分配符的标识的应用需要创建标识，然后将其资源标识符添加到应用配置中。

### <a name="using-the-azure-portal"></a>使用 Azure 门户

首先，需要创建用户分配的标识资源。

1. 根据[这些说明](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)创建用户分配的托管标识资源。

2. 按常规在门户中创建应用。 在门户中导航到该应用。

3. 如果使用函数应用，请导航到“平台功能”。 对于其他应用类型，请在左侧导航区域向下滚动到“设置”组。

4. 选择 "**标识**"。

5. 在 "**用户分配**" 选项卡中，单击 "**添加**"。

6. 搜索之前创建的标识并选择它。 单击“添加”。

    ![应用服务中的托管标识](media/app-service-managed-service-identity/user-assigned-managed-identity-in-azure-portal.png)

### <a name="using-an-azure-resource-manager-template"></a>使用 Azure 资源管理器模板

Azure 资源管理器模板可以用于自动化 Azure 资源部署。 若要详细了解如何部署到应用服务和 Functions，请参阅[在应用服务中自动执行资源部署](../app-service/deploy-complex-application-predictably.md)和[在 Azure Functions 中自动执行资源部署](../azure-functions/functions-infrastructure-as-code.md)。

通过在资源定义中包含以下块，然后将 `<RESOURCEID>` 替换为所需标识的资源 ID，就可以创建带有标识的任何 `Microsoft.Web/sites` 类型的资源：
```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {}
    }
}    
```

> [!NOTE] 
> 一个应用程序可以同时具有系统分配的标识和用户分配的标识。 在这种情况下，`type` 属性将为 `SystemAssigned,UserAssigned`

添加用户分配的类型会告知 Azure 使用为应用程序指定的用户分配的标识。

例如，Web 应用可能如下所示：
```json
{
    "apiVersion": "2016-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('appName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
            "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]": {}
        }
    },
    "properties": {
        "name": "[variables('appName')]",
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "hostingEnvironment": "",
        "clientAffinityEnabled": false,
        "alwaysOn": true
    },
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]"
    ]
}
```

网站创建后，它具有以下附加属性：
```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {
            "principalId": "<PRINCIPALID>",
            "clientId": "<CLIENTID>"
        }
    }
}
```

其中 `<PRINCIPALID>` 和 `<CLIENTID>` 替换为 GUID。 principalId 是用于 AAD 管理的标识的唯一标识符。 clientId 是应用程序新标识的唯一标识符，用于指定在运行时调用期间要使用的标识。


## <a name="obtaining-tokens-for-azure-resources"></a>获取 Azure 资源的令牌

应用可以使用其托管标识来获取令牌，以访问由 AAD 保护的其他资源，如 Azure Key Vault。 这些令牌代表访问资源的应用程序，而不是应用程序的任何特定用户。 

> [!IMPORTANT]
> 可能需要配置目标资源，允许从应用程序进行访问。 例如，如果请求令牌来访问 Key Vault，则需要确保已添加包含应用程序标识的访问策略。 否则，对 Key Vault 的调用将被拒绝，即使其中包含令牌。 若要详细了解支持 Azure Active Directory 令牌的资源，请参阅[支持 Azure AD 身份验证的 Azure 服务](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)。

在应用服务和 Azure Functions 中，使用简单的 REST 协议获取令牌。 这可用于所有应用程序和语言。 对于 .NET 和 Java，Azure SDK 通过此协议提供抽象，并促进本地开发体验。

### <a name="using-the-rest-protocol"></a>使用 REST 协议

有托管标识的应用定义了两个环境变量：

- MSI_ENDPOINT - 本地令牌服务的 URL。
- MSI_SECRET - 用于帮助缓解服务器端请求伪造 (SSRF) 攻击的标头。 该值由平台轮换。

“MSI_ENDPOINT”是一本地 URL，应用可向其请求令牌。 若要获取资源的令牌，请对此终结点发起 HTTP GET 请求，并包括以下参数：

> |参数名称|In|Description|
> |-----|-----|-----|
> |resource|查询|应获取其令牌的资源的 AAD 资源 URI。 这可以是[支持 Azure AD 身份验证的 Azure 服务](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)或任何其他资源 URI 之一。|
> |api-version|查询|要使用的令牌 API 版本。 目前唯一支持的版本是 "2017-09-01"。|
> |secret|标头|MSI_SECRET 环境变量的值。 此标头用于帮助缓解服务器端请求伪造 (SSRF) 攻击。|
> |clientid|查询|（除非用户分配，否则为可选）要使用的用户分配的标识的 ID。 如果省略，则将使用系统分配的标识。|

> [!IMPORTANT]
> 如果尝试获取用户分配的标识的令牌，则必须包含 `clientid` 属性。 否则，令牌服务将尝试为系统分配的标识获取令牌，这可能存在也可能不存在。

成功的 200 OK 响应包括具有以下属性的 JSON 正文：

> |属性名称|Description|
> |-------------|----------|
> |access_token|请求的访问令牌。 调用 Web 服务可以使用此令牌向接收 Web 服务进行身份验证。|
> |expires_on|访问令牌的过期时间。 该日期表示为自 1970-01-01T0:0:0Z UTC 至过期时间的秒数。 此值用于确定缓存令牌的生存期。|
> |resource|接收 Web 服务的应用 ID URI。|
> |token_type|指示令牌类型值。 Azure AD 唯一支持的类型是 Bearer。 有关持有者令牌的详细信息，请参阅 [OAuth 2.0 授权框架：持有者令牌用法 (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt)。|

此响应与 [AAD 服务到服务访问令牌请求的响应](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md#service-to-service-access-token-response)相同。

> [!NOTE]
> 进程第一次启动时会设置环境变量，因此为应用程序启用托管标识后，可能需要重启应用程序或重新部署其代码，然后才能在代码中使用 `MSI_ENDPOINT` 和 `MSI_SECRET`。

### <a name="rest-protocol-examples"></a>REST 协议示例

示例请求可能如下例所示：

```
GET /MSI/token?resource=https://vault.azure.net&api-version=2017-09-01 HTTP/1.1
Host: localhost:4141
Secret: 853b9a84-5bfa-4b22-a3f3-0b9a43d9ad8a
```

示例响应可能如下例所示：

```
HTTP/1.1 200 OK
Content-Type: application/json

{
    "access_token": "eyJ0eXAi…",
    "expires_on": "09/14/2017 00:00:00 PM +00:00",
    "resource": "https://vault.azure.net",
    "token_type": "Bearer"
}
```

### <a name="code-examples"></a>代码示例

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

> [!TIP]
> 对于 .NET 语言，也可使用 [Microsoft.Azure.Services.AppAuthentication](#asal) 而不是自己创建此请求。

```csharp
private readonly HttpClient _client;
// ...
public async Task<HttpResponseMessage> GetToken(string resource)  {
    var request = new HttpRequestMessage(HttpMethod.Get, 
        String.Format("{0}/?resource={1}&api-version=2017-09-01", Environment.GetEnvironmentVariable("MSI_ENDPOINT"), resource));
    request.Headers.Add("Secret", Environment.GetEnvironmentVariable("MSI_SECRET"));
    return await _client.SendAsync(request);
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
const rp = require('request-promise');
const getToken = function(resource, cb) {
    let options = {
        uri: `${process.env["MSI_ENDPOINT"]}/?resource=${resource}&api-version=2017-09-01`,
        headers: {
            'Secret': process.env["MSI_SECRET"]
        }
    };
    rp(options)
        .then(cb);
}
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```python
import os
import requests

msi_endpoint = os.environ["MSI_ENDPOINT"]
msi_secret = os.environ["MSI_SECRET"]

def get_bearer_token(resource_uri):
    token_auth_uri = f"{msi_endpoint}?resource={resource_uri}&api-version=2017-09-01"
    head_msi = {'Secret':msi_secret}

    resp = requests.get(token_auth_uri, headers=head_msi)
    access_token = resp.json()['access_token']

    return access_token
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

```powershell
$resourceURI = "https://<AAD-resource-URI-for-resource-to-obtain-token>"
$tokenAuthURI = $env:MSI_ENDPOINT + "?resource=$resourceURI&api-version=2017-09-01"
$tokenResponse = Invoke-RestMethod -Method Get -Headers @{"Secret"="$env:MSI_SECRET"} -Uri $tokenAuthURI
$accessToken = $tokenResponse.access_token
```

---

### <a name="asal"></a>使用用于.NET 的 Microsoft.Azure.Services.AppAuthentication 库

对于 .NET 应用程序和函数，使用托管标识最简单的方法是通过 Microsoft.Azure.Services.AppAuthentication 包。 此库还允许通过 Visual Studio、[Azure CLI](/cli/azure) 或 Active Directory 集成身份验证使用用户帐户，在开发计算机上对代码进行本地测试。 有关此库的本地开发选项的详细信息，请参阅 [Microsoft.Azure.Services.AppAuthentication 参考]。 本部分演示如何开始在代码中使用此库。

1. 向应用程序添加对 [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) 和任何其他必需 NuGet 包的引用。 以下示例还使用 [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault)。

2. 将以下代码添加到应用程序，以修改为针对相应的资源。 此示例演示了使用 Azure Key Vault 的两种方法：

    ```csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;
    // ...
    var azureServiceTokenProvider = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://vault.azure.net");
    // OR
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
    ```

若要了解有关 Microsoft.Azure.Services.AppAuthentication 及其公开的操作的详细信息，请参阅 [Microsoft.Azure.Services.AppAuthentication 参考]以及[将应用服务和 KeyVault 与 MSI.NET 配合使用示例](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)。

### <a name="using-the-azure-sdk-for-java"></a>使用用于 Java 的 Azure SDK

对于 Java 应用程序和函数，使用托管标识的最简单方法是使用[AZURE SDK For Java](https://github.com/Azure/azure-sdk-for-java)。 本部分演示如何开始在代码中使用此库。

1. 添加对[AZURE SDK 库](https://mvnrepository.com/artifact/com.microsoft.azure/azure)的引用。 对于 Maven 项目，可以将此代码片段添加到项目 POM 文件的 `dependencies` 部分：

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure</artifactId>
        <version>1.23.0</version>
    </dependency>
    ```

2. 使用 `AppServiceMSICredentials` 对象进行身份验证。 此示例演示如何使用此机制来处理 Azure Key Vault：

    ```java
    import com.microsoft.azure.AzureEnvironment;
    import com.microsoft.azure.management.Azure;
    import com.microsoft.azure.management.keyvault.Vault
    //...
    Azure azure = Azure.authenticate(new AppServiceMSICredentials(AzureEnvironment.AZURE))
            .withSubscription(subscriptionId);
    Vault myKeyVault = azure.vaults().getByResourceGroup(resourceGroup, keyvaultName);

    ```


## <a name="remove"></a>删除标识

可以使用门户、PowerShell 或 CLI 以与创建时相同的方式禁用此功能，从而删除系统分配的标识。 可以单独删除用户分配的标识。 若要删除所有标识，请在 REST/ARM 模板协议中通过将类型设置为“无”来完成此操作：

```json
"identity": {
    "type": "None"
}
```

以这种方式删除系统分配的标识也会将它从 AAD 中删除。 删除应用资源时，也将自动从 AAD 中删除系统分配的标识。

> [!NOTE]
> 还可以设置一个应用程序设置 (WEBSITE_DISABLE_MSI)，它只禁用本地令牌服务。 但是，它会原地保留标识，工具仍然会将托管标识显示为“打开”或“启用”。 因此，建议不要使用此设置。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用托管标识安全地访问 SQL 数据库](app-service-web-tutorial-connect-msi.md)

[Microsoft.Azure.Services.AppAuthentication 参考]: https://go.microsoft.com/fwlink/p/?linkid=862452
