---
title: 使用 Azure Active Directory 对 Azure Batch 服务进行身份验证
description: Batch 支持 Azure AD 在 Batch 服务中进行身份验证。 了解如何通过两种方式之一进行身份验证。
ms.topic: article
ms.date: 01/28/2020
ms.custom: has-adal-ref
ms.openlocfilehash: 3fa1aa2bb7389200fe5e5a80598686143344d636
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82608466"
---
# <a name="authenticate-batch-service-solutions-with-active-directory"></a>使用 Active Directory 对 Batch 服务解决方案进行身份验证

Azure Batch 支持使用 [Azure Active Directory][aad_about] (Azure AD) 进行身份验证。 Azure AD 是 Microsoft 提供的基于多租户云的目录和标识管理服务。 Azure 本身使用 Azure AD 对其客户、服务管理员和组织用户进行身份验证。

在使用 Azure Batch 对 Azure AD 进行身份验证时，可以通过以下两种方式之一进行身份验证：

- 使用集成身份验证  对与应用程序交互的用户进行身份验证。 使用集成身份验证的应用程序收集用户的凭据，并使用这些凭据对 Batch 资源访问进行身份验证。
- 使用服务主体  对无人参与的应用程序进行身份验证。 服务主体定义应用程序的策略和权限，使其能够在运行时访问资源时代表应用程序。

