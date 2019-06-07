---
title: 通过进行身份验证与 Azure Active Directory 访问 blob 和队列数据客户端应用程序
description: 使用 Azure Active Directory 进行身份验证从客户端应用程序、 获取 OAuth 2.0 令牌和授权对 Azure Blob 存储和队列存储的请求。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/05/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: e45fe20e93d81c1cfd1f868b40f76743558758bb
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2019
ms.locfileid: "66754927"
---
# <a name="authenticate-with-azure-active-directory-from-an-application-for-access-to-blobs-and-queues"></a>从进行身份验证与 Azure Active Directory 应用程序可访问 blob 和队列

使用 Azure Blob 存储或队列存储使用 Azure Active Directory (Azure AD) 的一个主要优点是你的凭据的不再需要存储在你的代码中。 相反，您可以从 Microsoft 标识平台 (以前称为 Azure AD) 请求的 OAuth 2.0 访问令牌。 Azure AD 进行身份验证安全主体 （用户、 组或服务主体） 运行应用程序。 如果验证成功，Azure AD 将向该应用程序，返回访问令牌和应用程序然后可以使用访问令牌以授权对 Azure Blob 存储或队列存储的请求。

本文介绍如何配置与 Azure AD 本机应用程序或 web 应用程序进行身份验证。 代码示例使用 .NET，但其他语言使用类似的方法。

有关 OAuth 2.0 代码授权流的概述，请参阅[使用 OAuth 2.0 代码授权流来授权访问 Azure Active Directory Web 应用程序](../../active-directory/develop/v2-oauth2-auth-code-flow.md)。

## <a name="assign-an-rbac-role-to-an-azure-ad-security-principal"></a>将 RBAC 角色分配给 Azure AD 安全主体

若要从 Azure 存储应用程序对安全主体进行身份验证，请先为该安全主体配置基于角色的访问控制 (RBAC) 设置。 Azure 存储定义包含容器和队列的权限的内置 RBAC 角色。 如果将 RBAC 角色分配给安全主体，该安全主体会获得该资源的访问权限。 有关详细信息，请参阅[到 Azure Blob 和队列数据，使用 RBAC 管理访问权限](storage-auth-aad-rbac.md)。

## <a name="register-your-application-with-an-azure-ad-tenant"></a>将应用程序注册到 Azure AD 租户

