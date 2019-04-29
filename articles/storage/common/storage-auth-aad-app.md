---
title: 进行身份验证与 Azure Active Directory 访问 blob 和队列数据从你的应用程序 |Microsoft Docs
description: 使用 Azure Active Directory 进行身份验证从应用程序内，然后授权请求 blob 和队列。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: c4959d6abd1b33c81f731ad0479bd59d271c7797
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61483914"
---
# <a name="authenticate-with-azure-active-directory-from-an-application-for-access-to-blobs-and-queues"></a>从进行身份验证与 Azure Active Directory 应用程序可访问 blob 和队列

搭配使用 Azure Active Directory (Azure AD) 和 Azure 存储的主要优点在于不再需要在代码中存储凭据。 相反，可从 Azure AD 请求 OAuth 2.0 访问令牌。 Azure AD 处理运行应用程序的安全主体（用户、组或服务主体）的身份验证。 如果身份验证成功，Azure AD 会将访问令牌返回应用程序，应用程序可随之使用访问令牌对 Azure 存储请求授权。

本文介绍如何配置应用程序使用 Azure AD 进行身份验证。 代码示例使用 .NET，但其他语言使用类似的方法。

进行安全主体身份验证从 Azure 存储应用程序之前，配置基于角色的访问控制 (RBAC) 设置为该安全主体。 Azure 存储定义包含容器和队列权限的 RBAC 角色。 如果将 RBAC 角色分配给安全主体，该安全主体会获得该资源的访问权限。 有关详细信息，请参阅[存储的数据使用 RBAC 管理访问权限](storage-auth-aad-rbac.md)。

有关 OAuth 2.0 代码授权流的概述，请参阅[使用 OAuth 2.0 代码授权流来授权访问 Azure Active Directory Web 应用程序](../../active-directory/develop/v1-protocols-oauth-code.md)。

要使用 OAuth 令牌授权 blob 和队列操作，必须使用 HTTPS。

## <a name="assign-an-rbac-role-to-an-azure-ad-security-principal"></a>将 RBAC 角色分配给 Azure AD 安全主体

若要从 Azure 存储应用程序对安全主体进行身份验证，请先为该安全主体配置基于角色的访问控制 (RBAC) 设置。 Azure 存储定义包含容器和队列权限的 RBAC 角色。 如果将 RBAC 角色分配给安全主体，该安全主体会获得该资源的访问权限。 有关详细信息，请参阅[到 Azure Blob 和队列数据，使用 RBAC 管理访问权限](storage-auth-aad-rbac.md)。

## <a name="register-your-application-with-an-azure-ad-tenant"></a>将应用程序注册到 Azure AD 租户

使用 Azure AD 授予存储资源访问权限的第一步是，在 Azure AD 租户中注册客户端应用程序。 通过注册应用程序，可以从代码中调用 Azure [Active Directory 身份验证库](../../active-directory/active-directory-authentication-libraries.md) (ADAL)。 ADAL 提供了一个 API，用于从应用程序中使用 Azure AD 进行身份验证。 通过注册应用程序，还可以使用访问令牌来授权从该应用程序对 Azure 存储 API 进行调用。

注册应用程序时，需要向 Azure AD 提供关于应用程序的信息。 Azure AD 随后会提供客户端 ID（也称为*应用程序 ID*）。在运行时，可以使用该 ID 将应用程序与 Azure AD 关联。 若要详细了解客户端 ID，请参阅 [Azure Active Directory 中的应用程序对象和服务主体对象](../../active-directory/develop/app-objects-and-service-principals.md)。

若要注册 Azure 存储应用程序，请遵循[将应用程序与 Azure Active Directory 集成](../../active-directory/active-directory-integrating-applications.md)的[添加应用程序](../../active-directory/develop/quickstart-v1-add-azure-ad-app.md)部分中的步骤。 如果将应用程序注册为本机应用程序，可以为**重定向 URI** 指定任何有效的 URI。 该值不需要是真实的终结点。

![显示如何使用 Azure AD 注册应用程序的屏幕截图](./media/storage-auth-aad-app/app-registration.png)

注册应用程序后，可在“设置”下看到应用程序 ID（或客户端 ID）：

![显示客户端 ID 的屏幕截图](./media/storage-auth-aad-app/app-registration-client-id.png)

有关向 Azure AD 注册应用程序的详细信息，请参阅[将应用程序与 Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md) 集成。 

## <a name="grant-your-registered-app-permissions-to-azure-storage"></a>向 Azure 存储授予注册应用权限

接下来，需要授予应用程序权限以调用 Azure 存储 API。 借助此步骤，应用程序可授权使用 Azure AD 调用 Azure 存储。

1. 在 Azure 门户的左侧导航窗格中，选择“所有服务”，然后搜索“应用注册”。
2. 搜索在上一步中创建的注册应用程序的名称。
3. 选择已注册的应用，然后单击“设置”。 在“API 访问”部分中，选择“所需的权限”。
4. 在“所需的权限”边栏选项卡中，单击“添加”按钮。
5. 在“选择 API”下，搜索“Azure 存储”，然后从结果列表中选择“Azure 存储”。

    ![存储的屏幕截图显示权限](media/storage-auth-aad-app/registered-app-permissions-1.png)

