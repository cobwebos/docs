---
title: 使用 Microsoft Graph API 管理用户
titleSuffix: Azure AD B2C
description: 如何通过调用 Microsoft Graph API 和使用应用程序标识自动化流程来管理 Azure AD B2C 租户中的用户。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 42596ba5470c6062efba4fd1050c1c9745b76e80
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80637334"
---
# <a name="manage-azure-ad-b2c-user-accounts-with-microsoft-graph"></a>使用 Microsoft Graph 管理 Azure AD B2C 用户帐户

Microsoft Graph 允许通过在 Microsoft Graph API 中提供创建、读取、更新和删除方法来管理 Azure AD B2C 目录中的用户帐户。 可以调用 Microsoft Graph API 将现有用户存储迁移到 Azure AD B2C 租户并执行其他用户帐户管理操作。

后面的部分将演示使用 Microsoft Graph API 管理 Azure AD B2C 用户时的重要方面。 本文演示的 Microsoft Graph API 操作、类型和属性是 Microsoft Graph API 参考文档中显示的相应元素的一部分。

## <a name="register-a-management-application"></a>注册管理应用程序

在编写的任何用户管理应用程序或脚本可与 Azure AD B2C 租户中的资源交互之前，需要创建一个应用程序注册，以授权执行此类操作。

遵循本操作指南文章中的步骤创建管理应用程序可以使用的应用程序注册：

[使用 Microsoft Graph 管理 Azure AD B2C](microsoft-graph-get-started.md)

## <a name="user-management-microsoft-graph-operations"></a>用户管理 Microsoft Graph 操作

