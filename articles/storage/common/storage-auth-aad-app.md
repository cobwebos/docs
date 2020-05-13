---
title: 从 Azure AD 获取用于从客户端应用程序授权请求的令牌
titleSuffix: Azure Storage
description: 使用 Azure Active Directory 从客户端应用程序内部进行身份验证、获取 OAuth 2.0 令牌，以及授权对 Azure Blob 存储和队列存储的请求。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.subservice: common
ms.custom: has-adal-ref
ms.openlocfilehash: 0cda75469edaa183ed6553a431b9ad13b611db7d
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83201070"
---
# <a name="acquire-a-token-from-azure-ad-for-authorizing-requests-from-a-client-application"></a>从 Azure AD 获取用于从客户端应用程序授权请求的令牌

将 Azure Active Directory (Azure AD) 与 Azure Blob 存储和队列存储配合使用的主要优点在于不再需要在代码中存储凭据。 可以从 Microsoft 标识平台（以前称为 Azure AD）请求 OAuth 2.0 访问令牌。 Azure AD 对运行应用程序的安全主体（用户、组或服务主体）进行身份验证。 如果身份验证成功，Azure AD 会将访问令牌返回应用程序，应用程序可随之使用访问令牌对 Azure Blob 存储或队列存储请求授权。

本文介绍如何配置本机应用程序或 Web 应用程序，以便在 Microsoft 标识平台 2.0 中进行身份验证。 代码示例使用 .NET，但其他语言使用类似的方法。 有关 Microsoft 标识平台 2.0 的详细信息，请参阅 [Microsoft 标识平台 (v2.0) 概述](../../active-directory/develop/v2-overview.md)。

有关 OAuth 2.0 代码授权流的概述，请参阅[使用 OAuth 2.0 代码授权流来授权访问 Azure Active Directory Web 应用程序](../../active-directory/develop/v2-oauth2-auth-code-flow.md)。

## <a name="assign-a-role-to-an-azure-ad-security-principal"></a>将角色分配给 Azure AD 安全主体

若要从 Azure 存储应用程序对安全主体进行身份验证，请先为该安全主体配置基于角色的访问控制 (RBAC) 设置。 Azure 存储定义包含容器和队列权限的内置 RBAC 角色。 如果将 RBAC 角色分配给安全主体，该安全主体会获得该资源的访问权限。 有关详细信息，请参阅[使用 RBAC 管理对 Azure Blob 和队列数据的访问权限](storage-auth-aad-rbac.md)。

## <a name="register-your-application-with-an-azure-ad-tenant"></a>将应用程序注册到 Azure AD 租户

