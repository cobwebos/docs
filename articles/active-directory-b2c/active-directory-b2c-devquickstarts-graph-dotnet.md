---
title: 使用图形 API - Azure AD B2C | Microsoft 文档
description: 如何通过使用应用程序标识自动执行该过程为 B2C 租户调用图形 API。
services: active-directory-b2c
documentationcenter: .net
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 08/07/2017
ms.author: davidmu
ms.openlocfilehash: 731ff24fe9cc1b5dbf0c597139a96ae80b863cc2
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
ms.locfileid: "32140025"
---
# <a name="azure-ad-b2c-use-the-azure-ad-graph-api"></a>Azure AD B2C：使用 Azure AD Graph API

>[!NOTE]
> 必须使用 [Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-operations-overview?f=255&MSPPError=-2147217396) 管理 Azure AD B2C 目录中的用户。 这不同于 Microsoft 图形 API。 在[此处](https://blogs.msdn.microsoft.com/aadgraphteam/2016/07/08/microsoft-graph-or-azure-ad-graph/)了解更多信息。

Azure Active Directory (Azure AD) B2C 租户往往会非常大。 这意味着许多常见的租户管理任务需要以编程方式执行。 用户管理是一个主要示例。 可能需要将现有用户存储迁移到 B2C 租户。 可能想要在自己的页面上托管用户注册，并在 Azure AD B2C 目录后台创建用户帐户。 这些类型的任务需要能够创建、读取、更新和删除用户帐户。 可以通过使用 Azure AD 图形 API 来执行这些任务。

对于 B2C 租户，与图形 API 通信有两种主要模式。

* 对于一次性运行的交互式任务，用户应在执行这些任务时充当 B2C 租户中的管理员帐户。 此模式需要管理员使用凭据登录，才能执行对图形 API 的任何调用。
* 对于自动化的连续任务，应该使用提供有所需特权的某些类型的服务帐户来执行管理任务。 在 Azure AD 中，可以通过注册应用程序并向 Azure AD 进行身份验证来执行此操作。 这通过利用使用 [OAuth 2.0 客户端凭据授予](../active-directory/develop/active-directory-authentication-scenarios.md#daemon-or-server-application-to-web-api)的**应用程序 ID** 来完成。 在这种情况下，应用程序作为其本身而不是用户来调用图形 API。

本文介绍如何执行自动使用案例。 构建一个执行用户创建、读取、更新和删除 (CRUD) 操作的 .NET 4.5 `B2CGraphClient`。 客户端将拥有一个 Windows 命令行接口 (CLI)，允许用户调用各种方法。 然而，代码被编写为以非交互式自动化的方式表现。

## <a name="get-an-azure-ad-b2c-tenant"></a>获取 Azure AD B2C 租户
创建应用程序或用户前，需要一个 Azure AD B2C 租户。 如果尚没有租户，请参阅 [Azure AD B2C 入门](active-directory-b2c-get-started.md)。

## <a name="register-your-application-in-your-tenant"></a>在租户中注册应用程序
拥有 B2C 租户后，需通过 [Azure 门户](https://portal.azure.com)注册应用程序。

> [!IMPORTANT]
> 若要将图形 API 用于 B2C 租户，需使用 Azure 门户中的“应用注册”服务（而非 Azure AD B2C 的“应用程序”菜单）注册应用程序。 根据以下介绍进入相应菜单。 不能重复使用在 Azure AD B2C 的“应用程序”菜单中注册的现有 B2C 应用程序。

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 通过在页面右上角选择帐户，选择 Azure AD B2C 租户。
3. 在左侧导航窗格中，选择“所有服务”，单击“应用注册”，并单击“添加”。
4. 根据提示创建新的应用程序。 
    1. 选择“Web 应用/API”作为应用程序类型。    
    2. 提供任一登录 URL（例如 https://B2CGraphAPI) ，它与此示例不相关）。  
5. 应用程序现在会显示在应用程序列表中，单击它以获取**应用程序 ID**（也称为客户端 ID）。 复制它，因为会在后面的部分用到它。
6. 在“设置”菜单中，单击“密钥”。
7. 在“密码”部分输入密钥说明，并选择持续时间，然后单击“保存”。 复制该密钥值（也称为客户端密码），便于在之后的章节中使用。

## <a name="configure-create-read-and-update-permissions-for-your-application"></a>为应用程序配置创建、读取和更新权限
现在，需要配置应用程序，以获取所有所需的创建、读取、更新和删除用户的权限。

1. 继续在 Azure 门户的“应用注册”菜单中，选择应用程序。
2. 在“设置”菜单中，单击“所需的权限”。
3. 在“所需的权限”菜单中，单击“Windows Azure Active Directory”。
4. 在“启用访问权限”菜单中，从“应用程序权限”中选择“读取和写入目录数据”权限，并单击“保存”。
5. 最后，返回“所需的权限”菜单，单击“授予权限”按钮。

现在就有了一个有权限从 B2C 租户创建、读取和更新用户的应用程序。

> [!NOTE]
> 权限授予可能需要几分钟才能完全处理。
> 
> 

## <a name="configure-delete-permissions-for-your-application"></a>为应用程序配置删除权限
目前，“读取和写入目录数据”权限**不**包括任何删除权限，如删除用户。 如果想要使应用程序能够删除用户，就需要执行涉及 PowerShell 的这些额外步骤，否则，可以跳到下一部分。

首先，请安装 [Azure AD PowerShell v1 模块 (MSOnline)](https://docs.microsoft.com/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0)（如果还没有安装）：

```powershell
Install-Module MSOnline
```

安装 PowerShell 模块后，连接到 Azure AD B2C 租户。

> [!IMPORTANT]
> 需要使用 B2C 租户**本地**的 B2C 租户管理员帐户。 这些帐户如下所示：myusername@myb2ctenant.onmicrosoft.com。

```powershell
Connect-MsolService
```

现在我们会在以下脚本中使用**应用程序 ID** 为应用程序分配用户帐户管理员角色，以允许应用程序删除用户。 这些角色具有已知的标识符，因此，只需在下面的脚本中输入**应用程序 ID**。

```powershell
$applicationId = "<YOUR_APPLICATION_ID>"
$sp = Get-MsolServicePrincipal -AppPrincipalId $applicationId
Add-MsolRoleMember -RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1 -RoleMemberObjectId $sp.ObjectId -RoleMemberType servicePrincipal
```

现在，应用程序也具有从 B2C 租户中删除用户的权限。

## <a name="download-configure-and-build-the-sample-code"></a>下载、配置和构建示例代码
首先，下载示例代码并运行。 下面将详细地查看该代码。  可以[将示例代码下载为 .zip 文件](https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet/archive/master.zip)。 也可以将其克隆到所选择的目录中：

```cmd
git clone https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet.git
```

在 Visual Studio 中打开 `B2CGraphClient\B2CGraphClient.sln` Visual Studio 解决方案。 在 `B2CGraphClient` 项目中，打开文件 `App.config`。 使用自己的值替换这三个应用设置：

```xml
<appSettings>
    <add key="b2c:Tenant" value="{Your Tenant Name}" />
    <add key="b2c:ClientId" value="{The ApplicationID from above}" />
    <add key="b2c:ClientSecret" value="{The Key from above}" />
</appSettings>
```

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

接下来，右键单击 `B2CGraphClient` 解决方案并重新生成示例。 如果成功，则现在 `B2CGraphClient\bin\Debug` 中应该有一个可执行文件 `B2C.exe`。

## <a name="build-user-crud-operations-by-using-the-graph-api"></a>使用图形 API 构建用户 CRUD 操作
要使用 B2CGraphClient，请打开 `cmd` Windows 命令提示符，并将目录更改为 `Debug` 目录。 然后运行 `B2C Help` 命令。

```cmd
cd B2CGraphClient\bin\Debug
B2C Help
```

此操作会显示每个命令的简要说明。 每次调用其中一个命令时，`B2CGraphClient` 都会向 Azure AD 图形 API 发出请求。

### <a name="get-an-access-token"></a>获取访问令牌
对图形 API 的任何请求都需要访问令牌来进行身份验证。 `B2CGraphClient` 使用开放源 Active Directory Authentication Library (ADAL) 来帮助获取访问令牌。 通过提供简单的 API 并处理某些重要细节（如缓存访问令牌），ADAL 使令牌获取变得更容易。 不过，不必非得使用 ADAL 获得令牌。 也可以通过制作 HTTP 请求获取令牌。

> [!NOTE]
> 此代码示例使用 ADAL v2 以便与图形 API 进行通信。  必须使用 ADAL v2 或 v3，获取可配合 Azure AD 图形 API 使用的访问令牌。
> 
> 

`B2CGraphClient` 运行时，它将创建 `B2CGraphClient` 类的实例。 此类的构造函数会设置 ADAL 身份验证基架：

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

我们以 `B2C Get-User` 命令为例。 调用 `B2C Get-User` 而没有任何其他输入时，CLI 将调用 `B2CGraphClient.GetAllUsers(...)` 方法。 此方法调用 `B2CGraphClient.SendGraphGetRequest(...)`，这会向图形 API 提交 HTTP GET 请求。 在 `B2CGraphClient.SendGraphGetRequest(...)` 发送 GET 请求之前，它首先会使用 ADAL 获取访问令牌：

```csharp
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

 ```cmd
 B2C Get-User
 ```

此处需要注意两个要点：

* 通过 ADAL 获取的访问令牌通过使用 `Bearer` 方案添加到 `Authorization` 标头。
* 对于 B2C 租户，必须使用查询参数 `api-version=1.6`。

这两个详细信息都在 `B2CGraphClient.SendGraphGetRequest(...)` 方法中进行处理：

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

```cmd
B2C Create-User ..\..\..\usertemplate-email.json
B2C Create-User ..\..\..\usertemplate-username.json
```

`Create-User` 命令将 .json 文件用作输入参数。 这包含用户对象的 JSON 表示形式。 示例代码中有两个示例 .json 文件：`usertemplate-email.json` 和 `usertemplate-username.json`。 可以修改这些文件以满足个人需要。 除了上述必填字段外，这些文件中还包含一些可以使用的可选字段。 有关可选字段的详细信息，请参阅 [Azure AD 图形 API 实体引用](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity)。

会看到如何在 `B2CGraphClient.SendGraphPostRequest(...)` 中构造 POST 请求。

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

```cmd
B2C Update-User <user-object-id> ..\..\..\usertemplate-email.json
B2C Update-User <user-object-id> ..\..\..\usertemplate-username.json
```

有关如何发送此请求的详细信息，请检查 `B2CGraphClient.SendGraphPatchRequest(...)` 方法。

### <a name="search-users"></a>搜索用户
可以通过两种方式在 B2C 租户中搜索用户。 第一种方式是使用用户的对象 ID；第二种方式是使用用户的登录标识符（即 `signInNames` 属性）。

运行以下命令之一，搜索特定用户：

```cmd
B2C Get-User <user-object-id>
B2C Get-User <filter-query-expression>
```

下面是几个示例：

```cmd
B2C Get-User 2bcf1067-90b6-4253-9991-7f16449c2d91
B2C Get-User $filter=signInNames/any(x:x/value%20eq%20%27joeconsumer@gmail.com%27)
```

### <a name="delete-users"></a>删除用户
删除用户的过程非常简单。 使用 HTTP `DELETE` 方法并构造具有正确对象 ID 的 URL：

```
DELETE https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

若要查看示例，请输入此命令并查看打印到控制台的删除请求：

```cmd
B2C Delete-User <object-id-of-user>
```

有关如何发送此请求的详细信息，请检查 `B2CGraphClient.SendGraphDeleteRequest(...)` 方法。

除了用户管理之外，还可以使用 Azure AD 图形 API 执行许多其他操作。 [Azure AD 图形 API 参考](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)提供了每个操作的详细信息以及示例请求。

## <a name="use-custom-attributes"></a>使用自定义属性
大多数使用者应用程序需要存储某些类型的自定义用户配置文件信息。 一种方法是在 B2C 租户中定义自定义属性。 然后，可按照处理用户对象上的任何其他属性的方式一样来处理该属性。 可以更新属性、删除属性、按属性查询、发送属性等，如登录令牌中的声明一样。

若要在 B2C 租户中定义自定义属性，请参阅 [B2C 自定义属性引用](active-directory-b2c-reference-custom-attr.md)。

可以使用 `B2CGraphClient` 查看 B2C 租户中定义的自定义属性：

```cmd
B2C Get-B2C-Application
B2C Get-Extension-Attribute <object-id-in-the-output-of-the-above-command>
```

这些函数的输出显示每个自定义属性的详细信息，例如：

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

可以使用完整名称（例如 `extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number`）作为用户对象的属性。  使用新属性和该属性的值更新 .json 文件，并运行：

```cmd
B2C Update-User <object-id-of-user> <path-to-json-file>
```

通过使用 `B2CGraphClient`，可以有一个能以编程方式管理 B2C 租户用户的服务应用程序。 `B2CGraphClient` 使用自己的应用程序标识，向 Azure AD 图形 API 进行验证。 它还会通过使用客户端密码获取令牌。 将此功能并入应用程序时，请记住 B2C 应用的几个要点：

* 需要将租户中的适当权限授予应用程序。
* 现在，需要使用 ADAL（而非 MSAL）获取访问令牌。 （也可以直接发送协议消息，而不使用库。）
* 调用图形 API 时，请使用 `api-version=1.6`。
* 创建和更新使用者用户时，需要几个属性，如上所述。

对于想要使用图形 API 在 B2C 租户上执行的操作，如有任何疑问或要求，请在本文留下批注，或在 GitHub 代码示例存储库中提出问题。

