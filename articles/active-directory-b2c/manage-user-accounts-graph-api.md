---
title: 使用 Azure Active Directory B2C 中的图形 API
description: 如何通过调用 Azure AD 图形 API 并使用应用程序标识来自动处理 Azure AD B2C 租户中的用户。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 71b437f57f9d9e6e18af88d6413269cac6f66c47
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77161658"
---
# <a name="azure-ad-b2c-use-the-azure-ad-graph-api"></a>Azure AD B2C：使用 Azure AD Graph API

Azure Active Directory B2C （Azure AD B2C）租户可以拥有数千或数百万的用户。 这意味着许多常见的租户管理任务需要以编程方式执行。 用户管理是一个主要示例。

可能需要将现有用户存储迁移到 B2C 租户。 可能想要在自己的页面上托管用户注册，并在 Azure AD B2C 目录后台创建用户帐户。 这类任务需要能够创建、读取、更新和删除用户帐户。 您可以使用 Azure AD 图形 API 来执行此类任务。

对于 B2C 租户，有两种与图形 API 通信的主要模式：

* 对于**交互式**的、运行一次的任务，您应在执行任务时作为 B2C 租户中的管理员帐户。 此模式需要管理员使用凭据登录，才能执行对图形 API 的任何调用。
* 对于**自动**的连续任务，你应使用你为执行管理任务所需的权限而提供的某种类型的服务帐户。 在 Azure AD 中，可以通过注册应用程序并向 Azure AD 进行身份验证来执行此操作。 这通过利用使用 *OAuth 2.0 客户端凭据授予*的[应用程序 ID](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md) 来完成。 在这种情况下，应用程序作为其本身而不是用户来调用图形 API。

本文介绍如何执行自动用例。 构建一个执行用户创建、读取、更新和删除 (CRUD) 操作的 .NET 4.5 `B2CGraphClient`。 客户端将拥有一个 Windows 命令行接口 (CLI)，允许用户调用各种方法。 但是，代码以非交互式、自动化的方式进行编写。

## <a name="prerequisites"></a>必备条件

创建应用程序或用户前，需要一个 Azure AD B2C 租户。 如果还没有，请创建一个[Azure Active Directory B2C 租户](tutorial-create-tenant.md)。

## <a name="register-an-application"></a>注册应用程序