使用 Azure AD 授予存储资源访问权限的第一步是，通过 [Azure 门户](https://portal.azure.com)在 Azure AD 租户中注册客户端应用程序。 注册客户端应用程序时，需要向 Azure AD 提供关于应用程序的信息。 Azure AD 随后会提供客户端 ID（也称为*应用程序 ID*）。在运行时，可以使用该 ID 将应用程序与 Azure AD 关联。 若要详细了解客户端 ID，请参阅 [Azure Active Directory 中的应用程序对象和服务主体对象](../../active-directory/develop/app-objects-and-service-principals.md)。

若要注册 Azure 存储应用程序，请遵循以下文章中所述的步骤：[快速入门：将应用程序注册到 Microsoft 标识平台](../../active-directory/develop/quickstart-configure-app-access-web-apis.md)。 下图显示了用于注册 Web 应用程序的常用设置：

![显示如何向 Azure AD 注册存储应用程序的屏幕截图](./media/storage-auth-aad-app/app-registration.png)

> [!NOTE]
> 如果将应用程序注册为本机应用程序，可以为**重定向 URI** 指定任何有效的 URI。 对于本机应用程序，此值不一定要是实际的 URL。 对于 Web 应用程序，重定向 URI 必须是有效的 URI，因为它指定了要向哪个 URL 提供令牌。

注册应用程序后，可在“设置”下看到应用程序 ID（或客户端 ID）  ：

![显示客户端 ID 的屏幕截图](./media/storage-auth-aad-app/app-registration-client-id.png)

有关向 Azure AD 注册应用程序的详细信息，请参阅[将应用程序与 Azure Active Directory](../../active-directory/develop/quickstart-v2-register-an-app.md) 集成。

## <a name="grant-your-registered-app-permissions-to-azure-storage"></a>向 Azure 存储授予注册应用权限

接下来，授予应用程序权限以调用 Azure 存储 API。 借助此步骤，应用程序可授权使用 Azure AD 请求 Azure 存储。

1. 在已注册的应用程序的“概述”页上，选择“查看 API 权限”。  
1. 在“API 权限”部分中，依次选择“添加权限”、“Microsoft API”。   
1. 从结果列表中选择“Azure 存储”  以显示“请求 API 权限”  窗格。
1. 在“应用程序需要哪种类型的权限?”下，注意可用的权限类型是“委托的权限”。   默认已自动选择此选项。
1. 在“请求 API 权限”窗格的“选择权限”部分，选中“user_impersonation”旁边的复选框，然后单击“添加权限”。    

    ![显示存储权限的屏幕截图](media/storage-auth-aad-app/registered-app-permissions-1.png)

现在，“API 权限”窗格会显示已注册的 Azure AD 应用程序有权访问 Microsoft Graph 和 Azure 存储。  首次向 Azure AD 注册应用时，系统会自动授予对 Microsoft Graph 的权限。

![显示注册应用权限的屏幕截图](media/storage-auth-aad-app/registered-app-permissions-2.png)

## <a name="create-a-client-secret"></a>创建客户端机密

请求令牌时，应用程序需要使用客户端机密来证明其身份。 若要添加客户端机密，请执行以下步骤：

1. 在 Azure 门户中导航到你的应用注册。
1. 选择“证书和机密”设置。 
1. 在“客户端机密”下，单击“新建客户端机密”以创建新的机密。  
1. 提供机密说明，并选择所需的过期时间间隔。
1. 请马上将新机密的值复制到安全位置。 完整的值只会显示一次。

    ![显示客户端机密的屏幕截图](media/storage-auth-aad-app/client-secret.png)

## <a name="client-libraries-for-token-acquisition"></a>用于获取令牌的客户端库

注册应用程序并向其授予 Azure Blob 存储或队列存储中的数据的访问权限后，可将代码添加到应用程序，以便对安全主体进行身份验证并获取 OAuth 2.0 令牌。 若要进行身份验证并获取令牌，可以使用 [Microsoft 标识平台身份验证库](../../active-directory/develop/reference-v2-libraries.md)，或其他支持 OpenID Connect 1.0 的开源库。 然后，应用程序可以使用访问令牌来授权针对 Azure Blob 存储或队列存储发出的请求。

有关支持获取令牌的方案的列表，请参阅 [Microsoft 身份验证库内容](/azure/active-directory/develop/msal-overview)的[身份验证流](/en-us/azure/active-directory/develop/msal-authentication-flows)部分。

## <a name="well-known-values-for-authentication-with-azure-ad"></a>使用 Azure AD 进行身份验证的已知值

若要使用 Azure AD 验证安全主体的身份，需要在代码中包含一些已知值。

### <a name="azure-ad-authority"></a>Azure AD 颁发机构

对于 Microsoft 公有云，基本 Azure AD 颁发机构如下，其中 *tenant-id* 是 Active Directory 租户 ID（或目录 ID）：

`https://login.microsoftonline.com/<tenant-id>/`

租户 ID 用于标识要用于身份验证的 Azure AD 租户。 它也称为目录 ID。 若要检索租户 ID，请在 Azure 门户中导航到应用注册的“概述”页，并从中复制该值。 

### <a name="azure-storage-resource-id"></a>Azure 存储资源 ID

[!INCLUDE [storage-resource-id-include](../../../includes/storage-resource-id-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>.NET 代码示例：创建块 Blob

代码示例展示如何从 Azure AD 获取访问令牌。 访问令牌用于对指定用户进行身份验证，然后授权用于创建块 blob 的请求。 若要让此示例能够正常工作，请首先遵循上述部分列出的步骤。

若要请求令牌，需要获取应用注册中的以下值：

- Azure AD 域的名称。 可从 Azure Active Directory 的“概述”页检索此值。 
- 租户（或目录）ID。 可从应用注册的“概述”页检索此值。 
- 客户端（或应用程序）ID。 可从应用注册的“概述”页检索此值。 
- 客户端重定向 URI。 可应用注册的“身份验证”设置检索此值。 
- 客户端机密的值。 可从先前复制到的位置检索此值。

### <a name="create-a-storage-account-and-container"></a>创建存储帐户和容器

若要运行代码示例，请在 Azure Active Directory 所在的同一订阅中创建一个存储帐户。 然后在该存储帐户中创建一个容器。 示例代码将在此容器中创建块 Blob。

接下来，将“存储 Blob 数据参与者”角色显式分配到用于运行示例代码的用户帐户。  若要了解如何在 Azure 门户中分配此角色的说明，请参阅[在 Azure 门户中使用 RBAC 授予对 Azure Blob 和队列数据的访问权限](storage-auth-aad-rbac-portal.md)。

> [!NOTE]
> 创建 Azure 存储帐户时，系统不会自动向你分配通过 Azure AD 访问数据的权限。 你必须为自己显式分配一个用于 Azure 存储的 RBAC 角色。 可以在订阅、资源组、存储帐户、容器或队列级别分配它。

### <a name="create-a-web-application-that-authorizes-access-to-blob-storage-with-azure-ad"></a>创建一个授权使用 Azure AD 访问 Blob 存储的 Web 应用程序

当应用程序访问 Azure 存储时，它是在代表用户进行访问，这意味着，这是在使用已登录用户的权限访问 blob 或队列资源。 若要尝试运行此代码示例，需要提供一个可以提示用户使用 Azure AD 标识登录的 Web 应用程序。 你可以创建自己的，或使用 Microsoft 提供的示例应用程序。

[GitHub](https://aka.ms/aadstorage) 上提供了一个已完成的示例 Web 应用程序，该应用程序可获取令牌，并使用该令牌在 Azure 存储中创建 Blob。 查看并运行这个已完成的示例可能有助于理解代码示例。 有关如何运行已完成的示例的说明，请参阅标题为[查看和运行已完成的示例](#view-and-run-the-completed-sample)的部分。

#### <a name="add-references-and-using-statements"></a>添加引用和 using 语句  

在 Visual Studio 中安装 Azure 存储客户端库。 在“工具”菜单中选择“NuGet 包管理器”，然后选择“包管理器控制台”    。 在控制台窗口中键入以下命令，以安装适用于 .NET 的 Azure 存储客户端库中的所需包：

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

添加以下代码片段以创建块 Blob：

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
> 若要授权使用 OAuth 2.0 令牌执行 Blob 和队列操作，必须使用 HTTPS。

在上面的示例中，.NET 客户端库处理请求的授权以创建块 blob。 其他语言的 Azure 存储客户端库也可以自动处理请求授权。 但是，如果正在使用 REST API 通过 OAuth 标记调用 Azure 存储操作，之后将需要使用 OAuth 标记对请求进行授权。

若要使用 OAuth 访问令牌调用 Blob 和队列服务操作，请使用“持有者令牌”方案在“授权”标头中传递访问令牌，并指定服务版本 2017-11-09 或更高版本，如以下示例所示   ：

```https
GET /container/file.txt HTTP/1.1
Host: mystorageaccount.blob.core.windows.net
x-ms-version: 2017-11-09
Authorization: Bearer eyJ0eXAiOnJKV1...Xd6j
```

#### <a name="get-an-oauth-token-from-azure-ad"></a>从 Azure AD 获取 OAuth 令牌

接下来，添加代表用户从 Azure AD 请求令牌的方法。 此方法定义要授予的权限的范围。 若要详细了解权限和范围，请参阅 [Microsoft 标识平台终结点中的权限和许可](../../active-directory/develop/v2-permissions-and-consent.md)。

请使用资源 ID 来构造获取的令牌的范围。 此示例构造范围时，将资源 ID 与内置的 `user_impersonation` 范围配合使用，后者指示令牌是代表用户请求的。

请记住，可能需要为用户呈现一个界面，让用户允许代表他/她来请求令牌。 必须进行许可时，此示例会捕获 **MsalUiRequiredException** 并调用另一方法，促进许可请求：

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

许可是指用户进行应用程序授权，让应用程序代表自己来访问受保护资源的过程。 Microsoft 标识平台 2.0 支持增量许可，这意味着，安全主体最初可以请求极少量的一组权限，以后可按需添加权限。 当代码请求访问令牌时，请在 `scope` 参数中指定应用在任意给定时间所需的权限范围。 有关增量许可的详细信息，请参阅[为何要更新到 Microsoft 标识平台 (v2.0)？](../../active-directory/azuread-dev/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent)中的“增量许可和动态许可”部分。 

以下方法将会构造用于请求增量许可的身份验证属性：

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

## <a name="view-and-run-the-completed-sample"></a>查看和运行已完成的示例

若要运行示例应用程序，请先从 [GitHub](https://github.com/Azure-Samples/storage-dotnet-azure-ad-msal) 克隆或下载它。 然后根据以下部分所述更新应用程序。

### <a name="provide-values-in-the-settings-file"></a>在设置文件中提供值

接下来，使用自己的值更新 *appsettings.json* 文件，如下所示：

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

在 *HomeController.cs* 文件中，更新引用块 Blob 的 URI，以使用存储帐户和容器的名称：

```csharp
CloudBlockBlob blob = new CloudBlockBlob(
                      new Uri("https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt"),
                      storageCredentials);
```

### <a name="enable-implicit-grant-flow"></a>启用隐式授权流

若要运行该示例，可能需要为应用注册配置隐式授权流。 执行以下步骤：

1. 在 Azure 门户中导航到你的应用注册。
1. 在“管理”部分，选择“身份验证”设置。 
1. 在“隐式授权”部分中的“高级设置”下，选中相应的复选框以启用访问令牌和 ID 令牌，如下图所示：  

    ![显示如何启用隐式授权流设置的屏幕截图](media/storage-auth-aad-app/enable-implicit-grant-flow.png)

### <a name="update-the-port-used-by-localhost"></a>更新 localhost 使用的端口

运行该示例时，你可能会发现，需要更新应用注册中指定重定向 URI，以使用在运行时分配的 *localhost* 端口。 若要更新重定向 URI 以使用分配的端口，请执行以下步骤：

1. 在 Azure 门户中导航到你的应用注册。
1. 在“管理”部分，选择“身份验证”设置。 
1. 在“重定向 URI”下编辑端口，使之与示例应用程序使用的端口相匹配，如下图所示： 

    ![显示应用注册的重定向 URI 的屏幕截图](media/storage-auth-aad-app/redirect-uri.png)

## <a name="next-steps"></a>后续步骤

- 若要详细了解 Microsoft 标识平台，请参阅 [Microsoft 标识平台](https://docs.microsoft.com/azure/active-directory/develop/)。
- 若要详细了解 Azure 存储中的 RBAC 角色，请参阅[使用 RBAC 管理存储数据的访问权限](storage-auth-aad-rbac.md)。
- 若要了解如何将 Azure 资源的托管标识与 Azure 存储一起使用，请参阅[使用 Azure 资源的 Azure Active Directory 和托管标识验证对 Blob 和队列的访问权限](storage-auth-aad-msi.md)。
