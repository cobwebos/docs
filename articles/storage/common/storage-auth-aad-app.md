---
title: 从存储应用程序使用 Azure AD 验证身份（预览版）| Microsoft Docs
description: 从 Azure 存储应用程序使用 Azure AD 验证身份（预览版）。
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 05/18/2018
ms.author: tamram
ms.openlocfilehash: 1bf4a8bba3b93c16f67d46f65292709ef2a1bba2
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34659220"
---
# <a name="authenticate-with-azure-ad-from-an-azure-storage-application-preview"></a>从 Azure 存储应用程序使用 Azure AD 验证身份（预览版）

搭配使用 Azure Active Directory (Azure AD) 和 Azure 存储的主要优点在于不再需要在代码中存储凭据。 相反，可从 Azure AD 请求 OAuth 2.0 访问令牌。 Azure AD 处理运行应用程序的安全主体（用户、组或服务主体）的身份验证。 如果身份验证成功，Azure AD 会将访问令牌返回应用程序，应用程序可随之使用访问令牌对 Azure 存储请求授权。

本文介绍如何配置应用程序使用 Azure AD 进行身份验证。 代码示例使用 .NET，但其他语言使用类似的方法。

请先为安全主体配置基于角色的访问控制 (RBAC) 设置，然后才能从 Azure 存储应用程序对该安全主体进行身份验证。 Azure 存储定义包含容器和队列权限的 RBAC 角色。 如果将 RBAC 角色分配给安全主体，该安全主体会获得该资源的访问权限。 有关详细信息，请参阅[使用 RBAC 管理存储数据访问权限（预览版）](storage-auth-aad-rbac.md)。

有关 OAuth 2.0 代码授权流的概述，请参阅[使用 OAuth 2.0 代码授权流来授权访问 Azure Active Directory Web 应用程序](../../active-directory/develop/active-directory-protocols-oauth-code.md)。

> [!IMPORTANT]
> 此预览版仅用于非生产用途。 适用于 Azure 存储的 Azure AD 集成正式发布后，生产服务级别协议 (SLA) 方可使用。 如果你的方案尚不支持 Azure AD 集成，请继续使用应用程序中的共享密钥授权或 SAS 令牌。 有关该预览版的其他信息，请参阅[使用 Azure Active Directory 验证 Azure 存储访问权限（预览版）](storage-auth-aad.md)。
>
> 预览期间，RBAC 角色分配可能需要长达五分钟的时间进行传播。

## <a name="register-your-application-with-an-azure-ad-tenant"></a>将应用程序注册到 Azure AD 租户

使用 Azure AD 授予存储资源访问权限的第一步是，在 Azure AD 租户中注册客户端应用程序。 通过注册应用程序，可以从代码中调用 Azure [Active Directory 身份验证库](../../active-directory/active-directory-authentication-libraries.md) (ADAL)。 ADAL 提供了一个 API，用于从应用程序中使用 Azure AD 进行身份验证。 通过注册应用程序，还可以使用访问令牌来授权从该应用程序对 Azure 存储 API 进行调用。

注册应用程序时，需要向 Azure AD 提供关于应用程序的信息。 Azure AD 随后会提供客户端 ID（也称为*应用程序 ID*）。在运行时，可以使用该 ID 将应用程序与 Azure AD 关联。 若要详细了解客户端 ID，请参阅 [Azure Active Directory 中的应用程序对象和服务主体对象](../../active-directory/develop/active-directory-application-objects.md)。

