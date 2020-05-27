---
title: 使用 Azure Active Directory 对 Batch 管理解决方案进行身份验证
description: 了解如何使用 Azure Active Directory 通过使用 Batch Management .NET 库的应用程序进行身份验证。
ms.topic: how-to
ms.date: 04/27/2017
ms.custom: has-adal-ref
ms.openlocfilehash: ec9cf15f37c3ca7e4e477c628733d34cac21c141
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83726887"
---
# <a name="authenticate-batch-management-solutions-with-active-directory"></a>使用 Active Directory 对 Batch 管理解决方案进行身份验证

调用 Azure Batch 管理服务的应用程序使用 [Azure Active Directory][aad_about] (Azure AD) 进行身份验证。 Azure AD 是 Microsoft 提供的基于多租户云的目录和标识管理服务。 Azure 本身使用 Azure AD 来对其客户、服务管理员和组织用户进行身份验证。

批处理管理 .NET 库公开用于使用批处理帐户、帐户密钥、应用程序和应用程序包的类型。 批处理管理 .NET 库是一个 Azure 资源提供程序客户端，与 [Azure 资源管理器][resman_overview] 结合使用可以编程方式管理这些资源。 需要使用 Azure AD 对通过任何 Azure 资源提供程序客户端（包括 Batch 管理 .NET 库）和 [Azure 资源管理器][resman_overview]发出的请求进行身份验证。

本文探讨如何使用 Azure AD，在使用 Batch 管理 .NET 库的应用程序中进行身份验证。 我们将演示如何使用 Azure AD 和集成身份验证对订阅管理员或协同管理员进行身份验证。 我们使用 GitHub 上提供的 [AccountManagment][acct_mgmt_sample] 示例项目来逐步讲解如何将 Azure AD 与 Batch 管理 .NET 库配合使用。

若要详细了解批处理管理 .NET 库的用法和 AccountManagement 示例，请参阅 [Manage Batch accounts and quotas with the Batch Management client library for .NET](batch-management-dotnet.md)（使用适用于 .NET 的批处理管理客户端库来管理批处理帐户和配额）。

## <a name="register-your-application-with-azure-ad"></a>将应用程序注册到 Azure AD

Azure [Active Directory 身份验证库][aad_adal] (ADAL) 提供一个可在应用程序中使用的 Azure AD 编程接口。 若要从应用程序调用 ADAL，必须在 Azure AD 租户中注册该应用程序。 注册应用程序时，请向 Azure AD 提供有关该应用程序的信息，包括该应用程序在 Azure AD 租户中的名称。 然后，Azure AD 将提供一个应用程序 ID，在运行时，可以使用该 ID 将应用程序与 Azure AD 相关联。 若要详细信息应用程序 ID，请参阅 [Azure Active Directory 中的应用程序对象和服务主体对象](../active-directory/develop/app-objects-and-service-principals.md)。

若要注册 AccountManagement 示例应用程序，请按照[将应用程序与 Azure Active Directory 集成][aad_integrate]的[添加应用程序](../active-directory/develop/quickstart-register-app.md)部分中的步骤操作。 指定“本机客户端应用程序”作为应用程序类型。  用于重定向 URI  的行业标准 OAuth 2.0 URI 是 `urn:ietf:wg:oauth:2.0:oob`。 但可为重定向 URI  指定任何有效的 URI（例如 `http://myaccountmanagementsample`），它不需要是实际的终结点：

![](./media/batch-aad-auth-management/app-registration-management-plane.png)

完成注册过程后，会列出应用程序的应用程序 ID 和对象（服务主体）ID。

![](./media/batch-aad-auth-management/app-registration-client-id.png)

## <a name="grant-the-azure-resource-manager-api-access-to-your-application"></a>向 Azure Resource Manager API 授予应用程序访问权限

接下来，需要将应用程序的访问权限委派给 Azure Resource Manager API。 Resource Manager API 的 Azure AD 标识符为 **Windows Azure Service Management API**。

在 Azure 门户中执行以下步骤：

1. 在 Azure 门户的左侧导航窗格中，选择“所有服务”，单击“应用注册”，并单击“添加”。   
2. 在应用注册列表中搜索应用程序名称：

    ![搜索应用程序名称](./media/batch-aad-auth-management/search-app-registration.png)

3. 此时会显示“设置”边栏选项卡。  在“API 访问”部分中，选择“所需的权限”。  
4. 单击“添加”添加新的所需权限。 
5. 在步骤 1 中输入 **Windows Azure Service Management API**，从结果列表中选择该 API，并单击“选择”按钮。 
6. 在步骤 2 中，选中“以组织用户的身份访问 Azure 经典部署模型”旁边的复选框，并单击“选择”按钮。  
7. 单击“完成”按钮。 