在 [Microsoft Graph API](https://docs.microsoft.com/graph/api/resources/user) 中可以使用以下用户管理操作：

- [获取用户列表](https://docs.microsoft.com/graph/api/user-list)
- [创建用户](https://docs.microsoft.com/graph/api/user-post-users)
- [获取用户](https://docs.microsoft.com/graph/api/user-get)
- [更新用户](https://docs.microsoft.com/graph/api/user-update)
- [删除用户](https://docs.microsoft.com/graph/api/user-delete)

## <a name="user-properties"></a>用户属性

### <a name="display-name-property"></a>显示名称属性

`displayName` 是要在用户的 Azure 门户用户管理界面中显示的，以及要在 Azure AD B2C 返回给应用程序的访问令牌中显示的名称。 此属性是必需项。

### <a name="identities-property"></a>标识属性

客户帐户（可以是使用者、合作伙伴或居民）可与以下标识类型相关联：

- **本地**标识 - 将用户名和密码存储在 Azure AD B2C 目录本地。 我们通常将此类标识称为“本地帐户”。
- **联合**身份（也称为*社交*或*企业*帐户），用户的标识由 Facebook、Microsoft、ADFS 或 Salesforce 等联合标识提供者进行管理。

具有客户帐户的用户可以使用多个标识进行登录。 例如，使用用户名、电子邮件、员工 ID、政府 ID 等。 一个帐户可以有多个密码相同的本地和社交标识。

在 Microsoft Graph API 中，本地标识和联合标识都存储在 `identities`objectIdentity[ 类型的用户 ][graph-objectIdentity] 特性中。 `identities` 集合表示用于登录到用户帐户的一组标识。 此集合使用户能够使用其关联的任何标识登录到用户帐户。

| properties   | 类型 |说明|
|:---------------|:--------|:----------|
|signInType|字符串| 指定目录中的用户登录类型。 对于本地帐户：`emailAddress`、`emailAddress1`、`emailAddress2`、`emailAddress3`、`userName`，或所需的任何其他类型。 社交帐户必须设置为 `federated`。|
|颁发者|字符串|指定标识的颁发者。 对于本地帐户（其 **signInType** 不是 `federated`），此属性是本地 B2C 租户的默认域名，例如 `contoso.onmicrosoft.com`。 对于社交标识（其中**signInType**为`federated`），该值为颁发者的名称，例如`facebook.com`|
|issuerAssignedId|字符串|指定由颁发者分配给用户的唯一标识符。 **issuer** 和 **issuerAssignedId** 的组合在租户中必须唯一。 对于本地帐户，当 **signInType** 设置为 `emailAddress` 或 `userName` 时，它表示用户的登录名。<br>如果 **signInType** 设置为： <ul><li>`emailAddress`（或以 `emailAddress` 开头，例如 `emailAddress1`），则 **issuerAssignedId** 必须是有效的电子邮件地址</li><li>`userName`（或任何其他值），则 **issuerAssignedId** 必须是有效的[电子邮件地址本地部分](https://tools.ietf.org/html/rfc3696#section-3)</li><li>`federated`，则 **issuerAssignedId** 表示联合帐户唯一标识符</li></ul>|

以下**标识**属性，其中包含一个本地帐户标识，其中包含登录名、电子邮件地址和社交标识。 

 ```JSON
 "identities": [
     {
       "signInType": "userName",
       "issuer": "contoso.onmicrosoft.com",
       "issuerAssignedId": "johnsmith"
     },
     {
       "signInType": "emailAddress",
       "issuer": "contoso.onmicrosoft.com",
       "issuerAssignedId": "jsmith@yahoo.com"
     },
     {
       "signInType": "federated",
       "issuer": "facebook.com",
       "issuerAssignedId": "5eecb0cd"
     }
   ]
 ```

对于联合标识，根据标识提供者， **issuerAssignedId**是每个应用程序或开发帐户的给定用户的唯一值。 使用同一个开发帐户中的社交提供程序或另一个应用程序之前分配的相同应用程序 ID 来配置 Azure AD B2C 策略。

### <a name="password-profile-property"></a>密码配置文件属性

对于本地标识， **passwordProfile**属性是必需的，并且包含用户的密码。 `forceChangePasswordNextSignIn`属性必须设置为`false`。

对于联合（社交）标识， **passwordProfile**属性不是必需的。

```JSON
"passwordProfile" : {
    "password": "password-value",
    "forceChangePasswordNextSignIn": false
  }
```

### <a name="password-policy-property"></a>密码策略属性

Azure AD B2C 的密码策略（对于本地帐户）基于 Azure Active Directory[强密码强度](../active-directory/authentication/concept-sspr-policy.md)策略。 Azure AD B2C 注册或登录和密码重置策略需要此强密码强度，并且不会使密码过期。

在用户迁移方案中，如果要迁移的帐户的密码强度比 Azure AD B2C 强制实施的[强密码强度](../active-directory/authentication/concept-sspr-policy.md)弱，则可以禁用强密码要求。 若要更改默认密码策略，请将 `passwordPolicies` 属性设置为 `DisableStrongPassword`。 例如，可按如下所示修改创建用户请求：

```JSON
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

### <a name="extension-properties"></a>扩展属性

每个面向客户的应用程序都对要收集的信息有独特的要求。 Azure AD B2C 租户附带了一组存储在属性中的信息，例如名字、姓氏、城市和邮政编码。 在 Azure AD B2C 中，可以扩展存储在每个客户帐户中的属性集。 有关定义自定义属性的详细信息，请参阅[自定义特性（用户流）](user-flow-custom-attributes.md)和[自定义特性（自定义策略）](custom-policy-custom-attributes.md)。

Microsoft Graph API 支持使用扩展属性创建和更新用户。 图形 API 中的扩展属性使用约定 `extension_ApplicationObjectID_attributename` 来命名。 例如：

```JSON
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyNumber": "212342"
```

## <a name="code-sample"></a>代码示例

此代码示例是一个 .NET Core 控制台应用程序，它使用[MICROSOFT GRAPH SDK](https://docs.microsoft.com/graph/sdks/sdks-overview)与 Microsoft Graph API 交互。 其中的代码演示了如何调用 API 来以编程方式管理 Azure AD B2C 租户中的用户。
您可以[下载示例存档](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management/archive/master.zip)（* .zip），浏览 GitHub 上[的存储库](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management)，或者克隆存储库：

```cmd
git clone https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management.git
```

获取代码示例后，根据环境对其进行配置，然后生成项目：

1. 在[Visual Studio](https://visualstudio.microsoft.com)中打开项目或[Visual Studio Code](https://code.visualstudio.com)。
1. 打开 `src/appsettings.json`。
1. 在`appSettings`部分中，将`your-b2c-tenant`替换为你的租户的名称， `Application (client) ID` `Client secret`将替换为你的管理应用程序注册的值（请参阅本文的[注册管理应用程序](#register-a-management-application)部分）。
1. 在存储库的本地克隆中打开控制台窗口，切换到`src`目录，然后生成项目：
    ```console
    cd src
    dotnet build
    ```
1. 使用 `dotnet` 命令运行应用程序：

    ```console
    dotnet bin/Debug/netcoreapp3.0/b2c-ms-graph.dll
    ```

应用程序将显示可执行的命令的列表。 例如，获取所有用户、获取单个用户、删除用户、更新用户的密码和大容量导入。

### <a name="code-discussion"></a>代码讨论

示例代码使用[Microsoft Graph SDK](https://docs.microsoft.com/graph/sdks/sdks-overview)，该 SDK 旨在简化访问 Microsoft Graph 的高质量、高效且可复原的应用程序。

对 Microsoft Graph API 发出的任何请求都需要使用访问令牌进行身份验证。 该解决方案利用 [Microsoft.Graph.Auth](https://www.nuget.org/packages/Microsoft.Graph.Auth/) NuGet 包，该包提供 Microsoft 身份验证库 (MSAL) 的基于身份验证方案的包装器，以便与 Microsoft Graph SDK 配合使用。

`RunAsync`Program.cs_文件中的_ 方法：

1. 从 _appsettings.json_ 文件读取应用程序设置
1. 使用 [OAuth 2.0 客户端凭据授予](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md)流初始化身份验证提供程序。 应用可以使用客户端凭据授予流获取用于调用 Microsoft Graph API 的访问令牌。
1. 使用身份验证提供程序设置 Microsoft Graph 服务客户端：

    ```csharp
    // Read application settings from appsettings.json (tenant ID, app ID, client secret, etc.)
    AppSettings config = AppSettingsFile.ReadFromJsonFile();

    // Initialize the client credential auth provider
    IConfidentialClientApplication confidentialClientApplication = ConfidentialClientApplicationBuilder
        .Create(config.AppId)
        .WithTenantId(config.TenantId)
        .WithClientSecret(config.ClientSecret)
        .Build();
    ClientCredentialProvider authProvider = new ClientCredentialProvider(confidentialClientApplication);

    // Set up the Microsoft Graph service client with client credentials
    GraphServiceClient graphClient = new GraphServiceClient(authProvider);
    ```

然后，在 *UserService.cs* 中使用初始化的 _GraphServiceClient_ 来执行用户管理操作。 例如，获取租户中的用户帐户列表：

```csharp
public static async Task ListUsers(GraphServiceClient graphClient)
{
    Console.WriteLine("Getting list of users...");

    // Get all users (one page)
    var result = await graphClient.Users
        .Request()
        .Select(e => new
        {
            e.DisplayName,
            e.Id,
            e.Identities
        })
        .GetAsync();

    foreach (var user in result.CurrentPage)
    {
        Console.WriteLine(JsonConvert.SerializeObject(user));
    }
}
```

[使用 Microsoft Graph SDK 发出 API 调用](https://docs.microsoft.com/graph/sdks/create-requests)中介绍了如何在 Microsoft Graph 中读取和写入信息，使用 `$select` 控制返回的属性，提供自定义查询参数，以及使用 `$filter` 和 `$orderBy` 查询参数。

## <a name="next-steps"></a>后续步骤

有关 Azure AD B2C 资源支持的 Microsoft Graph API 操作的完整索引，请参阅[可用于 Azure AD B2C 的 Microsoft Graph 操作](microsoft-graph-operations.md)。

<!-- LINK -->

[graph-objectIdentity]: https://docs.microsoft.com/graph/api/resources/objectidentity
[graph-user]: (https://docs.microsoft.com/graph/api/resources/user)