若要注册 Azure 存储应用程序，请遵循[将应用程序与 Azure Active Directory 集成](../../active-directory/active-directory-integrating-applications.md)的[添加应用程序](../../active-directory/develop/active-directory-integrating-applications.md#adding-an-application)部分中的步骤。 如果将应用程序注册为本机应用程序，可以为**重定向 URI** 指定任何有效的 URI。 该值不需要是真实的终结点。

![显示如何向 Azure AD 注册存储应用程序的屏幕截图](./media/storage-auth-aad-app/app-registration.png)

注册应用程序后，可在“设置”下看到应用程序 ID（或客户端 ID）：

![显示客户端 ID 的屏幕截图](./media/storage-auth-aad-app/app-registration-client-id.png)

有关向 Azure AD 注册应用程序的详细信息，请参阅[将应用程序与 Azure Active Directory](../../active-directory/develop/active-directory-integrating-applications.md) 集成。 

## <a name="grant-your-registered-app-permissions-to-azure-storage"></a>向 Azure 存储授予注册应用权限

接下来，需要授予应用程序权限以调用 Azure 存储 API。 借助此步骤，应用程序可授权使用 Azure AD 调用 Azure 存储。

1. 在 Azure 门户的左侧导航窗格中，选择“所有服务”，然后搜索“应用注册”。
2. 搜索在上一步中创建的注册应用程序的名称。
3. 选择已注册的应用，然后单击“设置”。 在“API 访问”部分中，选择“所需的权限”。
4. 在“所需的权限”边栏选项卡中，单击“添加”按钮。
5. 在“选择 API”下，搜索“Azure 存储”，然后从结果列表中选择“Azure 存储”。

    ![显示存储权限的屏幕截图](media/storage-auth-aad-app/registered-app-permissions-1.png)

6. 在“选择权限”下，勾选“访问 Azure 存储”旁边的复选框，然后单击“选择”。
7. 单击“Done”（完成） 。

“所需权限”窗口现在显示 Azure AD 应用程序拥有 Azure Active Directory 和 Azure 存储的访问权限。 首次向 Azure AD 注册应用时，系统会自动向 Azure AD 授予权限。

![显示注册应用权限的屏幕截图](media/storage-auth-aad-app/registered-app-permissions-2.png)

## <a name="net-code-example-create-a-block-blob"></a>.NET 代码示例：创建块 blob

代码示例展示如何从 Azure AD 获取访问令牌。 访问令牌用于对指定用户进行身份验证，然后授权用于创建块 blob 的请求。 若要让此示例能够正常工作，请首先遵循上述部分列出的步骤。

> [!NOTE]
> 作为 Azure 存储帐户的所有者，系统不会自动向你分配数据访问权限。 你必须为自己显式分配一个用于 Azure 存储的 RBAC 角色。 可以在订阅、资源组、存储帐户、容器或队列级别分配它。 
>
> 例如，若要在身为所有者的存储帐户上以自己的用户身份运行示例代码，则必须为自己分配用于 Blob 数据参与者的 RBAC 角色。 否则，用于创建 blob 的调用将失败并显示 HTTP 状态代码 403（禁止）。 有关详细信息，请参阅[使用 RBAC 管理存储数据访问权限（预览版）](storage-auth-aad-rbac.md)。

### <a name="well-known-values-for-authentication-with-azure-ad"></a>使用 Azure AD 进行身份验证的已知值

若要使用 Azure AD 验证安全主体的身份，需要在代码中包含一些已知值。

#### <a name="azure-ad-oauth-endpoint"></a>Azure AD OAuth 终结点

OAuth 2.0 的基本 Azure AD 权威终结点如下所示，其中 *tenant-id* 是 Active Directory 租户 ID（或目录 ID）：

`https://login.microsoftonline.com/<tenant-id>/oauth2/token`

租户 ID 用于标识要用于身份验证的 Azure AD 租户。 若要检索租户 ID，请遵循**获取 Azure Active Directory 的租户 ID** 中概述的步骤。

#### <a name="storage-resource-id"></a>存储资源 ID

使用 Azure 存储资源 ID 获取用于对 Azure 存储请求进行身份验证的的令牌：

`https://storage.azure.com/`

### <a name="get-the-tenant-id-for-your-azure-active-directory"></a>获取 Azure Active Directory 的租户 ID

若要获取租户 ID，请按照以下步骤操作：

1. 在 Azure 门户中，选择 Active Directory。
2. 单击“属性”。
3. 复制为“目录 ID”提供的 GUID 值。 该值也称为租户 ID。

![显示如何复制租户 ID 的屏幕截图](./media/storage-auth-aad-app/aad-tenant-id.png)

### <a name="add-references-and-using-statements"></a>添加引用和 using 语句  

在 Visual Studio 中，安装 Azure 存储客户端库的预览版本。 在“工具”菜单中，依次选择“NuGet 包管理器”和“包管理器控制台”。 在控制台中键入以下命令：

```
Install-Package https://www.nuget.org/packages/WindowsAzure.Storage/9.2.0  
```

接下来，向代码添加以下 using 语句：

```dotnet
using Microsoft.IdentityModel.Clients.ActiveDirectory; //ADAL client library for getting the access token
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
```

### <a name="get-an-oauth-token-from-azure-ad"></a>从 Azure AD 获取 OAuth 令牌

接下来，添加从 Azure AD 请求令牌的方法。 若要请求令牌，请调用 [AuthenticationContext.AcquireTokenAsync](https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.clients.activedirectory.authenticationcontext.acquiretokenasync) 方法。

```dotnet
static string GetUserOAuthToken()
{
    const string ResourceId = "https://storage.azure.com/"; // Storage resource endpoint
    const string AuthEndpoint = "https://login.microsoftonline.com/{0}/oauth2/token"; // Azure AD OAuth endpoint
    const string TenantId = "<tenant-id>"; // Tenant or directory ID

    // Construct the authority string from the Azure AD OAuth endpoint and the tenant ID. 
    string authority = string.Format(CultureInfo.InvariantCulture, AuthEndpoint, TenantId);
    AuthenticationContext authContext = new AuthenticationContext(authority);

    // Acquire an access token from Azure AD. 
    AuthenticationResult result = authContext.AcquireTokenAsync(ResourceId, 
                                                                "<client-id>", 
                                                                new Uri(@"<client-redirect-uri>"), 
                                                                new PlatformParameters(PromptBehavior.Auto)).Result;

    return result.AccessToken;
}
```

### <a name="create-the-block-blob"></a>创建块 blob

最后，使用访问令牌新建存储凭据，然后使用这些凭据创建 blob：

```dotnet
// Get the access token.
string accessToken = GetUserOAuthToken();

// Use the access token to create the storage credentials.
TokenCredential tokenCredential = new TokenCredential(accessToken);
StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

// Create a block blob using those credentials
CloudBlockBlob blob = new CloudBlockBlob(new Uri("https://storagesamples.blob.core.windows.net/sample-container/Blob1.txt"), storageCredentials);
```

> [!NOTE]
> Azure AD 与 Azure 存储的集成要求使用 HTTPS 进行 Azure 存储操作。

## <a name="next-steps"></a>后续步骤

- 若要详细了解适用于 Azure 存储的 RBAC 角色，请参阅[使用 RBAC 管理存储数据访问权限（预览版）](storage-auth-aad-rbac.md)。
- 若要了解如何配合使用托管服务标识和 Azure 存储，请参阅[使用 Azure AD 通过 Azure 托管服务标识进行身份验证（预览版）](storage-auth-aad-msi.md)。
- 若要了解如何使用 Azure AD 标识登录 Azure CLI 和 PowerShell，请参阅[使用 Azure AD 标识通过 CLI 或 PowerShell 访问 Azure 存储（预览版）](storage-auth-aad-script.md)。
- 有关适用于 Azure Blob 和队列的 Azure AD 集成的其他信息，请参阅 Azure 存储团队博客文章[宣布推出适用于 Azure 存储的 Azure AD 身份验证预览版](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/)。



