---
title: 使用 Microsoft 图形 API 管理用户
titleSuffix: Azure AD B2C
description: 如何通过调用 Microsoft 图形 API 并使用应用程序标识来自动执行该过程来管理 Azure AD B2C 租户中的用户。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3bd166572cea23fbb710cd053c28f51e76ba534a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476665"
---
# <a name="manage-azure-ad-b2c-user-accounts-with-microsoft-graph"></a>使用微软图形管理 Azure AD B2C 用户帐户

Microsoft 图形允许您通过在 Microsoft 图形 API 中提供创建、读取、更新和删除方法来管理 Azure AD B2C 目录中的用户帐户。 您可以将现有用户存储迁移到 Azure AD B2C 租户，并通过调用 Microsoft 图形 API 执行其他用户帐户管理操作。

在以下各节中，介绍了使用 Microsoft 图形 API 进行 Azure AD B2C 用户管理的关键方面。 此处介绍的 Microsoft 图形 API 操作、类型和属性是 Microsoft 图形 API 参考文档中显示的操作、类型和属性的子集。

## <a name="register-a-management-application"></a>注册管理应用程序

在编写的任何用户管理应用程序或脚本都可以与 Azure AD B2C 租户中的资源进行交互之前，需要授予执行此操作的权限的应用程序注册。

按照本操作操作文章中的步骤创建管理应用程序可以使用的应用程序注册：

[使用微软图形管理 Azure AD B2C](microsoft-graph-get-started.md)

## <a name="user-management-microsoft-graph-operations"></a>用户管理 微软图形操作

