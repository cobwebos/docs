---
title: "Azure Active Directory B2C：使用图形 API | Microsoft Docs"
description: "如何通过使用应用程序标识自动执行该过程为 B2C 租户调用图形 API。"
services: active-directory-b2c
documentationcenter: .net
author: dstrockis
manager: mbaldwin
editor: bryanla
ms.assetid: f9904516-d9f7-43b1-ae4f-e4d9eb1c67a0
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
translationtype: Human Translation
ms.sourcegitcommit: e65393c9582056f84530a32804e0d82fd451b688
ms.openlocfilehash: a932b617d57184ef714bf18f1e1e23599db52487


---
# <a name="azure-ad-b2c-use-the-graph-api"></a>Azure AD B2C：使用图形 API
Azure Active Directory (Azure AD) B2C 租户往往会非常大。 这意味着许多常见的租户管理任务需要以编程方式执行。 用户管理是一个主要示例。 你可能需要将现有用户存储迁移到 B2C 租户。 你可能想要在自己的页面上托管用户注册，并在 Azure AD 后台创建用户帐户。 这些类型的任务需要能够创建、读取、更新和删除用户帐户。 可以通过使用 Azure AD 图形 API 来执行这些任务。

对于 B2C 租户，与图形 API 通信有两种主要模式。