6. 在“选择权限”下，勾选“访问 Azure 存储”旁边的复选框，然后单击“选择”。
7. 单击“完成”。

“所需权限”窗口现在显示 Azure AD 应用程序拥有 Azure Active Directory 和 Azure 存储的访问权限。 首次向 Azure AD 注册应用时，系统会自动向 Azure AD 授予权限。

![显示的屏幕截图注册应用权限](media/storage-auth-aad-app/registered-app-permissions-2.png)

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

租户 ID 用于标识要用于身份验证的 Azure AD 租户。 若要检索租户 ID，请遵循**获取 Azure Active Directory 的租户 ID** 中概述的步骤。

#### <a name="storage-resource-id"></a>存储资源 ID

使用 Azure 存储资源 ID 获取用于对 Azure 存储请求进行身份验证的的令牌：

`https://storage.azure.com/`

### <a name="get-the-tenant-id-for-your-azure-active-directory"></a>获取 Azure Active Directory 的租户 ID

若要获取租户 ID，请按照以下步骤操作：

1. 在 Azure 门户中，选择 Active Directory。
2. 单击“属性”。
3. 复制为“目录 ID”提供的 GUID 值。 该值也称为租户 ID。

![显示如何将复制的租户 ID 的屏幕截图](./media/storage-auth-aad-app/aad-tenant-id.png)

### <a name="add-references-and-using-statements"></a>添加引用和 using 语句  

从 Visual Studio 中，安装 Azure 存储客户端库。 在“工具”菜单中，依次选择“NuGet 包管理器”和“包管理器控制台”。 在控制台中键入以下命令，安装最新版本的 .NET 客户端库：

```
Install-Package WindowsAzure.Storage
```

并安装最新版本的 ADAL：

```
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
```

接下来，向代码添加以下 using 语句：

```dotnet
using System.Globalization;
using Microsoft.IdentityModel.Clients.ActiveDirectory; //ADAL client library for getting the access token
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
```

### <a name="get-an-oauth-token-from-azure-ad"></a>从 Azure AD 获取 OAuth 令牌

接下来，添加从 Azure AD 请求令牌的方法。 若要请求令牌，请调用 [AuthenticationContext.AcquireTokenAsync](https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.clients.activedirectory.authenticationcontext.acquiretokenasync) 方法。 确保之前执行的步骤中包含以下值：

- 租户（目录）ID
- 客户端（应用程序）ID
- 客户端重定向 URI

```dotnet
static string GetUserOAuthToken()
{
    const string ResourceId = "https://storage.azure.com/";
    const string AuthInstance = "https://login.microsoftonline.com/{0}/";
    const string TenantId = "<tenant-id>"; // Tenant or directory ID

    // Construct the authority string from the Azure AD OAuth endpoint and the tenant ID. 
    string authority = string.Format(CultureInfo.InvariantCulture, AuthInstance, TenantId);
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

最后，使用访问令牌来创建新的存储凭据，然后将这些凭据用于创建 blob。 请注意，若要授权使用 OAuth 令牌的 blob 和队列操作，则必须使用 HTTPS。:

```dotnet
// Get the access token.
string accessToken = GetUserOAuthToken();

// Use the access token to create the storage credentials.
TokenCredential tokenCredential = new TokenCredential(accessToken);
StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

// Create a block blob using those credentials
CloudBlockBlob blob = new CloudBlockBlob(new Uri("https://storagesamples.blob.core.windows.net/sample-container/Blob1.txt"), storageCredentials);

blob.UploadTextAsync("Blob created by Azure AD authenticated user.");
```

> [!NOTE]
> Azure AD 与 Azure 存储集成要求用户使用 HTTPS 进行 Azure 存储操作。

在上面的示例中，.NET 客户端库处理请求的授权以创建块 blob。 其他存储客户端库也为你处理请求的授权。 但是，如果正在使用 REST API 通过 OAuth 标记调用 Azure 存储操作，之后将需要使用 OAuth 标记对请求进行授权。   

若要使用 OAuth 访问令牌调用 Blob 和队列服务操作，请使用“持有者令牌”方案在“授权”标头中传递访问令牌，并指定服务版本 2017-11-09 或更高版本，如以下示例所示：

```
GET /container/file.txt HTTP/1.1
Host: mystorageaccount.blob.core.windows.net
x-ms-version: 2017-11-09
Authorization: Bearer eyJ0eXAiOnJKV1...Xd6j
```

授权从 REST 的 Azure 存储操作的详细信息，请参阅[使用 Azure Active Directory 进行身份验证](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory)。

## <a name="next-steps"></a>后续步骤

- 若要详细了解 Azure 存储中的 RBAC 角色，请参阅[使用 RBAC 管理存储数据的访问权限](storage-auth-aad-rbac.md)。
- 若要了解如何使用适用于使用 Azure 存储的 Azure 资源管理的标识，请参阅[验证对 blob 和队列与 Azure Active Directory 和管理的标识访问 Azure 资源的](storage-auth-aad-msi.md)。
- 若要了解如何使用 Azure AD 凭据运行 Azure CLI 和 PowerShell 命令，请参阅[运行 Azure CLI 或 PowerShell 命令使用 Azure AD 凭据来访问 blob 或队列数据](storage-auth-aad-script.md)。