以下用户管理操作在[Microsoft 图形 API](https://docs.microsoft.com/graph/api/resources/user)中可用：

- [获取用户列表](https://docs.microsoft.com/graph/api/user-list)
- [创建用户](https://docs.microsoft.com/graph/api/user-post-users)
- [获取用户](https://docs.microsoft.com/graph/api/user-get)
- [更新用户](https://docs.microsoft.com/graph/api/user-update)
- [删除用户](https://docs.microsoft.com/graph/api/user-delete)

## <a name="user-properties"></a>用户属性

### <a name="display-name-property"></a>显示名称属性

是`displayName`要在 Azure 门户用户管理中显示的名称，在访问令牌 Azure AD B2C 中返回到应用程序。 此属性是必需项。

### <a name="identities-property"></a>标识属性

客户帐户（可以是消费者、合作伙伴或公民）可以与以下标识类型相关联：

- **本地**标识 - 用户名和密码存储在 Azure AD B2C 目录中的本地。 我们经常将这些身份称为"本地帐户"。
- **联合**标识 - 也称为*社交*或*企业*帐户，用户的身份由联合标识提供商（如 Facebook、Microsoft、ADFS 或 Salesforce）管理。

具有客户帐户的用户可以使用多个标识登录。 例如，用户名、电子邮件、员工 ID、政府 ID 等。 单个帐户可以具有多个标识（本地身份和社交身份）具有相同的密码。

在 Microsoft 图形 API 中，本地标识和联合标识都存储在用户`identities`属性中，该属性的类型为[对象标识][graph-objectIdentity]。 该`identities`集合表示用于登录到用户帐户的一组标识。 此集合使用户能够使用任何关联标识登录到用户帐户。

| properties   | 类型 |说明|
|:---------------|:--------|:----------|
|符号类型|字符串| 指定目录中的用户登录类型。 对于本地帐户：、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、 `emailAddress` `emailAddress1` `emailAddress2` `emailAddress3` `userName` 社会帐户必须设置为`federated`。|
|颁发者|字符串|指定标识的颁发者。 对于本地帐户（**其中 signInType** `federated`不是），此属性是本地 B2C 租户默认域名，例如`contoso.onmicrosoft.com`。 对于社交标识（**其中符号InType**为`federated`） 值是颁发者的名称，例如`facebook.com`|
|发行人分配ID|字符串|指定颁发者分配给用户的唯一标识符。 **颁发者和颁发**者**AssignedId**的组合在租户中必须是唯一的。 对于本地帐户，当**signInType**设置为`emailAddress``userName`或 时，它表示用户的登录名称。<br>当**符号类型**设置为： <ul><li>`emailAddress`（或从`emailAddress`喜欢`emailAddress1`开始）**颁发者分配 Id**必须是有效的电子邮件地址</li><li>`userName`（或任何其他值），**颁发者 AssignedId**必须是电子邮件地址的有效[本地部分](https://tools.ietf.org/html/rfc3696#section-3)</li><li>`federated`，**颁发者分配Id**表示联合帐户的唯一标识符</li></ul>|

以下**标识**属性，具有具有登录名的本地帐户标识、作为登录名的电子邮件地址以及具有社交标识。 

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

对于联合标识，根据标识提供程序，**颁发者 AssignedId**是每个应用程序或开发帐户给定用户的唯一值。 配置 Azure AD B2C 策略时，具有以前由社交提供程序或同一开发帐户中其他应用程序分配的应用程序 ID。

### <a name="password-profile-property"></a>密码配置文件属性

对于本地标识，密码**配置文件**属性是必需的，并包含用户的密码。 属性`forceChangePasswordNextSignIn`必须设置为`false`。

对于联合（社交）标识，密码**配置文件**属性不是必需的。

```JSON
"passwordProfile" : {
    "password": "password-value",
    "forceChangePasswordNextSignIn": false
  }
```

### <a name="password-policy-property"></a>密码策略属性

Azure AD B2C 密码策略（针对本地帐户）基于 Azure 活动目录[强密码强度](../active-directory/authentication/concept-sspr-policy.md)策略。 Azure AD B2C 注册或登录和密码重置策略需要此强密码强度，并且不会使密码过期。

在用户迁移方案中，如果要迁移的帐户的密码强度低于 Azure AD B2C 强制[的强密码强度](../active-directory/authentication/concept-sspr-policy.md)，则可以禁用强密码要求。 若要更改默认密码策略，请将 `passwordPolicies` 属性设置为 `DisableStrongPassword`。 例如，可按如下所示修改创建用户请求：

```JSON
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

### <a name="extension-properties"></a>扩展属性

每个面向客户的应用程序对要收集的信息都有独特的要求。 Azure AD B2C 租户附带一组内置信息，这些信息存储在属性中，如给定名称、姓氏、城市和邮政编码。 使用 Azure AD B2C，可以扩展存储在每个客户帐户中的属性集。 有关定义自定义属性的详细信息，请参阅[自定义属性（用户流）](user-flow-custom-attributes.md)和[自定义属性（自定义策略）。](custom-policy-custom-attributes.md)

Microsoft 图形 API 支持使用扩展属性创建和更新用户。 图形 API 中的扩展属性使用约定 `extension_ApplicationObjectID_attributename` 来命名。 例如：

```JSON
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyNumber": "212342"
```

## <a name="code-sample"></a>代码示例

此代码示例是 .NET 核心控制台应用程序，它使用[Microsoft 图形 SDK](https://docs.microsoft.com/graph/sdks/sdks-overview)与 Microsoft 图形 API 进行交互。 其中的代码演示了如何调用 API 来以编程方式管理 Azure AD B2C 租户中的用户。
您可以[下载示例存档](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management/archive/master.zip)（*.zip）、浏览 GitHub 上的[存储库](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management)或克隆存储库：

```cmd
git clone https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management.git
```

获取代码示例后，根据环境对其进行配置，然后生成项目：

1. 在[视觉工作室](https://visualstudio.microsoft.com)或[视觉工作室代码](https://code.visualstudio.com)中打开项目。
1. 打开 `src/appsettings.json`。
1. 在本`appSettings`节中，使用租户`your-b2c-tenant`的名称以及`Application (client) ID``Client secret`管理应用程序注册的值替换（请参阅本文的["注册管理应用程序](#register-a-management-application)"部分）。
1. 打开本地克隆中的控制台窗口，切换到`src`目录，然后生成项目：
    ```console
    cd src
    dotnet build
    ```
1. 使用 `dotnet` 命令运行应用程序：

    ```console
    dotnet bin/Debug/netcoreapp3.0/b2c-ms-graph.dll
    ```

应用程序显示可以执行的命令列表。 例如，获取所有用户、获取单个用户、删除用户、更新用户的密码以及批量导入。

### <a name="code-discussion"></a>代码讨论

示例代码使用[Microsoft 图形 SDK，](https://docs.microsoft.com/graph/sdks/sdks-overview)旨在简化构建访问 Microsoft Graph 的高质量、高效和弹性应用程序。 因此，您不需要直接使用所有 Microsoft 图形 API。

对 Microsoft 图形 API 的任何请求都需要访问令牌进行身份验证。 该解决方案使用[Microsoft.Graph.Auth](https://www.nuget.org/packages/Microsoft.Graph.Auth/) NuGet 包，该包提供 Microsoft 身份验证库 （MSAL） 的基于身份验证方案的包装器，以便与 Microsoft 图形 SDK 一起使用。

Program.cs`RunAsync`文件中的方法： _Program.cs_

1. 从_应用程序设置.json_文件读取应用程序设置
1. 使用[OAuth 2.0 客户端凭据授予](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md)流初始化身份验证提供程序。 使用客户端凭据授予流，应用能够获取访问令牌来调用 Microsoft 图形 API。
1. 使用身份验证提供程序设置 Microsoft 图形服务客户端：

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

然后，在_UserService.cs_中使用初始化*的图形ServiceClient*来执行用户管理操作。 例如，获取租户中的用户帐户列表：

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

[使用 Microsoft 图形 SDK 进行 API 调用](https://docs.microsoft.com/graph/sdks/create-requests)包括有关如何从 Microsoft 图形读取和写入信息`$select`、用于控制返回的属性、提供自定义查询参数以及使用`$filter`和`$orderBy`查询参数的信息。

## <a name="next-steps"></a>后续步骤

有关 Azure AD B2C 资源支持的 Microsoft 图形 API 操作的完整索引，请参阅[适用于 Azure AD B2C 的 Microsoft 图形操作](microsoft-graph-operations.md)。

<!-- LINK -->

[graph-objectIdentity]: https://docs.microsoft.com/graph/api/resources/objectidentity
[graph-user]: (https://docs.microsoft.com/graph/api/resources/user)
