---
title: 利用 Microsoft Graph API 管理用户
titleSuffix: Azure AD B2C
description: 如何通过调用 Microsoft Graph API 并使用应用程序标识来自动处理 Azure AD B2C 租户中的用户。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 8d65217a109a851275d3ba9205024f32bd182d4f
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78187310"
---
# <a name="manage-azure-ad-b2c-user-accounts-with-microsoft-graph"></a>通过 Microsoft Graph 管理 Azure AD B2C 用户帐户

Microsoft Graph 允许通过在 Microsoft Graph API 中提供创建、读取、更新和删除方法来管理 Azure AD B2C 目录中的用户帐户。 可以通过调用 Microsoft Graph API 将现有用户存储迁移到 Azure AD B2C 租户并执行其他用户帐户管理操作。

在下面的部分中，将显示 Azure AD B2C 用户管理 Microsoft Graph API 的主要方面。 此处提供的 Microsoft Graph API 操作、类型和属性是显示在 Microsoft Graph API 参考文档中的一个子集。

## <a name="register-a-management-application"></a>注册管理应用程序

在你编写的任何用户管理应用程序或脚本可以与 Azure AD B2C 租户中的资源进行交互之前，你需要一个授予执行此操作的权限的应用程序注册。

按照此操作方法一文中的步骤创建管理应用程序可以使用的应用程序注册：

[用 Microsoft Graph 管理 Azure AD B2C](microsoft-graph-get-started.md)

## <a name="user-management-microsoft-graph-operations"></a>用户管理 Microsoft Graph 操作