使用 Azure AD 来授予对存储资源的访问权限的第一步向 Azure AD 租户中注册客户端应用程序[Azure 门户](https://portal.azure.com)。 注册客户端应用程序时，您将提供到 Azure AD 应用程序的信息。 Azure AD 随后会提供客户端 ID（也称为*应用程序 ID*）。在运行时，可以使用该 ID 将应用程序与 Azure AD 关联。 若要详细了解客户端 ID，请参阅 [Azure Active Directory 中的应用程序对象和服务主体对象](../../active-directory/develop/app-objects-and-service-principals.md)。

若要注册你的 Azure 存储应用程序，请按照中所示的步骤[快速入门：注册一个应用程序的 Microsoft 标识平台](../../active-directory/develop/quickstart-configure-app-access-web-apis.md)。 下图显示用于注册的 web 应用程序常用的设置：

![显示如何使用 Azure AD 注册应用程序的屏幕截图](./media/storage-auth-aad-app/app-registration.png)

> [!NOTE]
> 如果将应用程序注册为本机应用程序，可以为**重定向 URI** 指定任何有效的 URI。 对于本机应用程序，此值不必是实际的 URL。 对于 web 应用程序，重定向 URI 必须是有效的 URI，因为它指定提供标记的 URL。

注册应用程序后，可在“设置”下看到应用程序 ID（或客户端 ID）  ：

![显示客户端 ID 的屏幕截图](./media/storage-auth-aad-app/app-registration-client-id.png)

有关向 Azure AD 注册应用程序的详细信息，请参阅[将应用程序与 Azure Active Directory](../../active-directory/develop/quickstart-v2-register-an-app.md) 集成。

## <a name="grant-your-registered-app-permissions-to-azure-storage"></a>向 Azure 存储授予注册应用权限

接下来，授予应用程序的权限来调用 Azure 存储 Api。 此步骤中使应用程序与 Azure AD 的 Azure 存储的请求进行授权。

1. 上**概述**已注册应用程序页，选择**查看 API 权限**。
1. 在中**API 的权限**部分中，选择**添加权限**，然后选择**我的组织使用的 Api**。
1. 下**我的组织使用的 Api**部分中，搜索"Azure 存储"，并选择**Azure 存储**从列表中要显示的结果**请求 API 权限**窗格。

    ![存储的屏幕截图显示权限](media/storage-auth-aad-app/registered-app-permissions-1.png)

1. 下**应用程序需要哪种类型的权限？** ，请注意，可用权限类型为**委派权限**。 默认情况下，为你选择此选项。
1. 在**选择权限**一部分**请求 API 权限**窗格中，选中的复选框旁边**user_impersonation**，然后单击**添加权限**。
1. **API 权限**窗格现在显示 Azure AD 应用程序有权访问 Microsoft Graph 和 Azure 存储。 自动授予权限向 Microsoft Graph 时首先向 Azure AD 注册您的应用程序。

    ![显示的屏幕截图注册应用权限](media/storage-auth-aad-app/registered-app-permissions-2.png)

## <a name="libraries-for-token-acquisition"></a>库来获取令牌

注册你的应用程序并授予它访问 Azure Blob 存储或队列存储中的数据的权限后，您可以将代码添加到你的应用程序安全主体进行身份验证并获取 OAuth 2.0 令牌。 若要进行身份验证和获取令牌，可以使用任一[Microsoft 标识平台身份验证库](../../active-directory/develop/reference-v2-libraries.md)或其他支持 OpenID Connect 1.0 的开放源代码库。 然后，应用程序可以使用访问令牌以授权对 Azure Blob 存储或队列存储的请求。

为其支持获取令牌方案的列表，请参阅[方案](https://aka.ms/msal-net-scenarios)一部分[适用于.NET 的 Microsoft 身份验证库 (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) GitHub 存储库。

## <a name="net-code-example-create-a-block-blob"></a>.NET 代码示例：创建块 Blob

代码示例展示如何从 Azure AD 获取访问令牌。 访问令牌用于对指定用户进行身份验证，然后授权用于创建块 blob 的请求。 若要让此示例能够正常工作，请首先遵循上述部分列出的步骤。

> [!NOTE]
> 作为 Azure 存储帐户的所有者，系统不会自动向你分配数据访问权限。 你必须为自己显式分配一个用于 Azure 存储的 RBAC 角色。 可以在订阅、资源组、存储帐户、容器或队列级别分配它。
>
> 例如，若要在身为所有者的存储帐户上以自己的用户身份运行示例代码，则必须为自己分配用于 Blob 数据参与者的 RBAC 角色。 否则，用于创建 blob 的调用将失败并显示 HTTP 状态代码 403（禁止）。 有关详细信息，请参阅[存储的数据使用 RBAC 管理访问权限](storage-auth-aad-rbac.md)。

### <a name="well-known-values-for-authentication-with-azure-ad"></a>使用 Azure AD 进行身份验证的已知值

若要使用 Azure AD 验证安全主体的身份，需要在代码中包含一些已知值。

#### <a name="azure-ad-authority"></a>Azure AD 颁发机构

对于 Microsoft 公有云，基本 Azure AD 颁发机构如下，其中 *tenant-id* 是 Active Directory 租户 ID（或目录 ID）：

`https://login.microsoftonline.com/<tenant-id>/`

租户 ID 用于标识要用于身份验证的 Azure AD 租户。 若要检索租户 ID，请执行在一节中所述的步骤**获取 Azure Active Directory 租户 ID**。

#### <a name="storage-resource-id"></a>存储资源 ID

使用 Azure 存储资源 ID 来获取令牌以授权对 Azure 存储请求：

`https://storage.azure.com/`

### <a name="get-the-tenant-id-for-your-azure-active-directory"></a>获取 Azure Active Directory 的租户 ID

若要获取租户 ID，请按照以下步骤操作：

1. 在 Azure 门户中，选择 Active Directory。
2. 单击“属性”。 
3. 复制为“目录 ID”提供的 GUID 值。  该值也称为租户 ID。

![显示如何将复制的租户 ID 的屏幕截图](./media/storage-auth-aad-app/aad-tenant-id.png)

## <a name="set-up-a-basic-web-app-to-authenticate-to-azure-ad"></a>设置基本的 web 应用，Azure AD 进行身份验证

当你的应用程序访问 Azure 存储时，它 does 等代表该用户。 若要尝试此代码示例，你需要一个 web 应用程序会提示用户可以使用 Azure AD 标识登录。 你可以下载这个[的代码示例](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)若要测试的基本 web 应用程序使用 Azure AD 帐户进行身份验证。

### <a name="completed-sample"></a>已完成的示例

可以从下载本文中所示的示例代码的完整工作版本[GitHub](http://aka.ms/aadstorage)。 检查并运行完整的示例可能有助于你了解的代码示例。

### <a name="add-references-and-using-statements"></a>添加引用和 using 语句  

从 Visual Studio 中，安装 Azure 存储客户端库。 在“工具”菜单中，依次选择“NuGet 包管理器”和“包管理器控制台”    。 控制台窗口中，以安装用于.NET 的 Azure 存储客户端库中的所需的包中键入以下命令：

```console
Install-Package Microsoft.Azure.Storage.Blob
Install-Package Microsoft.Azure.Storage.Common
```

接下来，将以下代码添加到 HomeController.cs 文件 using 语句：

```csharp
using System;
using Microsoft.Identity.Client; //MSAL library for getting the access token
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
```

### <a name="create-a-block-blob"></a>创建块 Blob

添加下面的代码段，若要创建块 blob:

```csharp
private static async Task<string> CreateBlob(string accessToken)
{
    // Create a blob on behalf of the user
    TokenCredential tokenCredential = new TokenCredential(accessToken);
    StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

    // Replace the URL below with your storage account URL
    CloudBlockBlob blob =
        new CloudBlockBlob(
            new Uri("https://<storage-account>.blob.core.windows.net/sample-container/Blob1.txt"),
            storageCredentials);
    await blob.UploadTextAsync("Blob created by Azure AD authenticated user.");
    return "Blob successfully created";
}
```

> [!NOTE]
> 若要授权使用 OAuth 2.0 令牌的 blob 和队列操作，必须使用 HTTPS。

在上面的示例中，.NET 客户端库处理请求的授权以创建块 blob。 对于其他语言的 azure 存储客户端库还处理为您请求的授权。 但是，如果正在使用 REST API 通过 OAuth 标记调用 Azure 存储操作，之后将需要使用 OAuth 标记对请求进行授权。

若要使用 OAuth 访问令牌调用 Blob 和队列服务操作，请使用“持有者令牌”方案在“授权”标头中传递访问令牌，并指定服务版本 2017-11-09 或更高版本，如以下示例所示   ：

```https
GET /container/file.txt HTTP/1.1
Host: mystorageaccount.blob.core.windows.net
x-ms-version: 2017-11-09
Authorization: Bearer eyJ0eXAiOnJKV1...Xd6j
```

### <a name="get-an-oauth-token-from-azure-ad"></a>从 Azure AD 获取 OAuth 令牌

接下来，添加从 Azure AD 请求令牌的方法。 您请求的令牌将代表用户，并且我们将使用 GetTokenOnBehalfOfUser 方法。

若要请求令牌，将需要你的应用注册中的以下值

- 租户 （或目录） 的 ID
- 客户端 （或应用程序 ID)
- 客户端重定向 URI

请记住，如果只是已登录，并且您请求的令牌`storage.azure.com`资源，你将需要为用户提供一个用户界面，用户可以同意代表其执行此操作。 为了便于您需要捕获`MsalUiRequiredException`并将该功能来请求用户同意的情况下，添加下面的示例中所示：

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
        AuthenticationProperties properties = BuildAuthenticationPropertiesForIncrementalConsent(scopes, ex);
        return Challenge(properties);
    }
}
```

许可是指用户授权应用程序代表他们访问受保护资源的过程。 Microsoft 标识平台 2.0 支持增量许可，这意味着安全主体可以最初请求最小权限集，并根据需要随着时间的推移添加权限。 当你的代码请求访问令牌时，指定的应用在任何给定时间按中所需的权限作用域`scope`参数。 以下方法构造请求增量许可的身份验证属性：

```csharp
private AuthenticationProperties BuildAuthenticationPropertiesForIncrementalConsent(string[] scopes, MsalUiRequiredException ex)
{
    AuthenticationProperties properties = new AuthenticationProperties();

    // Set the scopes, including the scopes that ADAL.NET / MSAL.NET need for the Token cache.
    string[] additionalBuildInScopes = new string[] { "openid", "offline_access", "profile" };
    properties.SetParameter<ICollection<string>>(OpenIdConnectParameterNames.Scope, scopes.Union(additionalBuildInScopes).ToList());

    // Attempt to set the login_hint so that the logged-in user is not presented with an account selection dialog.
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

## <a name="next-steps"></a>后续步骤

- 若要了解有关 Microsoft 标识平台的详细信息，请参阅[Microsoft 标识平台](https://docs.microsoft.com/azure/active-directory/develop/)。
- 若要详细了解 Azure 存储中的 RBAC 角色，请参阅[使用 RBAC 管理存储数据的访问权限](storage-auth-aad-rbac.md)。
- 若要了解如何使用适用于使用 Azure 存储的 Azure 资源管理的标识，请参阅[验证对 blob 和队列与 Azure Active Directory 和管理的标识访问 Azure 资源的](storage-auth-aad-msi.md)。