* 对于一次性运行的交互式任务，用户应在执行这些任务时充当 B2C 租户中的管理员帐户。 此模式需要管理员使用凭据登录，然后才能执行对图形 API 的任何调用。
* 对于自动化的连续任务，应该使用你提供有所需特权的某些类型的服务帐户来执行管理任务。 在 Azure AD 中，可以通过注册应用程序并向 Azure AD 进行身份验证来执行此操作。 这通过利用使用 [OAuth 2.0 客户端凭据授予](../active-directory/develop/active-directory-authentication-scenarios.md#daemon-or-server-application-to-web-api)的**应用程序 ID** 来完成。 在这种情况下，应用程序作为其本身而不是用户来调用图形 API。

在本文中，我们将讨论如何执行自动使用案例。 为了演示，我们将构建一个执行用户创建、读取、更新和删除 (CRUD) 操作的 .NET 4.5 `B2CGraphClient`。 客户端将拥有一个 Windows 命令行接口 (CLI)，允许用户调用各种方法。 然而，代码被编写为以非交互式自动化的方式表现。

## <a name="get-an-azure-ad-b2c-tenant"></a>获取 Azure AD B2C 租户
租户中需要有一个 Azure AD B2C 租户和一个全局管理员帐户，才可创建应用程序或用户之前，或与 Azure AD 进行交互之前。 如果尚没有租户，请参阅 [Azure AD B2C 入门](active-directory-b2c-get-started.md)。

## <a name="register-a-service-application-in-your-tenant"></a>在租户中注册服务应用程序
拥有 B2C 租户后，需要使用 Azure AD PowerShell cmdlet 创建服务应用程序。
首先，下载并安装 [Microsoft Online Services 登录助手](http://go.microsoft.com/fwlink/?LinkID=286152)。 然后下载并安装[用于 Windows PowerShell 的 64 位 Azure Active Directory 模块](http://go.microsoft.com/fwlink/p/?linkid=236297)。

> [!IMPORTANT]
> 若要搭配使用 B2C 租户与图形 API，需要使用 PowerShell 注册一个专用应用程序。 请遵循本文说明来执行此操作。 不能重复使用在 Azure 门户中注册的现有 B2C 应用程序。
> 
> 

安装 PowerShell 模块后，打开 PowerShell 并连接到 B2C 租户。 运行 `Get-Credential` 后，系统将提示输入用户名和密码，输入 B2C 租户管理员帐户的用户名和密码。

```
> $msolcred = Get-Credential
> Connect-MsolService -credential $msolcred
```

在创建应用程序之前，需要生成新的**客户端密码**。  应用程序将使用客户端密码向 Azure AD 验证并获取访问令牌。 可以在 PowerShell 中生成有效的密码：

```
> $bytes = New-Object Byte[] 32
> $rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
> $rand.GetBytes($bytes)
> $rand.Dispose()
> $newClientSecret = [System.Convert]::ToBase64String($bytes)
> $newClientSecret
```

最后一个命令应该会打印新的客户端密码。 将其复制到安全的地方。 稍后需要用到此信息。 现在，可以通过提供新的客户端密码作为应用凭据来创建应用程序：

```
> New-MsolServicePrincipal -DisplayName "My New B2C Graph API App" -Type password -Value $newClientSecret

DisplayName           : My New B2C Graph API App
ServicePrincipalNames : {dd02c40f-1325-46c2-a118-4659db8a55d5}
ObjectId              : e2bde258-6691-410b-879c-b1f88d9ef664
AppPrincipalId        : dd02c40f-1325-46c2-a118-4659db8a55d5
TrustedForDelegation  : False
AccountEnabled        : True
Addresses             : {}
KeyType               : Password
KeyId                 : a261e39d-953e-4d6a-8d70-1f915e054ef9
StartDate             : 9/2/2015 1:33:09 AM
EndDate               : 9/2/2016 1:33:09 AM
Usage                 : Verify
```

如果成功创建应用程序，应该会打印出如上所示的应用程序属性。 你将需要 `ObjectId` 和 `AppPrincipalId`，因此也要复制这些值。

在 B2C 租户中创建应用程序后，需要为其分配执行用户 CRUD 操作所需的权限。 为应用程序分配三个角色：目录读取器（用于读取用户）、目录编写器（用于创建和更新用户）和用户帐户管理员（用于删除用户）。 这些角色具有已知的标识符，因此可以将 `-RoleMemberObjectId` 参数替换为上述 `ObjectId`，并运行以下命令。 若要查看所有目录角色的列表，请尝试运行 `Get-MsolRole`。

```
> Add-MsolRoleMember -RoleObjectId 88d8e3e3-8f55-4a1e-953a-9b9898b8876b -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
> Add-MsolRoleMember -RoleObjectId 9360feb5-f418-4baa-8175-e2a00bac4301 -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
> Add-MsolRoleMember -RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1 -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
```

现在就有了一个有权限从你的 B2C 租户创建、读取、更新和删除用户的应用程序。

## <a name="download-configure-and-build-the-sample-code"></a>下载、配置和构建示例代码
首先，下载示例代码并运行。 下面将详细地查看该代码。  可以[将示例代码下载为 .zip 文件](https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet/archive/master.zip)。 也可以将其克隆到所选择的目录中：

```
git clone https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet.git
```

在 Visual Studio 中打开 `B2CGraphClient\B2CGraphClient.sln` Visual Studio 解决方案。 在 `B2CGraphClient` 项目中，打开文件 `App.config`。 使用自己的值替换这三个应用设置：

```
<appSettings>
    <add key="b2c:Tenant" value="contosob2c.onmicrosoft.com" />
    <add key="b2c:ClientId" value="{The AppPrincipalId from above}" />
    <add key="b2c:ClientSecret" value="{The client secret you generated above}" />
</appSettings>
```

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

接下来，右键单击 `B2CGraphClient` 解决方案并重新生成示例。 如果成功，则现在 `B2CGraphClient\bin\Debug` 中应该有一个可执行文件 `B2C.exe`。

## <a name="build-user-crud-operations-by-using-the-graph-api"></a>使用图形 API 构建用户 CRUD 操作
要使用 B2CGraphClient，请打开 `cmd` Windows 命令提示符，并将目录更改为 `Debug` 目录。 然后运行 `B2C Help` 命令。

```
> cd B2CGraphClient\bin\Debug
> B2C Help
```

此操作将显示每个命令的简要说明。 每次调用其中一个命令时，`B2CGraphClient` 都会向 Azure AD 图形 API 发出请求。

### <a name="get-an-access-token"></a>获取访问令牌
对图形 API 的任何请求都需要访问令牌来进行身份验证。 `B2CGraphClient` 使用开放源 Active Directory Authentication Library (ADAL) 来帮助获取访问令牌。 通过提供简单的 API 并处理某些重要细节（如缓存访问令牌），ADAL 使令牌获取变得更容易。 不过，不必非得使用 ADAL 获得令牌。 也可以通过制作 HTTP 请求获取令牌。

> [!NOTE]
> 此代码示例使用 ADAL v2 以便与图形 API 进行通信。  必须使用 ADAL v2 或 v3，获取可配合 Azure AD 图形 API 使用的访问令牌。
> 
> 

`B2CGraphClient` 运行时，它将创建 `B2CGraphClient` 类的实例。 此类的构造函数会设置 ADAL 身份验证基架：

```C#
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

我们将以 `B2C Get-User` 命令为例。 调用 `B2C Get-User` 而没有任何其他输入时，CLI 将调用 `B2CGraphClient.GetAllUsers(...)` 方法。 此方法调用 `B2CGraphClient.SendGraphGetRequest(...)`，这会向图形 API 提交 HTTP GET 请求。 在 `B2CGraphClient.SendGraphGetRequest(...)` 发送 GET 请求之前，它首先会使用 ADAL 获取访问令牌：

```C#
public async Task<string> SendGraphGetRequest(string api, string query)
{
    // First, use ADAL to acquire a token by using the app's identity (the credential)
    // The first parameter is the resource we want an access_token for; in this case, the Graph API.
    AuthenticationResult result = authContext.AcquireToken("https://graph.windows.net", credential);

    ...

```

可以通过调用 ADAL `AuthenticationContext.AcquireToken(...)` 方法获取图形 API 的访问令牌。 ADAL 之后会返回表示应用程序标识的 `access_token`。

### <a name="read-users"></a>读取用户
想要从图形 API 获取用户列表或获取特定用户时，可以向 `/users` 终结点发送 HTTP `GET` 请求。 要求获取租户中所有用户的请求如下所示：

```
GET https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

若要查看此请求，请运行：

 ```
 > B2C Get-User
 ```

此处需要注意两个要点：

* 通过 ADAL 获取的访问令牌通过使用 `Bearer` 方案添加到 `Authorization` 标头。
* 对于 B2C 租户，必须使用查询参数 `api-version=1.6`。

这两个详细信息都在 `B2CGraphClient.SendGraphGetRequest(...)` 方法中进行处理：

```C#
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
在 B2C 租户中创建用户帐户时，可以向 `/users` 终结点发送 HTTP `POST` 请求：

```
POST https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 338

{
    // All of these properties are required to create consumer users.

    "accountEnabled": true,
    "signInNames": [                            // controls which identifier the user uses to sign in to the account
        {
            "type": "emailAddress",             // can be 'emailAddress' or 'userName'
            "value": "joeconsumer@gmail.com"
        }
    ],
    "creationType": "LocalAccount",            // always set to 'LocalAccount'
    "displayName": "Joe Consumer",                // a value that can be used for displaying to the end user
    "mailNickname": "joec",                        // an email alias for the user
    "passwordProfile": {
        "password": "P@ssword!",
        "forceChangePasswordNextLogin": false   // always set to false
    },
    "passwordPolicies": "DisablePasswordExpiration"
}
```

此请求中的大多数属性都是创建使用者用户所必需的。 若要了解详细信息，请单击[此处](https://msdn.microsoft.com/library/azure/ad/graph/api/users-operations#CreateLocalAccountUser)。 请注意，已包含 `//` 注释用于说明。 不要将它们包含在实际请求中。

若要查看请求，请运行以下命令之一：

```
> B2C Create-User ..\..\..\usertemplate-email.json
> B2C Create-User ..\..\..\usertemplate-username.json
```

`Create-User` 命令将 .json 文件用作输入参数。 这包含用户对象的 JSON 表示形式。 示例代码中有两个示例 .json 文件：`usertemplate-email.json` 和 `usertemplate-username.json`。 可以修改这些文件以满足个人需要。 除了上述必填字段外，这些文件中还包含一些可以使用的可选字段。 有关可选字段的详细信息，请参阅 [Azure AD 图形 API 实体引用](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity)。

你会看到如何在 `B2CGraphClient.SendGraphPostRequest(...)` 中构造 POST 请求。

* 它会将访问令牌附加到请求的 `Authorization` 标头。
* 它会设置 `api-version=1.6`。
* 它会将 JSON 用户对象包含在请求的正文中。

> [!NOTE]
> 如果要从现有用户存储迁移的帐户的密码强度低于 [Azure AD B2C 强制实施的强密码强度](https://msdn.microsoft.com/library/azure/jj943764.aspx)，可以使用 `passwordPolicies` 属性中的 `DisableStrongPassword` 值禁用强密码要求。 例如，可以修改上面提供的创建用户请求，如下所示：`"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"`。
> 
> 

### <a name="update-consumer-user-accounts"></a>更新使用者用户帐户
更新用户对象时，该过程与用于创建用户对象的过程类似。 但此过程使用 HTTP `PATCH` 方法：

```
PATCH https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 37

{
    "displayName": "Joe Consumer",                // this request updates only the user's displayName
}
```

尝试通过新数据更新 JSON 文件，从而更新用户。 然后，可以使用 `B2CGraphClient` 运行以下命令之一：

```
> B2C Update-User <user-object-id> ..\..\..\usertemplate-email.json
> B2C Update-User <user-object-id> ..\..\..\usertemplate-username.json
```

有关如何发送此请求的详细信息，请检查 `B2CGraphClient.SendGraphPatchRequest(...)` 方法。

### <a name="search-users"></a>搜索用户
可以通过两种方式在 B2C 租户中搜索用户。 第一种方式是使用用户的对象 ID；第二种方式是使用用户的登录标识符（即 `signInNames` 属性）。

运行以下命令之一，搜索特定用户：

```
> B2C Get-User <user-object-id>
> B2C Get-User <filter-query-expression>
```

下面是几个示例：

```
> B2C Get-User 2bcf1067-90b6-4253-9991-7f16449c2d91
> B2C Get-User $filter=signInNames/any(x:x/value%20eq%20%27joeconsumer@gmail.com%27)
```

### <a name="delete-users"></a>删除用户
删除用户的过程非常简单。 使用 HTTP `DELETE` 方法并构造具有正确对象 ID 的 URL：

```
DELETE https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

若要查看示例，请输入此命令并查看打印到控制台的删除请求：

```
> B2C Delete-User <object-id-of-user>
```

有关如何发送此请求的详细信息，请检查 `B2CGraphClient.SendGraphDeleteRequest(...)` 方法。

除了用户管理之外，还可以使用 Azure AD 图形 API 执行许多其他操作。 [Azure AD 图形 API 参考](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)提供了每个操作的详细信息以及示例请求。

## <a name="use-custom-attributes"></a>使用自定义属性
大多数使用者应用程序需要存储某些类型的自定义用户配置文件信息。 一种方法是在 B2C 租户中定义自定义属性。 然后，可以按照处理用户对象上的任何其他属性的方式一样来处理该属性。 可以更新属性、删除属性、按属性查询、发送属性等，如登录令牌中的声明一样。

若要在 B2C 租户中定义自定义属性，请参阅 [B2C 自定义属性引用](active-directory-b2c-reference-custom-attr.md)。

可以使用 `B2CGraphClient` 查看 B2C 租户中定义的自定义属性：

```
> B2C Get-B2C-Application
> B2C Get-Extension-Attribute <object-id-in-the-output-of-the-above-command>
```

这些函数的输出显示每个自定义属性的详细信息，例如：

```JSON
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

可以使用完整名称（例如 `extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number`）作为用户对象的属性。  使用新属性和该属性的值更新 .json 文件，然后运行：

```
> B2C Update-User <object-id-of-user> <path-to-json-file>
```

通过使用 `B2CGraphClient`，可以有一个能以编程方式管理 B2C 租户用户的服务应用程序。 `B2CGraphClient` 使用自己的应用程序标识，向 Azure AD 图形 API 进行验证。 它还会通过使用客户端密码获取令牌。 将此功能并入应用程序时，请记住 B2C 应用的几个要点：

* 需要将租户中的适当权限授予应用程序。
* 现在，需要使用 ADAL v2 获取访问令牌。 （也可以直接发送协议消息，而不使用库。）
* 调用图形 API 时，请使用 `api-version=1.6`。
* 创建和更新使用者用户时，需要几个属性，如上所述。

对于想要使用图形 API 在 B2C 租户上执行的操作，如有任何疑问或要求，请在本文留下批注，或在 GitHub 代码示例存储库中提出问题。




<!--HONumber=Feb17_HO1-->