[MICROSOFT GRAPH API](https://docs.microsoft.com/graph/api/resources/user)中提供了以下用户管理操作：

- [获取用户列表](https://docs.microsoft.com/graph/api/user-list)
- [创建用户](https://docs.microsoft.com/graph/api/user-post-users)
- [获取用户](https://docs.microsoft.com/graph/api/user-get)
- [更新用户](https://docs.microsoft.com/graph/api/user-update)
- [删除用户](https://docs.microsoft.com/graph/api/user-delete)

## <a name="user-properties"></a>用户属性

### <a name="display-name-property"></a>显示名称属性

`displayName` 是要在用户的 Azure 门户用户管理中显示的名称，并且在访问令牌中 Azure AD B2C 返回到应用程序。 此属性是必需项。

### <a name="identities-property"></a>标识属性

客户帐户（可以是使用者、合作伙伴或公民）可以与以下标识类型相关联：

- **本地**标识-用户名和密码存储在本地 Azure AD B2C 目录中。 我们经常将这些标识称为 "本地帐户"。
- **联合**身份（也称为*社交*或*企业*帐户），用户的标识由 Facebook、Microsoft、ADFS 或 Salesforce 等联合标识提供者进行管理。

具有客户帐户的用户可以使用多个标识进行登录。 例如，用户名、电子邮件、员工 ID、政府 ID 等。 单个帐户可以有多个标识，本地和社交都具有相同密码。

在 Microsoft Graph API 中，本地和联合标识都存储在用户 `identities` 属性中，该属性的类型为[objectIdentity][graph-objectIdentity]。 `identities` 集合表示一组用于登录用户帐户的标识。 此集合使用户能够登录到具有其任何关联标识的用户帐户。

| properties   | 类型 |说明|
|:---------------|:--------|:----------|
|signInType|字符串| 指定目录中的用户登录类型。 对于 "本地帐户"： `emailAddress`、`emailAddress1`、`emailAddress2`、`emailAddress3`、`userName`或你喜欢的任何其他类型。 社交帐户必须设置为 `federated`。|
|颁发者|字符串|指定标识的颁发者。 对于本地帐户（其中**signInType**不 `federated`），此属性为本地 B2C 租户默认域名，例如 `contoso.onmicrosoft.com`。 对于社交标识（其中**signInType**为 `federated`），该值为颁发者的名称，例如 `facebook.com`|
|issuerAssignedId|字符串|指定由颁发者分配给用户的唯一标识符。 **颁发者**和**issuerAssignedId**的组合在你的租户中必须是唯一的。 对于本地帐户，当**signInType**设置为 `emailAddress` 或 `userName`时，它表示用户的登录名。<br>当**signInType**设置为时： <ul><li>`emailAddress` （或以 `emailAddress1`等 `emailAddress` 开头） **issuerAssignedId**必须是有效的电子邮件地址</li><li>`userName` （或任何其他值）， **issuerAssignedId**必须是[电子邮件地址的有效本地部分](https://tools.ietf.org/html/rfc3696#section-3)</li><li>`federated`， **issuerAssignedId**表示联合帐户唯一标识符</li></ul>|

对于联合标识，根据标识提供者， **issuerAssignedId**是每个应用程序或开发帐户的给定用户的唯一值。 使用同一个开发帐户中的社交提供程序或另一个应用程序之前分配的相同应用程序 ID 来配置 Azure AD B2C 策略。

### <a name="password-profile-property"></a>密码配置文件属性

对于本地标识， **passwordProfile**属性是必需的，并且包含用户的密码。 `forceChangePasswordNextSignIn` 属性必须设置为 `false`。

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

此代码示例是一个 .NET Core 控制台应用程序，它使用[MICROSOFT GRAPH SDK](https://docs.microsoft.com/graph/sdks/sdks-overview)与 Microsoft Graph API 交互。 它的代码演示如何调用 API 以编程方式管理 Azure AD B2C 租户中的用户。
您可以[下载示例存档](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management/archive/master.zip)（* .zip），浏览 GitHub 上[的存储库](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management)，或者克隆存储库：

```cmd
git clone https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management.git
```

获取代码示例后，为环境配置它，然后生成项目：

1. 在[Visual Studio](https://visualstudio.microsoft.com)中打开项目或[Visual Studio Code](https://code.visualstudio.com)。
1. 打开 `src/appsettings.json`。
1. 在 `appSettings` 部分中，将 `your-b2c-tenant` 替换为你的租户的名称，并使用你的管理应用程序注册的值 `Application (client) ID` 和 `Client secret` （请参阅本文的[注册管理应用程序](#register-a-management-application)部分）。
1. 在存储库的本地克隆中打开控制台窗口，切换到 `src` 目录，然后生成项目：
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

示例代码使用[Microsoft Graph SDK](https://docs.microsoft.com/graph/sdks/sdks-overview)，该 SDK 旨在简化访问 Microsoft Graph 的高质量、高效且可复原的应用程序。 因此，无需直接使用所有 Microsoft Graph API。

对 Microsoft Graph API 的任何请求都需要访问令牌以进行身份验证。 此解决方案利用了用于提供 Microsoft 身份验证库（MSAL）的基于身份验证方案包装的[microsoft.](https://www.nuget.org/packages/Microsoft.Graph.Auth/) authentication NuGet 包，以便与 Microsoft Graph SDK 一起使用。

_Program.cs_文件中的 `RunAsync` 方法：

1. 从_appsettings_文件中读取应用程序设置
1. 使用[OAuth 2.0 客户端凭据授予](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md)流初始化身份验证提供程序。 使用客户端凭据授予流，应用可以获取访问令牌来调用 Microsoft Graph API。
1. 通过身份验证提供程序设置 Microsoft Graph service 客户端：

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

然后，在_UserService.cs_中使用初始化后的*GraphServiceClient*执行用户管理操作。 例如，获取租户中用户帐户的列表：

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

[使用 Microsoft Graph Sdk 进行 API 调用](https://docs.microsoft.com/graph/sdks/create-requests)包括有关如何从 Microsoft Graph 读取和写入信息，使用 `$select` 控制返回的属性、提供自定义查询参数，以及使用 `$filter` 和 `$orderBy` 查询参数的信息。

## <a name="next-steps"></a>后续步骤

有关 Azure AD B2C 资源支持的 Microsoft Graph API 操作的完整索引，请参阅[可用于 Azure AD B2C 的 Microsoft Graph 操作](microsoft-graph-operations.md)。

<!-- LINK -->

[graph-objectIdentity]: https://docs.microsoft.com/graph/api/resources/objectidentity
[graph-user]: (https://docs.microsoft.com/graph/api/resources/user)
