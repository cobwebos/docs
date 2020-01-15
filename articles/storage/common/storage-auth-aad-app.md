---
title: 从 Azure AD 获取用于从客户端应用程序授权请求的令牌
titleSuffix: Azure Storage
description: 使用 Azure Active Directory 从客户端应用程序中进行身份验证，获取 OAuth 2.0 令牌，并向 Azure Blob 存储和队列存储授权请求。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: ede43e3ed939083e7b5ff94899d12f6f4795a880
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2020
ms.locfileid: "75941481"
---
# <a name="acquire-a-token-from-azure-ad-for-authorizing-requests-from-a-client-application"></a>从 Azure AD 获取用于从客户端应用程序授权请求的令牌

将 Azure Active Directory （Azure AD）与 Azure Blob 存储或队列存储配合使用的主要优点是，您的凭据不再需要存储在代码中。 相反，你可以从 Microsoft 标识平台（以前称为 Azure AD）请求 OAuth 2.0 访问令牌。 Azure AD 对运行应用程序的安全主体（用户、组或服务主体）进行身份验证。 如果身份验证成功，Azure AD 会返回应用程序的访问令牌，然后应用程序便可以使用访问令牌对 Azure Blob 存储或队列存储的请求进行授权。

本文介绍如何配置本机应用程序或 web 应用程序，以便通过 Microsoft 标识平台2.0 进行身份验证。 代码示例使用 .NET，但其他语言使用类似的方法。 有关 Microsoft 标识平台2.0 的详细信息，请参阅[microsoft 标识平台（v2.0）概述](../../active-directory/develop/v2-overview.md)。

有关 OAuth 2.0 代码授权流的概述，请参阅[使用 OAuth 2.0 代码授权流来授权访问 Azure Active Directory Web 应用程序](../../active-directory/develop/v2-oauth2-auth-code-flow.md)。

## <a name="assign-a-role-to-an-azure-ad-security-principal"></a>向 Azure AD 安全主体分配角色

若要从 Azure 存储应用程序对安全主体进行身份验证，请先为该安全主体配置基于角色的访问控制 (RBAC) 设置。 Azure 存储空间定义包含容器和队列权限的内置 RBAC 角色。 如果将 RBAC 角色分配给安全主体，该安全主体会获得该资源的访问权限。 有关详细信息，请参阅[使用 RBAC 管理对 Azure Blob 和队列数据的访问权限](storage-auth-aad-rbac.md)。

## <a name="register-your-application-with-an-azure-ad-tenant"></a>将应用程序注册到 Azure AD 租户