Azure AD B2C 租户后，需要使用[Azure 门户](https://portal.azure.com)注册管理应用程序。 还需要向其授予代表自动脚本或管理应用程序执行管理任务所需的权限。

### <a name="register-application-in-azure-active-directory"></a>在 Azure Active Directory 中注册应用程序

若要将 Azure AD 图形 API 用于 B2C 租户，需要使用 Azure Active Directory 应用程序注册工作流来注册应用程序。

[!INCLUDE [active-directory-b2c-appreg-mgmt](../../includes/active-directory-b2c-appreg-mgmt.md)]

### <a name="assign-api-access-permissions"></a>分配 API 访问权限

[!INCLUDE [active-directory-b2c-permissions-directory](../../includes/active-directory-b2c-permissions-directory.md)]

### <a name="create-client-secret"></a>创建客户端密码

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

现在，你有一个应用程序，该应用程序有权在 Azure AD B2C 租户中*创建*、*读取*和*更新*用户。 转到下一节，添加用户*删除*和*密码更新*权限。

## <a name="add-user-delete-and-password-update-permissions"></a>添加用户删除和密码更新权限

之前授予的 "*读取和写入目录数据*" 权限**不**包括删除用户或更新其密码的功能。

如果要使应用程序能够删除用户或更新密码，则需要向其授予 "*用户管理员*" 角色。

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在门户工具栏中选择 "**目录 + 订阅**" 图标，然后选择包含 Azure AD B2C 租户的目录。
1. 在 Azure 门户中，搜索并选择 " **Azure AD B2C**"。
1. 在 "**管理**" 下，选择 "**角色和管理员**"。
1. 选择 "**用户管理员**" 角色。
1. 选择 "**添加分配**"。
1. 在 "**选择**" 文本框中，输入前面注册的应用程序的名称，例如*managementapp1*。 当应用程序出现在搜索结果中时，请选择它。
1. 选择 **添加** 。 权限完全传播可能需要几分钟的时间。

现在，Azure AD B2C 应用程序具有删除用户或更新 B2C 租户中的密码所需的其他权限。

## <a name="get-the-sample-code"></a>获取示例代码

此代码示例是一个 .NET 控制台应用程序，它使用[Active Directory 身份验证库（ADAL）](../active-directory/azuread-dev/active-directory-authentication-libraries.md)与 Azure AD 图形 API 进行交互。 它的代码演示如何调用 API 以编程方式管理 Azure AD B2C 租户中的用户。

您可以[下载示例存档](https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet/archive/master.zip)（\*.zip）或克隆 GitHub 存储库：

```cmd
git clone https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet.git
```

获取代码示例后，为环境配置它，然后生成项目：

1. 在 Visual Studio 中打开 `B2CGraphClient\B2CGraphClient.sln` 解决方案。
1. 在**B2CGraphClient**项目中，打开*app.config*文件。
1. 将 `<appSettings>` 部分替换为以下 XML。 然后将 `{your-b2c-tenant}` 替换为你的租户的名称，并将 `{Application ID}` 和 `{Client secret}` 替换为之前记录的值。

    ```xml
    <appSettings>
        <add key="b2c:Tenant" value="{your-b2c-tenant}.onmicrosoft.com" />
        <add key="b2c:ClientId" value="{Application ID}" />
        <add key="b2c:ClientSecret" value="{Client secret}" />
    </appSettings>
    ```

1. 生成解决方案。 在解决方案资源管理器中右键单击 " **B2CGraphClient** " 解决方案，然后选择 "**重新生成解决方案**"。

如果生成成功，则可以在 `B2CGraphClient\bin\Debug`中找到 `B2C.exe` 的控制台应用程序。

## <a name="review-the-sample-code"></a>查看示例代码

若要使用 B2CGraphClient，请打开命令提示符（`cmd.exe`），然后更改为项目的 `Debug` 目录。 然后，运行 `B2C Help` 命令。

```cmd
cd B2CGraphClient\bin\Debug
B2C Help
```

`B2C Help` 命令显示可用子命令的简短说明。 每次调用其中一个子命令时，`B2CGraphClient` 向 Azure AD 图形 API 发送请求。

以下各节讨论了应用程序的代码如何调用 Azure AD 图形 API。

### <a name="get-an-access-token"></a>获取访问令牌

对 Azure AD 图形 API 的任何请求都需要使用访问令牌进行身份验证。 `B2CGraphClient` 使用开源 Active Directory 身份验证库（ADAL）帮助获取访问令牌。 ADAL 提供帮助器 API，并处理几个重要的详细信息（如缓存访问令牌），使得令牌获取更容易。 不过，您不必使用 ADAL 来获取令牌。 可以通过手动编写 HTTP 请求来获取令牌。

> [!NOTE]
> 必须使用 ADAL v2 或更高版本才能获取可与 Azure AD 图形 API 一起使用的访问令牌。 不能使用 ADAL v1。

`B2CGraphClient` 执行时，它将创建 `B2CGraphClient` 类的实例。 此类的构造函数设置 ADAL 身份验证基架：

```csharp
public B2CGraphClient(string clientId, string clientSecret, string tenant)
{
    // The client_id, client_secret, and tenant are provided in Program.cs, which pulls the values from App.config
    this.clientId = clientId;
    this.clientSecret = clientSecret;
    this.tenant = tenant;

    // The AuthenticationContext is ADAL's primary class, in which you indicate the tenant to use.
    this.authContext = new AuthenticationContext("https://login.microsoftonline.com/" + tenant);

    // The ClientCredential is where you pass in your client_id and client_secret, which are
    // provided to Azure AD in order to receive an access_token by using the app's identity.
    this.credential = new ClientCredential(clientId, clientSecret);
}
```

让我们使用 `B2C Get-User` 命令作为示例。

如果调用不带其他参数的 `B2C Get-User`，应用程序将调用 `B2CGraphClient.GetAllUsers()` 方法。 然后 `GetAllUsers()` 调用 `B2CGraphClient.SendGraphGetRequest()`，后者将 HTTP GET 请求提交到 Azure AD 图形 API。 在 `B2CGraphClient.SendGraphGetRequest()` 发送 GET 请求之前，它首先使用 ADAL 获取访问令牌：

```csharp
public async Task<string> SendGraphGetRequest(string api, string query)
{
    // First, use ADAL to acquire a token by using the app's identity (the credential)
    // The first parameter is the resource we want an access_token for; in this case, the Graph API.
    AuthenticationResult result = authContext.AcquireToken("https://graph.windows.net", credential);
    ...
```

可以通过调用 ADAL `AuthenticationContext.AcquireToken()` 方法获取图形 API 的访问令牌。 ADAL 之后会返回表示应用程序标识的 `access_token`。

### <a name="read-users"></a>读取用户

如果要获取用户列表或从 Azure AD 图形 API 获取特定用户，可以将 HTTP `GET` 请求发送到 `/users` 终结点。 要求获取租户中所有用户的请求如下所示：

```HTTP
GET https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

若要查看此请求，请运行：

 ```cmd
 B2C Get-User
 ```

此处需要注意两个要点：

* 使用 ADAL 获取的访问令牌将通过使用 `Bearer` 方案添加到 `Authorization` 标头。
* 对于 B2C 租户，必须使用查询参数 `api-version=1.6`。

这两个详细信息都在 `B2CGraphClient.SendGraphGetRequest()` 方法中进行处理：

```csharp
public async Task<string> SendGraphGetRequest(string api, string query)
{
    ...

    // For B2C user management, be sure to use the 1.6 Graph API version.
    HttpClient http = new HttpClient();
    string url = "https://graph.windows.net/" + tenant + api + "?" + "api-version=1.6";
    if (!string.IsNullOrEmpty(query))
    {
        url += "&" + query;
    }

    // Append the access token for the Graph API to the Authorization header of the request by using the Bearer scheme.
    HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, url);
    request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
    HttpResponseMessage response = await http.SendAsync(request);

    ...
```

### <a name="create-consumer-user-accounts"></a>创建使用者用户帐户

在 B2C 租户中创建用户帐户时，可以将 HTTP `POST` 请求发送到 `/users` 终结点。 以下 HTTP `POST` 请求显示了要在租户中创建的用户示例。

以下请求中的大部分属性都是创建使用者用户所必需的。 已为说明提供 `//` 注释--不要将它们包含在实际请求中。

```HTTP
POST https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 338

{
    // All of these properties are required to create consumer users.

    "accountEnabled": true,
    "signInNames": [                           // controls which identifier the user uses to sign in to the account
        {
            "type": "emailAddress",            // can be 'emailAddress' or 'userName'
            "value": "consumer@fabrikam.com"
        }
    ],
    "creationType": "LocalAccount",            // always set to 'LocalAccount'
    "displayName": "Consumer User",            // a value that can be used for displaying to the end user
    "mailNickname": "cuser",                   // an email alias for the user
    "passwordProfile": {
        "password": "P@ssword!",
        "forceChangePasswordNextLogin": false  // always set to false
    },
    "passwordPolicies": "DisablePasswordExpiration"
}
```

若要查看请求，请运行以下命令之一：

```cmd
B2C Create-User ..\..\..\usertemplate-email.json
B2C Create-User ..\..\..\usertemplate-username.json
```

`Create-User` 命令将包含用户对象的 JSON 表示形式的 JSON 文件作为输入参数。 代码示例中有两个示例 JSON 文件： `usertemplate-email.json` 和 `usertemplate-username.json`。 可以修改这些文件以满足个人需要。 除了上述必填字段，文件中还包含若干可选字段。

有关必填字段和可选字段的详细信息，请参阅[实体和复杂类型引用 |图形 API 引用](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference)。

可以在 `B2CGraphClient.SendGraphPostRequest()`中查看如何构造 POST 请求：

* 它会将访问令牌附加到请求的 `Authorization` 标头。
* 它会设置 `api-version=1.6`。
* 它会将 JSON 用户对象包含在请求的正文中。

> [!NOTE]
> 如果要从现有用户存储迁移的帐户的密码强度低于[Azure AD B2C 强制实施的强密码强度](user-flow-password-complexity.md)，可以通过使用 `passwordPolicies` 属性中的 `DisableStrongPassword` 值来禁用强密码要求。 例如，可以按如下所示修改以前的 "创建用户" 请求： `"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"`。

### <a name="update-consumer-user-accounts"></a>更新使用者用户帐户

更新用户对象时，该进程与用于创建用户对象的过程类似，但使用 HTTP `PATCH` 方法：

```HTTP
PATCH https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 37

{
    "displayName": "Joe Consumer"    // this request updates only the user's displayName
}
```

尝试通过修改 JSON 文件中的某些值更新用户，然后使用 `B2CGraphClient` 运行以下命令之一：

```cmd
B2C Update-User <user-object-id> ..\..\..\usertemplate-email.json
B2C Update-User <user-object-id> ..\..\..\usertemplate-username.json
```

有关如何发送此请求的详细信息，请检查 `B2CGraphClient.SendGraphPatchRequest()` 方法。

### <a name="search-users"></a>搜索用户

可以通过以下方式在 B2C 租户中搜索用户：

* 引用用户的**对象 ID**。
* 引用其登录标识符（`signInNames` 属性）。
* 引用任何有效的 OData 参数。 例如，"givenName"、"姓"、"displayName" 等。

运行以下命令之一以搜索用户：

```cmd
B2C Get-User <user-object-id>
B2C Get-User <filter-query-expression>
```

例如：

```cmd
B2C Get-User 2bcf1067-90b6-4253-9991-7f16449c2d91
B2C Get-User $filter=signInNames/any(x:x/value%20eq%20%27consumer@fabrikam.com%27)
B2C get-user $filter=givenName%20eq%20%27John%27
B2C get-user $filter=surname%20eq%20%27Doe%27
B2C get-user $filter=displayName%20eq%20%27John%20Doe%27
```

### <a name="delete-users"></a>删除用户

若要删除用户，请使用 HTTP `DELETE` 方法，并使用用户的对象 ID 构造 URL：

```HTTP
DELETE https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

若要查看示例，请输入此命令并查看打印到控制台的删除请求：

```cmd
B2C Delete-User <object-id-of-user>
```

有关如何发送此请求的详细信息，请检查 `B2CGraphClient.SendGraphDeleteRequest()` 方法。

除了用户管理之外，还可以使用 Azure AD 图形 API 执行许多其他操作。 [Azure AD 图形 API 参考](/previous-versions/azure/ad/graph/api/api-catalog)提供了每个操作的详细信息以及示例请求。

## <a name="use-custom-attributes"></a>使用自定义属性

大多数使用者应用程序需要存储某些类型的自定义用户配置文件信息。 实现此目的的一种方法是在 B2C 租户中定义自定义属性。 然后，可以采用与处理用户对象上任何其他属性相同的方式来处理该属性。 可以更新属性、删除属性、按属性查询、发送属性等，如登录令牌中的声明一样。

若要在 B2C 租户中定义自定义属性，请参阅 [B2C 自定义属性引用](user-flow-custom-attributes.md)。

可以通过使用以下 `B2CGraphClient` 命令来查看 B2C 租户中定义的自定义属性：

```cmd
B2C Get-B2C-Application
B2C Get-Extension-Attribute <object-id-in-the-output-of-the-above-command>
```

输出显示每个自定义属性的详细信息。 例如：

```json
{
      "odata.type": "Microsoft.DirectoryServices.ExtensionProperty",
      "objectType": "ExtensionProperty",
      "objectId": "cec6391b-204d-42fe-8f7c-89c2b1964fca",
      "deletionTimestamp": null,
      "appDisplayName": "",
      "name": "extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number",
      "dataType": "Integer",
      "isSyncedFromOnPremises": false,
      "targetObjects": [
        "User"
      ]
}
```

可以使用完整名称（例如 `extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number`）作为用户对象的属性。 使用新属性和属性的值更新 JSON 文件，然后运行：

```cmd
B2C Update-User <object-id-of-user> <path-to-json-file>
```

## <a name="next-steps"></a>后续步骤

通过使用 `B2CGraphClient`，可以有一个能以编程方式管理 B2C 租户用户的服务应用程序。 `B2CGraphClient` 使用自己的应用程序标识，向 Azure AD 图形 API 进行验证。 它还会通过使用客户端密码获取令牌。

将此功能合并到自己的应用程序中时，请记住 B2C 应用程序的几个要点：

* 向应用程序授予租户中所需的权限。
* 调用图形 API 时，请使用 `api-version=1.6`。
* 创建和更新使用者用户时，需要几个属性，如上所述。