有关 Azure AD 的详细信息，请阅读 [Azure Active Directory 文档](https://docs.microsoft.com/azure/active-directory/)。

## <a name="endpoints-for-authentication"></a>身份验证终结点

若要使用 Azure AD 对 Batch 应用程序进行验证，需要在代码中包含一些已知终结点。

### <a name="azure-ad-endpoint"></a>Azure AD 终结点

基础 Azure AD 颁发机构终结点是：

`https://login.microsoftonline.com/`

要使用 Azure AD 进行验证，请将此终结点与租户 ID（即目录 ID）一起使用。 租户 ID 用于标识要用于身份验证的 Azure AD 租户。 若要检索租户 ID，请按照[获取 Azure Active Directory 的租户 ID](#get-the-tenant-id-for-your-active-directory)中概述的步骤进行操作：

`https://login.microsoftonline.com/<tenant-id>`

> [!NOTE]
> 使用服务主体进行验证时，需要特定于租户的终结点。
>
> 使用集成身份验证进行验证时，虽然特定于租户的终结点为可选，但仍推荐。 然而，还可以使用 Azure AD 常用终结点。 未提供特定租户时，该常用终结点可提供泛型凭据收集接口。 常用终结点为 `https://login.microsoftonline.com/common`。
>
>

有关 Azure AD 终结点的详细信息，请参阅 [Azure AD 的身份验证方案][aad_auth_scenarios]。

### <a name="batch-resource-endpoint"></a>Batch 资源终结点

Azure Batch 资源终结点  用于获取对 Batch 服务的请求进行身份验证的令牌：

`https://batch.core.windows.net/`

## <a name="register-your-application-with-a-tenant"></a>向租户注册应用程序

使用 Azure AD 进行验证的第一步是在 Azure AD 租户中注册应用程序。 通过注册应用程序，可以从代码中调用 Azure [Active Directory 身份验证库][aad_adal] (ADAL)。 ADAL 提供了一个 API，用于从应用程序中使用 Azure AD 进行身份验证。 无论是计划使用集成身份验证还是服务主体，都必须注册应用程序。

注册应用程序时，需要向 Azure AD 提供关于应用程序的信息。 然后，Azure AD 将提供一个应用程序 ID（也称为“客户端 ID”  ），在运行时，可以使用该 ID 将应用程序与 Azure AD 相关联。 若要详细信息应用程序 ID，请参阅 [Azure Active Directory 中的应用程序对象和服务主体对象](../active-directory/develop/app-objects-and-service-principals.md)。

要注册批处理应用程序，请遵循[将应用程序与 Azure Active Directory 集成][aad_integrate]的[添加应用程序](../active-directory/develop/quickstart-register-app.md)部分中的步骤。 如果将应用程序注册为本机应用程序，可以为重定向 URI  指定任何有效 URI。 它不需要是实际的终结点。

注册应用程序后，会看到应用程序 ID：

![将批处理应用程序注册到 Azure AD](./media/batch-aad-auth/app-registration-data-plane.png)

有关使用 Azure AD 注册应用程序的详细信息，请参阅 [Azure AD 的身份验证方案](../active-directory/develop/authentication-scenarios.md)。

## <a name="get-the-tenant-id-for-your-active-directory"></a>获取 Active Directory 的租户 ID

租户 ID 用于标识向应用程序提供身份验证服务的 Azure AD 租户。 若要获取租户 ID，请按照以下步骤操作：

1. 在 Azure 门户中，选择 Active Directory。
1. 选择“属性”  。
1. 复制为“目录 ID”提供的 GUID 值。  该值也称为租户 ID。

![复制目录 ID](./media/batch-aad-auth/aad-directory-id.png)

## <a name="use-integrated-authentication"></a>使用集成身份验证

若要使用集成身份验证进行验证，需要授予应用程序连接到 Batch 服务 API 的权限。 此步骤使应用程序可使用 Azure AD 对 Batch 服务 API 的调用进行验证。

注册了应用程序后，请按照 Azure 门户中的以下步骤来向其授予对 Batch 服务的访问权限：

1. 在 Azure 门户的左侧导航窗格中，选择“所有服务”  。 选择“应用注册”  。
1. 在应用注册列表中搜索应用程序名称：

    ![搜索应用程序名称](./media/batch-aad-auth/search-app-registration.png)

1. 请选择应用程序，然后选择“API 权限”  。
1. 在“API 权限”  部分，选择“添加权限”  。
1. 在“选择 API”  中，搜索 Batch API。 搜索每一条字符串，直到找到此 API：
    1. **Microsoft Azure Batch**
    1. ddbf3205-c6bd-46ae-8127-60eb93363864 是此 Batch API 的 ID  。
1. 找到此 Batch API 后，将其选中，然后选择“选择”。 
1. 在“选择权限”中，选中“访问 Azure Batch 服务”旁边的复选框，然后选择“添加权限”。   

现在，“API 权限”部分会显示你的 Azure AD 应用程序有权访问 Microsoft Graph 和 Batch 服务 API。  首次向 Azure AD 注册应用时，系统会自动授予对 Microsoft Graph 的权限。

![授予 API 权限](./media/batch-aad-auth/required-permissions-data-plane.png)

## <a name="use-a-service-principal"></a>使用服务主体

若要对以无人参与方式运行的应用程序进行验证，可以使用服务主体。 注册应用程序后，请按照 Azure 门户中的下列步骤配置服务主体：

1. 为应用程序请求一个机密。
1. 将基于角色的访问控制 (RBAC) 分配到应用程序。

### <a name="request-a-secret-for-your-application"></a>为应用程序请求一个机密

使用服务主体对应用程序进行验证时，它将同时向 Azure AD 发送应用程序 ID 和机密。 需要创建并复制要在代码中使用的密钥。

在 Azure 门户中执行以下步骤：

1. 在 Azure 门户的左侧导航窗格中，选择“所有服务”  。 选择“应用注册”  。
1. 从应用注册列表中选择你的应用程序。
1. 选择应用程序，然后选择“证书和机密”。  在“客户端机密”部分选择“新建客户端机密”。  
1. 若要创建机密，请提供机密的说明。 然后，选择机密的过期时间：一年、两年或不过期。
1. 选择“添加”  ，创建并显示机密。 将机密值复制到安全的位置，因为离开该页面后将无法再次访问它。

    ![创建密钥](./media/batch-aad-auth/secret-key.png)

### <a name="assign-rbac-to-your-application"></a>向应用程序分配 RBAC

若要使用服务主体进行验证，需要向应用程序分配 RBAC。 执行以下步骤:

1. 在 Azure 门户中，导航到应用程序使用的 Batch 帐户。
1. 在 Batch 帐户的“设置”  部分，选择“访问控制(标识和访问管理)”  。
1. 选择“角色分配”选项卡  。
1. 选择“添加角色分配”  。
1. 在“角色”  下拉列表中，为应用程序选择参与者  或读者  角色。 有关这些角色的详细信息，请参阅 [Azure 门户中基于角色的访问控制入门](../role-based-access-control/overview.md)。
1. 在“选择”  字段中，输入应用程序的名称。 从列表中选择你的应用程序，然后选择“保存”  。

现在，应用程序应出现在访问控制设置中，同时已分配有 RBAC 角色。

![向应用程序分配 RBAC 角色](./media/batch-aad-auth/app-rbac-role.png)

### <a name="assign-a-custom-role"></a>分配自定义角色

自定义角色向用户授予用于提交作业、任务等的精细权限。 这可以防止用户执行影响成本的操作，例如创建池或修改节点。

可以使用自定义角色向 Azure AD 用户、组或服务主体授予以下 RBAC 操作的权限：

- Microsoft.Batch/batchAccounts/pools/write
- Microsoft.Batch/batchAccounts/pools/delete
- Microsoft.Batch/batchAccounts/pools/read
- Microsoft.Batch/batchAccounts/jobSchedules/write
- Microsoft.Batch/batchAccounts/jobSchedules/delete
- Microsoft.Batch/batchAccounts/jobSchedules/read
- Microsoft.Batch/batchAccounts/jobs/write
- Microsoft.Batch/batchAccounts/jobs/delete
- Microsoft.Batch/batchAccounts/jobs/read
- Microsoft.Batch/batchAccounts/certificates/write
- Microsoft.Batch/batchAccounts/certificates/delete
- Microsoft.Batch/batchAccounts/certificates/read
- Microsoft.Batch/batchAccounts/read（适用于任何读取操作）
- Microsoft.Batch/batchAccounts/listKeys/action（适用于任何操作）

自定义角色适用于通过 Azure AD 而不是 Batch 帐户凭据（共享密钥）进行身份验证的用户。 请注意，Batch 帐户凭据将为 Batch 帐户授予完全权限。 另请注意，使用 Autopool 的作业需要池级别权限。

下面是自定义角色定义的示例：

```json
{
 "properties":{
    "roleName":"Azure Batch Custom Job Submitter",
    "type":"CustomRole",
    "description":"Allows a user to submit jobs to Azure Batch but not manage pools",
    "assignableScopes":[
      "/subscriptions/88888888-8888-8888-8888-888888888888"
    ],
    "permissions":[
      {
        "actions":[
          "Microsoft.Batch/*/read",
          "Microsoft.Authorization/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Support/*",
          "Microsoft.Insights/alertRules/*"
        ],
        "notActions":[

        ],
        "dataActions":[
          "Microsoft.Batch/batchAccounts/jobs/*",
          "Microsoft.Batch/batchAccounts/jobSchedules/*"
        ],
        "notDataActions":[

        ]
      }
    ]
  }
}
```

有关如何创建自定义角色的更多常规信息，请参阅 [Azure 资源的自定义角色](../role-based-access-control/custom-roles.md)。

### <a name="get-the-tenant-id-for-your-azure-active-directory"></a>获取 Azure Active Directory 的租户 ID

租户 ID 用于标识向应用程序提供身份验证服务的 Azure AD 租户。 若要获取租户 ID，请按照以下步骤操作：

1. 在 Azure 门户中，选择 Active Directory。
1. 选择“属性”  。
1. 复制为“目录 ID”提供的 GUID 值。  该值也称为租户 ID。

![复制目录 ID](./media/batch-aad-auth/aad-directory-id.png)

## <a name="code-examples"></a>代码示例

此部分中的代码示例演示如何使用集成身份验证和服务主体通过 Azure AD 进行验证。 这些代码示例大多使用了 .NET，但概念与其他语言类似。

> [!NOTE]
> Azure AD 身份验证令牌在一小时后过期。 使用生存期较长的 **BatchClient** 对象时，我们建议每次发出请求都从 ADAL 中检索令牌，确保始终获得有效的令牌。
>
>
> 要在 .NET 中实现此目的，可编写一个方法从 Azure AD 中检索令牌，然后将该方法作为委派传递给 **BatchTokenCredentials** 对象。 这样，每次批处理服务发出请求都会调用该委派方法，确保提供有效的令牌。 默认情况下，ADAL 会缓存令牌，以便只在必要时，才从 Azure AD 中检索新令牌。 有关 Azure AD 中的令牌的详细信息，请参阅 [Azure AD 的身份验证方案][aad_auth_scenarios]。
>
>

### <a name="code-example-using-azure-ad-integrated-authentication-with-batch-net"></a>代码示例：将 Azure AD 集成身份验证与 Batch .NET 一起使用

若要在 Batch .NET 中使用集成身份验证进行验证，请参考 [Azure Batch .NET](https://www.nuget.org/packages/Microsoft.Azure.Batch/) 包和 [ADAL](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) 包。

在代码中包含以下 `using` 语句：

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

在代码中引用 Azure AD 终结点，包括租户 ID。 若要检索租户 ID，请按照[获取 Azure Active Directory 的租户 ID](#get-the-tenant-id-for-your-active-directory)中概述的步骤进行操作：

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

引用 Batch 服务资源终结点：

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

引用 Batch 帐户：

```csharp
private const string BatchAccountUrl = "https://myaccount.mylocation.batch.azure.com";
```

指定应用程序的应用程序 ID（客户端 ID）。 应用程序 ID 在 Azure 门户中的应用注册中提供：

```csharp
private const string ClientId = "<application-id>";
```

另外，如果已将应用程序指定为本机应用程序，请复制你指定的重定向 URI。 在代码中指定的重定向 URI 必须与注册应用程序时提供的重定向 URI 相匹配：

```csharp
private const string RedirectUri = "http://mybatchdatasample";
```

编写一个回调方法从 Azure AD 获取身份验证令牌。 此处所示的 GetAuthenticationTokenAsync  回调方法调用 ADAL 对与应用程序交互的用户进行验证。 ADAL 提供的 AcquireTokenAsync  方法提示用户输入其凭据，用户提供凭据后，应用程序可继续工作（除非已有缓存凭据）：

```csharp
public static async Task<string> GetAuthenticationTokenAsync()
{
    var authContext = new AuthenticationContext(AuthorityUri);

    // Acquire the authentication token from Azure AD.
    var authResult = await authContext.AcquireTokenAsync(BatchResourceUri,
                                                        ClientId,
                                                        new Uri(RedirectUri),
                                                        new PlatformParameters(PromptBehavior.Auto));

    return authResult.AccessToken;
}
```

构造使用委派作为参数的 **BatchTokenCredentials** 对象。 使用这些凭据打开 **BatchClient** 对象。 可以使用该 BatchClient  对象针对 Batch 服务执行后续操作：

```csharp
public static async Task PerformBatchOperations()
{
    Func<Task<string>> tokenProvider = () => GetAuthenticationTokenAsync();

    using (var client = await BatchClient.OpenAsync(new BatchTokenCredentials(BatchAccountUrl, tokenProvider)))
    {
        await client.JobOperations.ListJobs().ToListAsync();
    }
}
```

### <a name="code-example-using-an-azure-ad-service-principal-with-batch-net"></a>代码示例：将 Azure AD 服务主体与 Batch .NET 一起使用

若要在 Batch .NET 中使用服务主体进行验证，请参考 [Azure Batch .NET](https://www.nuget.org/packages/Azure.Batch/) 包和 [ADAL](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) 包。

在代码中包含以下 `using` 语句：

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

在代码中引用 Azure AD 终结点，包括租户 ID。 使用服务主体时，必须提供特定于租户的终结点。 若要检索租户 ID，请按照[获取 Azure Active Directory 的租户 ID](#get-the-tenant-id-for-your-active-directory)中概述的步骤进行操作：

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

引用 Batch 服务资源终结点：

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

引用 Batch 帐户：

```csharp
private const string BatchAccountUrl = "https://myaccount.mylocation.batch.azure.com";
```

指定应用程序的应用程序 ID（客户端 ID）。 应用程序 ID 在 Azure 门户中的应用注册中提供：

```csharp
private const string ClientId = "<application-id>";
```

指定从 Azure 门户复制的密钥：

```csharp
private const string ClientKey = "<secret-key>";
```

编写一个回调方法从 Azure AD 获取身份验证令牌。 此处显示的 GetAuthenticationTokenAsync  回调方法调用 ADAL 进行无人参与的身份验证：

```csharp
public static async Task<string> GetAuthenticationTokenAsync()
{
    AuthenticationContext authContext = new AuthenticationContext(AuthorityUri);
    AuthenticationResult authResult = await authContext.AcquireTokenAsync(BatchResourceUri, new ClientCredential(ClientId, ClientKey));

    return authResult.AccessToken;
}
```

构造使用委派作为参数的 **BatchTokenCredentials** 对象。 使用这些凭据打开 **BatchClient** 对象。 然后，使用该 **BatchClient** 对象针对 Batch 服务执行后续操作：

```csharp
public static async Task PerformBatchOperations()
{
    Func<Task<string>> tokenProvider = () => GetAuthenticationTokenAsync();

    using (var client = await BatchClient.OpenAsync(new BatchTokenCredentials(BatchAccountUrl, tokenProvider)))
    {
        await client.JobOperations.ListJobs().ToListAsync();
    }
}
```

### <a name="code-example-using-an-azure-ad-service-principal-with-batch-python"></a>代码示例：将 Azure AD 服务主体与 Batch Python 一起使用

若要在 Batch Python 中使用服务主体进行身份验证，请安装并引用 [azure-batch](https://pypi.org/project/azure-batch/) 和 [azure-common](https://pypi.org/project/azure-common/) 模块。

```python
from azure.batch import BatchServiceClient
from azure.common.credentials import ServicePrincipalCredentials
```

使用服务主体时，必须提供租户 ID。 若要检索租户 ID，请按照[获取 Azure Active Directory 的租户 ID](#get-the-tenant-id-for-your-active-directory)中概述的步骤进行操作：

```python
TENANT_ID = "<tenant-id>"
```

引用 Batch 服务资源终结点：

```python
RESOURCE = "https://batch.core.windows.net/"
```

引用 Batch 帐户：

```python
BATCH_ACCOUNT_URL = "https://myaccount.mylocation.batch.azure.com"
```

指定应用程序的应用程序 ID（客户端 ID）。 应用程序 ID 在 Azure 门户中的应用注册中提供：

```python
CLIENT_ID = "<application-id>"
```

指定从 Azure 门户复制的密钥：

```python
SECRET = "<secret-key>"
```

创建一个 **ServicePrincipalCredentials** 对象：

```python
credentials = ServicePrincipalCredentials(
    client_id=CLIENT_ID,
    secret=SECRET,
    tenant=TENANT_ID,
    resource=RESOURCE
)
```

使用服务主体凭据打开一个 **BatchServiceClient** 对象。 然后，使用该 **BatchServiceClient** 对象针对 Batch 服务执行后续操作。

```python
    batch_client = BatchServiceClient(
    credentials,
    base_url=BATCH_ACCOUNT_URL
)
```

## <a name="next-steps"></a>后续步骤

- 有关 Azure AD 的详细信息，请阅读 [Azure Active Directory 文档](https://docs.microsoft.com/azure/active-directory/)。 演示如何使用 [Azure 代码示例](https://azure.microsoft.com/resources/samples/?service=active-directory)库中提供的 ADAL 的深度讲解示例。

- 若要了解关于服务主体的详细信息，请参阅 [Azure Active Directory 中的应用程序和服务主体对象](../active-directory/develop/app-objects-and-service-principals.md)。 若要使用 Azure 门户创建服务主体，请参阅[使用门户创建可访问资源的 Active Directory 应用程序和服务主体](../active-directory/develop/howto-create-service-principal-portal.md)。 也可使用 PowerShell 或 Azure CLI 创建服务主体。

- 若要使用 Azure AD 对 Batch 应用程序进行验证，请参阅[使用 Active Directory 对 Batch 管理解决方案进行验证](batch-aad-auth-management.md)。

- 有关如何创建使用 Azure AD 令牌进行身份验证的 Batch 客户端的 Python 示例，请参阅[使用 Python 脚本部署 Azure Batch 自定义映像](https://github.com/azurebigcompute/recipes/blob/master/Azure%20Batch/CustomImages/CustomImagePython.md)示例。

[aad_about]:../active-directory/fundamentals/active-directory-whatis.md "什么是 Azure Active Directory？"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]: ../active-directory/active-directory-authentication-scenarios.md "Azure AD 的身份验证方案"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "将应用程序与 Azure Active Directory 集成"
[azure_portal]: https://portal.azure.com