使用 Azure AD 授予对存储资源的访问权限的第一步是从[Azure 门户](https://portal.azure.com)向 Azure AD 租户注册客户端应用程序。 注册客户端应用程序时，将提供有关要 Azure AD 的应用程序的信息。 Azure AD 随后会提供客户端 ID（也称为*应用程序 ID*）。在运行时，可以使用该 ID 将应用程序与 Azure AD 关联。 若要详细了解客户端 ID，请参阅 [Azure Active Directory 中的应用程序对象和服务主体对象](../../active-directory/develop/app-objects-and-service-principals.md)。

若要注册你的 Azure 存储应用程序，请按照[快速入门：将应用程序注册到 Microsoft 标识平台](../../active-directory/develop/quickstart-configure-app-access-web-apis.md)中所示的步骤进行操作。 下图显示了用于注册 web 应用程序的常见设置：

![显示如何向 Azure AD 注册存储应用程序的屏幕截图](./media/storage-auth-aad-app/app-registration.png)

> [!NOTE]
> 如果将应用程序注册为本机应用程序，可以为**重定向 URI** 指定任何有效的 URI。 对于本机应用程序，此值不必是实际的 URL。 对于 web 应用程序，重定向 URI 必须是有效的 URI，因为它指定提供令牌的 URL。

注册应用程序后，可在“设置”下看到应用程序 ID（或客户端 ID）：

![显示客户端 ID 的屏幕截图](./media/storage-auth-aad-app/app-registration-client-id.png)

有关向 Azure AD 注册应用程序的详细信息，请参阅[将应用程序与 Azure Active Directory](../../active-directory/develop/quickstart-v2-register-an-app.md) 集成。

## <a name="grant-your-registered-app-permissions-to-azure-storage"></a>向 Azure 存储授予注册应用权限

接下来，向应用程序授予调用 Azure 存储 Api 的权限。 此步骤允许你的应用程序通过 Azure AD 向 Azure 存储授权请求。

1. 在已注册应用程序的 "**概述**" 页上，选择 "**查看 API 权限**"。
1. 在 " **API 权限**" 部分中，选择 "**添加权限**"，然后选择 " **Microsoft api**"。
1. 从结果列表中选择 " **Azure 存储空间**"，以显示 "**请求 API 权限**" 窗格。
1. 在 "**你的应用程序需要何种类型的权限？** " 下，观察可用权限类型是否为 "**委托权限**"。 默认情况下，此选项处于选中状态。
1. 在 "**请求 API 权限**" 窗格的 "**选择权限**" 部分中，选中 " **user_impersonation**" 旁边的复选框，然后单击 "**添加权限**"。

    ![显示存储权限的屏幕截图](media/storage-auth-aad-app/registered-app-permissions-1.png)

现在， **API 权限**窗格显示已注册的 Azure AD 应用程序可访问 Microsoft Graph 和 Azure 存储。 首次向 Azure AD 注册应用时，会将权限授予自动 Microsoft Graph。

![显示 register 应用权限的屏幕截图](media/storage-auth-aad-app/registered-app-permissions-2.png)

## <a name="create-a-client-secret"></a>创建客户端机密

在请求令牌时，应用程序需要客户端机密来证明其身份。 若要添加客户端密钥，请执行以下步骤：

1. 在 Azure 门户中导航到你的应用注册。
1. 选择 "**证书" & 机密**"设置。
1. 在 "**客户端密码**" 下，单击 "**新建客户端密码**" 以创建新密钥。
1. 提供机密的说明，并选择所需的过期时间间隔。
1. 立即将新机密的值复制到安全位置。 整个值只显示一次。

    ![显示客户端密钥的屏幕截图](media/storage-auth-aad-app/client-secret.png)

## <a name="client-libraries-for-token-acquisition"></a>用于令牌获取的客户端库

注册应用程序并向其授予访问 Azure Blob 存储或队列存储中数据的权限后，可以将代码添加到应用程序以对安全主体进行身份验证并获取 OAuth 2.0 令牌。 若要进行身份验证并获取令牌，你可以使用其中一个[Microsoft 标识平台身份验证库](../../active-directory/develop/reference-v2-libraries.md)或另一个支持 OpenID connect 1.0 的开源库。 然后，应用程序可以使用访问令牌对 Azure Blob 存储或队列存储的请求进行授权。

有关获取令牌支持的方案的列表，请参阅[Microsoft 身份验证库内容](/azure/active-directory/develop/msal-overview)的[身份验证流](/en-us/azure/active-directory/develop/msal-authentication-flows)部分。

## <a name="well-known-values-for-authentication-with-azure-ad"></a>使用 Azure AD 进行身份验证的已知值

若要使用 Azure AD 验证安全主体的身份，需要在代码中包含一些已知值。

### <a name="azure-ad-authority"></a>Azure AD 颁发机构

对于 Microsoft 公有云，基本 Azure AD 颁发机构如下，其中 *tenant-id* 是 Active Directory 租户 ID（或目录 ID）：

`https://login.microsoftonline.com/<tenant-id>/`

租户 ID 用于标识要用于身份验证的 Azure AD 租户。 它也称为目录 ID。 若要检索租户 ID，请导航到 Azure 门户中的应用注册的 "**概述**" 页，并从该处复制值。

### <a name="azure-storage-resource-id"></a>Azure 存储资源 ID

[!INCLUDE [storage-resource-id-include](../../../includes/storage-resource-id-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>.NET 代码示例：创建块 blob

代码示例展示如何从 Azure AD 获取访问令牌。 访问令牌用于对指定用户进行身份验证，然后授权用于创建块 blob 的请求。 若要让此示例能够正常工作，请首先遵循上述部分列出的步骤。

若要请求令牌，你将需要应用注册的以下值：

- Azure AD 域的名称。 从 Azure Active Directory 的 "**概述**" 页检索此值。
- 租户（或目录） ID。 从应用注册的 "**概述**" 页检索此值。
- 客户端（或应用程序） ID。 从应用注册的 "**概述**" 页检索此值。
- 客户端重定向 URI。 从应用注册的**身份验证**设置中检索此值。
- 客户端机密的值。 从以前复制的位置检索此值。

### <a name="create-a-storage-account-and-container"></a>创建存储帐户和容器

若要运行代码示例，请在与 Azure Active Directory 相同的订阅中创建存储帐户。 然后在该存储帐户中创建一个容器。 示例代码将在此容器中创建块 blob。

接下来，将 "**存储 Blob 数据参与者**" 角色显式分配给运行示例代码所用的用户帐户。 有关如何在 Azure 门户中分配此角色的说明，请参阅在[Azure 门户中使用 RBAC 授予对 Azure blob 和队列数据的访问权限](storage-auth-aad-rbac-portal.md)。

> [!NOTE]
> 创建 Azure 存储帐户时，不会通过 Azure AD 自动分配访问数据的权限。 你必须为自己显式分配一个用于 Azure 存储的 RBAC 角色。 可以在订阅、资源组、存储帐户、容器或队列级别分配它。

### <a name="create-a-web-application-that-authorizes-access-to-blob-storage-with-azure-ad"></a>创建一个授权访问 Blob 存储的 web 应用程序，Azure AD

当应用程序访问 Azure 存储时，它代表用户执行此操作，这意味着可以使用登录用户的权限访问 blob 或队列资源。 若要尝试此代码示例，需要一个 web 应用程序，该应用程序提示用户使用 Azure AD 标识登录。 你可以创建自己的，或使用 Microsoft 提供的示例应用程序。

[GitHub](https://aka.ms/aadstorage)上提供了一个已完成的示例 web 应用程序，该应用程序获取令牌并使用它在 Azure 存储中创建 blob。 查看并运行已完成的示例可能对理解代码示例有帮助。 有关如何运行已完成示例的说明，请参阅标题为 "[查看" 的部分和 "运行已完成的示例](#view-and-run-the-completed-sample)"。

#### <a name="add-references-and-using-statements"></a>添加引用和 using 语句  

在 Visual Studio 中，安装 Azure 存储空间客户端库。 在“工具”菜单中选择“NuGet 包管理器”，然后选择“包管理器控制台”。 在控制台窗口中键入以下命令，从适用于 .NET 的 Azure 存储客户端库安装所需的包：

```console
Install-Package Microsoft.Azure.Storage.Blob
Install-Package Microsoft.Azure.Storage.Common
```

接下来，将以下 using 语句添加到 HomeController.cs 文件：

```csharp
using Microsoft.Identity.Client; //MSAL library for getting the access token
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
```

#### <a name="create-a-block-blob"></a>创建块 Blob

添加以下代码片段以创建块 blob：

```csharp
private static async Task<string> CreateBlob(string accessToken)
{
    // Create a blob on behalf of the user
    TokenCredential tokenCredential = new TokenCredential(accessToken);
    StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

    // Replace the URL below with your storage account URL
    CloudBlockBlob blob =
        new CloudBlockBlob(
            new Uri("https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt"),
            storageCredentials);
    await blob.UploadTextAsync("Blob created by Azure AD authenticated user.");
    return "Blob successfully created";
}
```

> [!NOTE]
> 若要使用 OAuth 2.0 令牌对 blob 和队列操作进行授权，必须使用 HTTPS。

在上面的示例中，.NET 客户端库处理请求的授权以创建块 blob。 用于其他语言的 Azure 存储客户端库还会处理请求的授权。 但是，如果正在使用 REST API 通过 OAuth 标记调用 Azure 存储操作，之后将需要使用 OAuth 标记对请求进行授权。

若要使用 OAuth 访问令牌调用 Blob 和队列服务操作，请使用“持有者令牌”方案在“授权”标头中传递访问令牌，并指定服务版本 2017-11-09 或更高版本，如以下示例所示：

```https
GET /container/file.txt HTTP/1.1
Host: mystorageaccount.blob.core.windows.net
x-ms-version: 2017-11-09
Authorization: Bearer eyJ0eXAiOnJKV1...Xd6j
```

#### <a name="get-an-oauth-token-from-azure-ad"></a>从 Azure AD 获取 OAuth 令牌

接下来，添加一个方法，该方法从 Azure AD 代表用户请求令牌。 此方法定义要为其授予权限的作用域。 有关权限和范围的详细信息，请参阅[Microsoft 标识平台终结点中的权限和同意](../../active-directory/develop/v2-permissions-and-consent.md)。

使用资源 ID 构造要为其获取令牌的范围。 该示例通过将资源 ID 与内置 `user_impersonation` 作用域结合使用来构造作用域，这表示该令牌是代表用户请求的。

请记住，你可能需要向用户提供一个接口，该接口允许用户同意代表他们请求令牌。 在需要同意时，此示例将捕获**MsalUiRequiredException** ，并调用另一个方法来促进同意请求：

```csharp
public async Task<IActionResult> Blob()
{
    var scopes = new string[] { "https://storage.azure.com/user_impersonation" };
    try
    {
        var accessToken =
            await _tokenAcquisition.GetAccessTokenOnBehalfOfUser(HttpContext, scopes);
        ViewData["Message"] = await CreateBlob(accessToken);
        return View();
    }
    catch (MsalUiRequiredException ex)
    {
        AuthenticationProperties properties =
            BuildAuthenticationPropertiesForIncrementalConsent(scopes, ex);
        return Challenge(properties);
    }
}
```

许可是指用户授权应用程序代表他们访问受保护资源的过程。 Microsoft 标识平台2.0 支持增量许可，这意味着，安全主体最初可以请求一组最小的权限，并根据需要在一段时间内添加权限。 当代码请求访问令牌时，在任何给定时间通过 `scope` 参数指定应用所需的权限范围。 有关增量许可的详细信息，请参阅有关[更新 Microsoft 标识平台（v2.0）的原因](../../active-directory/develop/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent)中标题为 "**增量和动态同意**" 的部分。

以下方法将构造用于请求增量许可的身份验证属性：

```csharp
private AuthenticationProperties BuildAuthenticationPropertiesForIncrementalConsent(string[] scopes,
                                                                                    MsalUiRequiredException ex)
{
    AuthenticationProperties properties = new AuthenticationProperties();

    // Set the scopes, including the scopes that ADAL.NET or MSAL.NET need for the Token cache.
    string[] additionalBuildInScopes = new string[] { "openid", "offline_access", "profile" };
    properties.SetParameter<ICollection<string>>(OpenIdConnectParameterNames.Scope,
                                                 scopes.Union(additionalBuildInScopes).ToList());

    // Attempt to set the login_hint so that the logged-in user is not presented
    // with an account selection dialog.
    string loginHint = HttpContext.User.GetLoginHint();
    if (!string.IsNullOrWhiteSpace(loginHint))
    {
        properties.SetParameter<string>(OpenIdConnectParameterNames.LoginHint, loginHint);

        string domainHint = HttpContext.User.GetDomainHint();
        properties.SetParameter<string>(OpenIdConnectParameterNames.DomainHint, domainHint);
    }

    // Specify any additional claims that are required (for instance, MFA).
    if (!string.IsNullOrEmpty(ex.Claims))
    {
        properties.Items.Add("claims", ex.Claims);
    }

    return properties;
}
```

## <a name="view-and-run-the-completed-sample"></a>查看并运行已完成的示例

若要运行示例应用程序，请首先从[GitHub](https://github.com/Azure-Samples/storage-dotnet-azure-ad-msal)克隆或下载该应用程序。 然后更新应用程序，如以下各节所述。

### <a name="provide-values-in-the-settings-file"></a>在设置文件中提供值

接下来，用自己的值更新*appsettings*文件，如下所示：

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "<azure-ad-domain-name>.onmicrosoft.com",
    "TenantId": "<tenant-id>",
    "ClientId": "<client-id>",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc",

    // To call an API
    "ClientSecret": "<client-secret>"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="update-the-storage-account-and-container-name"></a>更新存储帐户和容器名称

在*HomeController.cs*文件中，更新引用块 BLOB 的 URI 以使用存储帐户和容器的名称：

```csharp
CloudBlockBlob blob = new CloudBlockBlob(
                      new Uri("https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt"),
                      storageCredentials);
```

### <a name="enable-implicit-grant-flow"></a>启用隐式授权流

若要运行该示例，可能需要为应用注册配置隐式授权流。 执行以下步骤:

1. 在 Azure 门户中导航到你的应用注册。
1. 在 "管理" 部分中，选择**身份验证**设置。
1. 在 "**高级设置**" 下的 "**隐式授权**" 部分中，选中相应的复选框以启用访问令牌和 ID 令牌，如下图所示：

    ![显示如何启用隐式授予流设置的屏幕截图](media/storage-auth-aad-app/enable-implicit-grant-flow.png)

### <a name="update-the-port-used-by-localhost"></a>更新 localhost 使用的端口

运行该示例时，可能会发现需要更新应用注册中指定的重定向 URI，才能使用在运行时分配的*本地主机*端口。 若要将重定向 URI 更新为使用分配的端口，请执行以下步骤：

1. 在 Azure 门户中导航到你的应用注册。
1. 在 "管理" 部分中，选择**身份验证**设置。
1. 在 "**重定向 uri**" 下，编辑端口以匹配示例应用程序使用的端口，如下图所示：

    ![显示应用注册的重定向 Uri 的屏幕截图](media/storage-auth-aad-app/redirect-uri.png)

## <a name="next-steps"></a>后续步骤

- 若要了解有关 Microsoft 标识平台的详细信息，请参阅[microsoft 标识平台](https://docs.microsoft.com/azure/active-directory/develop/)。
- 若要了解有关 Azure 存储的 RBAC 角色的详细信息，请参阅[使用 Rbac 管理对存储数据的访问权限](storage-auth-aad-rbac.md)。
- 若要了解如何在 azure 存储中使用 Azure 资源的托管标识，请参阅使用[Azure 资源的 Azure Active Directory 和托管标识对 blob 和队列的访问权限进行身份验证](storage-auth-aad-msi.md)。