现在，“所需的权限”边栏选项卡会显示向 ADAL 和 Resource Manager API 授予的应用程序权限。  首先在 Azure AD 中注册应用程序时，默认向 ADAL 授予权限。

![向 Azure Resource Manager API 委派权限](./media/batch-aad-auth-management/required-permissions-management-plane.png)

## <a name="azure-ad-endpoints"></a>Azure AD 终结点

要使用 Azure AD 对 Batch 管理解决方案进行身份验证，将需要两个已知的终结点。

- Azure AD 常见终结点  ，未提供特定租户时（例如，集成身份验证），该终结点提供泛型凭据收集接口：

    `https://login.microsoftonline.com/common`

- Azure 资源管理器终结点  ，用于获取对 Batch 管理服务的请求进行身份验证的令牌：

    `https://management.core.windows.net/`

AccountManagement 示例应用程序定义这些终结点的常量。 请将以下常量保持不变：

```csharp
// Azure Active Directory "common" endpoint.
private const string AuthorityUri = "https://login.microsoftonline.com/common";
// Azure Resource Manager endpoint
private const string ResourceUri = "https://management.core.windows.net/";
```

## <a name="reference-your-application-id"></a>引用应用程序 ID

在运行时，客户端应用程序使用应用程序 ID（也称为客户端 ID）来访问 Azure AD。 在 Azure 门户中注册应用程序后，请更新代码，以使用 Azure AD 为已注册的应用程序提供的应用程序 ID。 在 AccountManagement 示例应用程序中，将 Azure 门户上的应用程序 ID 复制到相应的常量中：

```csharp
// Specify the unique identifier (the "Client ID") for your application. This is required so that your
// native client application (i.e. this sample) can access the Microsoft Graph API. For information
// about registering an application in Azure Active Directory, please see "Register an application with the Microsoft identity platform" here:
// https://docs.microsoft.com/en-us/azure/active-directory/develop/quickstart-register-app
private const string ClientId = "<application-id>";
```
此外，请复制在注册过程中指定的重定向 URI。 在代码中指定的重定向 URI 必须与注册应用程序时提供的重定向 URI 相匹配。

```csharp
// The URI to which Azure AD will redirect in response to an OAuth 2.0 request. This value is
// specified by you when you register an application with AAD (see ClientId comment). It does not
// need to be a real endpoint, but must be a valid URI (e.g. https://accountmgmtsampleapp).
private const string RedirectUri = "http://myaccountmanagementsample";
```

## <a name="acquire-an-azure-ad-authentication-token"></a>获取 Azure AD 身份验证令牌

在 Azure AD 租户中注册 AccountManagement 示例并更新示例源代码中的值后，便可以使用 Azure AD 对该示例进行身份验证。 运行该示例时，ADAL 会尝试获取身份验证令牌。 执行此步骤时，系统会提示输入 Microsoft 凭据：

```csharp
// Obtain an access token using the "common" AAD resource. This allows the application
// to query AAD for information that lies outside the application's tenant (such as for
// querying subscription information in your Azure account).
AuthenticationContext authContext = new AuthenticationContext(AuthorityUri);
AuthenticationResult authResult = authContext.AcquireToken(ResourceUri,
                                                        ClientId,
                                                        new Uri(RedirectUri),
                                                        PromptBehavior.Auto);
```

提供凭据后，示例应用程序可以继续向批处理管理服务发出身份验证的请求。

## <a name="next-steps"></a>后续步骤

有关运行 [AccountManagement 示例应用程序][acct_mgmt_sample]的详细信息，请参阅[使用适用于 .NET 的批处理管理客户端库来管理 Batch 帐户和配额](batch-management-dotnet.md)。

有关 Azure AD 的详细信息，请阅读 [Azure Active Directory 文档](https://docs.microsoft.com/azure/active-directory/)。 演示如何使用 [Azure 代码示例](https://azure.microsoft.com/resources/samples/?service=active-directory)库中提供的 ADAL 的深度讲解示例。

若要使用 Azure AD 对 Batch 服务应用程序进行身份验证，请参阅[使用 Active Directory 对 Batch 服务解决方案进行身份验证](batch-aad-auth.md)。


[aad_about]:../active-directory/fundamentals/active-directory-whatis.md "什么是 Azure Active Directory？"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]:../active-directory/develop/authentication-scenarios.md "Azure AD 的身份验证方案"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "将应用程序与 Azure Active Directory 集成"
[acct_mgmt_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement
[azure_portal]: https://portal.azure.com
[resman_overview]: ../azure-resource-manager/management/overview.md
