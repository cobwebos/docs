---
title: 对访问 Azure 服务总线实体的应用程序进行身份验证
description: 本文介绍如何对使用 Azure Active Directory 访问 Azure 服务总线实体（队列、主题等）的应用程序进行身份验证
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: f5142e59e1711d9bf63a7badc2d0947fd86f49f4
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2020
ms.locfileid: "87835963"
---
# <a name="authenticate-and-authorize-an-application-with-azure-active-directory-to-access-azure-service-bus-entities"></a>使用 Azure Active Directory 对应用程序进行身份验证和授权，使之能够访问 Azure 服务总线实体
Azure 服务总线支持使用 Azure Active Directory (Azure AD) 授权对服务总线实体（队列、主题、订阅或筛选器）的请求。 可以通过 Azure AD 使用基于角色的访问控制 (RBAC) 授予对服务主体的访问权限，该服务主体可能是用户、组或应用程序服务主体。 若要详细了解角色和角色分配，请参阅[了解不同的角色](../role-based-access-control/overview.md)。

## <a name="overview"></a>概述
当某个安全主体（用户、组或应用程序）尝试访问服务总线实体时，请求必须获得授权。 使用 Azure AD 是，访问资源的过程包括两个步骤。 

 1. 首先，验证安全主体的身份并返回 OAuth 2.0 令牌。 用于请求令牌的资源名称为 `https://servicebus.azure.net`。
 1. 接下来，将该令牌作为请求的一部分传递给服务总线服务，用于授权访问指定的资源。

身份验证步骤要求应用程序请求包含在运行时使用的 OAuth 2.0 访问令牌。 如果应用程序在 Azure 实体（如 Azure VM、虚拟机规模集或 Azure 函数应用）中运行，它可以使用托管标识来访问资源。 若要了解如何对托管标识向服务总线服务发出的请求进行身份验证，请参阅[对使用 Azure Active Directory 和 Azure 资源的托管标识访问 Azure 服务总线资源进行身份验证](service-bus-managed-service-identity.md)。 

授权步骤要求向安全主体分配一个或多个 Azure 角色。 Azure 服务总线提供了包含服务总线资源的权限集的 Azure 角色。 分配给安全主体的角色确定了该主体拥有的权限。 若要了解有关将 Azure 角色分配到 Azure 服务总线的详细信息，请参阅 azure[服务总线的 azure 内置角色](#azure-built-in-roles-for-azure-service-bus)。 

向服务总线发出请求的本机应用程序和 Web 应用程序也可以使用 Azure AD 进行授权。 本文介绍如何请求访问令牌，并使用它针对服务总线资源进行请求授权。 


## <a name="assigning-azure-roles-for-access-rights"></a>为访问权限分配 Azure 角色
Azure Active Directory (Azure AD) 通过[AZURE RBAC (的 azure 基于角色的访问控制](../role-based-access-control/overview.md)来授予对受保护资源的访问权限。 Azure 服务总线定义一组 Azure 内置角色，其中包含用于访问服务总线实体的常用权限集，还可以定义用于访问数据的自定义角色。

将 Azure 角色分配到 Azure AD 安全主体时，Azure 会向该安全主体授予对这些资源的访问权限。 访问权限可以局限到订阅、资源组或服务总线命名空间级别。 Azure AD 安全主体可以是用户、组、应用程序服务主体，也可以是 [Azure 资源的托管标识](../active-directory/managed-identities-azure-resources/overview.md)。

## <a name="azure-built-in-roles-for-azure-service-bus"></a>Azure Service Bus 的 azure 内置角色
对于 Azure 服务总线，通过 Azure 门户和 Azure 资源管理 API 对命名空间和所有相关资源的管理已使用*基于角色的访问控制* (RBAC) 模型进行了保护。 Azure 提供以下 Azure 内置角色，用于授权访问服务总线命名空间：

- [Azure 服务总线数据所有者](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner)：允许对服务总线命名空间及其实体（队列、主题、订阅和筛选器）进行数据访问
- [Azure 服务总线数据发送者](../role-based-access-control/built-in-roles.md#azure-service-bus-data-sender)：使用此角色可以为服务总线命名空间及其实体提供发送访问权限。
- [Azure 服务总线数据接收者](../role-based-access-control/built-in-roles.md#azure-service-bus-data-receiver)：使用此角色可以为服务总线命名空间及其实体提供接收访问权限。 

## <a name="resource-scope"></a>资源范围 
向安全主体分配 Azure 角色之前，请确定安全主体应具有的访问权限的范围。 最佳做法指出，最好是授予尽可能小的范围。

以下列表描述了可将服务总线资源访问权限限定到哪些级别，从最小的范围开始：

- **队列**、**主题**或**订阅**：角色分配适用于特定的服务总线实体。 目前，Azure 门户不支持在订阅级别将用户/组/托管标识分配到服务总线 Azure 角色。 
- **服务总线命名空间**：角色分配横跨命名空间中服务总线的整个拓扑，并延伸至与之关联的使用者组。
- **资源组**：角色分配适用于资源组下的所有服务总线资源。
- **订阅**：角色分配适用于订阅的所有资源组中的所有服务总线资源。

> [!NOTE]
> 请记住，Azure 角色分配可能需要长达五分钟才能传播。 

有关如何定义内置角色的详细信息，请参阅[了解角色定义](../role-based-access-control/role-definitions.md#management-and-data-operations)。 有关创建 Azure 自定义角色的详细信息，请参阅[azure 自定义角色](../role-based-access-control/custom-roles.md)。


## <a name="assign-azure-roles-using-the-azure-portal"></a>使用 Azure 门户分配 Azure 角色  
若要详细了解如何使用 RBAC 和 Azure 门户管理对 Azure 资源的访问，请参阅[此文](..//role-based-access-control/role-assignments-portal.md)。 

在确定角色分配的适当范围后，在 Azure 门户中导航到该资源。 显示资源的“访问控制(IAM)”设置，并按以下说明管理角色分配：

> [!NOTE]
> 下面介绍的步骤为服务总线命名空间分配一个角色。 可以按照相同的步骤向其他受支持的范围（资源组、订阅等）分配角色。

1. 在 [Azure 门户](https://portal.azure.com/)中，导航到你的服务总线命名空间。 选择左侧菜单上的“访问控制(标识和访问管理)”，显示命名空间的访问控制设置  。 如果需要创建服务总线命名空间，请按此文中的说明操作：[创建服务总线消息传送命名空间](service-bus-create-namespace-portal.md)。

    ![在左侧菜单中选择“访问控制”](./media/authenticate-application/select-access-control-menu.png)
1. 选择“角色分配”  选项卡以查看角色分配列表。 在工具栏上选择“添加”按钮，然后选择“添加角色分配”。 

    ![工具栏上的“添加”按钮](./media/authenticate-application/role-assignments-add-button.png)
1. 在“添加角色分配”  页上，执行以下步骤：
    1. 选择要分配的**服务总线角色**。 
    1. 通过搜索找到要为其分配该角色的**安全主体**（用户、组、服务主体）。
    1. 选择“保存”以保存角色分配。 

        ![向用户分配角色](./media/authenticate-application/assign-role-to-user.png)
    4. 分配有该角色的标识列出在该角色下。 例如，下图显示 Azure-users 充当“Azure 服务总线数据所有者”角色。 
        
        ![列表中的用户](./media/authenticate-application/user-in-list.png)

可以遵循类似的步骤来分配限定为资源组或订阅范围的角色。 定义角色及其范围后，可以使用 [GitHub 上的示例](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl)测试此行为。


## <a name="authenticate-from-an-application"></a>通过应用程序进行身份验证
将 Azure AD 与服务总线配合使用的主要优势之一在于，不再需要在代码中存储凭据。 可以从 Microsoft 标识平台请求 OAuth 2.0 访问令牌。 Azure AD 对运行应用程序的安全主体（用户、组或服务主体）进行身份验证。 如果身份验证成功，Azure AD 会将访问令牌返回应用程序，应用程序可随之使用访问令牌对 Azure 服务总线请求授权。

以下部分介绍如何配置本机应用程序或 Web 应用程序，以便在 Microsoft 标识平台 2.0 中进行身份验证。 有关 Microsoft 标识平台 2.0 的详细信息，请参阅 [Microsoft 标识平台 (v2.0) 概述](../active-directory/develop/v2-overview.md)。

有关 OAuth 2.0 代码授权流的概述，请参阅[使用 OAuth 2.0 代码授权流来授权访问 Azure Active Directory Web 应用程序](../active-directory/develop/v2-oauth2-auth-code-flow.md)。

### <a name="register-your-application-with-an-azure-ad-tenant"></a>将应用程序注册到 Azure AD 租户
使用 Azure AD 授权访问服务总线实体的第一步是，通过 [Azure 门户](https://portal.azure.com/)在 Azure AD 租户中注册客户端应用程序。 注册客户端应用程序时，需要向 AD 提供关于应用程序的信息。 Azure AD 随后会提供客户端 ID（也称为应用程序 ID）。在运行时，可以使用该 ID 将应用程序与 Azure AD 关联。 若要详细了解客户端 ID，请参阅 [Azure Active Directory 中的应用程序对象和服务主体对象](../active-directory/develop/app-objects-and-service-principals.md)。 

下图显示了注册 Web 应用程序的步骤：

![注册应用程序](./media/authenticate-application/app-registrations-register.png)

> [!Note]
> 如果将应用程序注册为本机应用程序，可为重定向 URI 指定任何有效的 URI。 对于本机应用程序，此值不一定要是实际的 URL。 对于 Web 应用程序，重定向 URI 必须是有效的 URI，因为它指定了要向哪个 URL 提供令牌。

注册应用程序后，可在“设置”下看到“应用程序(客户端) ID”：

![已注册的应用程序的应用程序 ID](./media/authenticate-application/application-id.png)

有关向 Azure AD 注册应用程序的详细信息，请参阅[将应用程序与 Azure Active Directory](../active-directory/develop/quickstart-v2-register-an-app.md) 集成。

> [!IMPORTANT]
> 记下 **TenantId** 和 **ApplicationId**。 将需要这些值来运行应用程序。

### <a name="create-a-client-secret"></a>创建客户端机密   
请求令牌时，应用程序需要使用客户端机密来证明其身份。 若要添加客户端机密，请执行以下步骤。

1. 在 Azure 门户中导航到你的应用注册（如果尚未转到此页上）。
1. 在左侧菜单上，选择“证书和机密”  。
1. 在“客户端机密”下，选择“新建客户端机密”以创建新的机密。

    ![新建客户端机密 - 按钮](./media/authenticate-application/new-client-secret-button.png)
1. 提供机密说明，并选择所需的过期时间间隔，然后选择“添加”。

    ![“添加客户端机密”页](./media/authenticate-application/add-client-secret-page.png)
1. 请马上将新机密的值复制到安全位置。 填充值只会显示一次。

    ![客户端机密](./media/authenticate-application/client-secret.png)

### <a name="permissions-for-the-service-bus-api"></a>服务总线 API 的权限
如果应用程序是一个控制台应用程序，则必须注册一个本机应用程序并将 **Microsoft.ServiceBus** 的 API 权限添加到“必需的权限”集。 本机应用程序在 Azure AD 中还需要有一个充当标识符的 **redirect-URI**，该 URI 不需要是网络目标。 对于此示例请使用 `https://servicebus.microsoft.com`，因为示例代码已使用了该 URI。

### <a name="client-libraries-for-token-acquisition"></a>用于获取令牌的客户端库  
注册应用程序并向其授予在 Azure 服务总线发送/接收数据的权限后，可将代码添加到应用程序，以便对安全主体进行身份验证并获取 OAuth 2.0 令牌。 若要进行身份验证并获取令牌，可以使用 [Microsoft 标识平台身份验证库](../active-directory/develop/reference-v2-libraries.md)，或者其他支持 OpenID 或 Connect 1.0 的开源库。 然后，应用程序可以使用访问令牌授权针对 Azure 服务总线发出的请求。

有关支持获取令牌的方案列表，请参阅[适用于 .NET 的 Microsoft 身份验证库 (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) GitHub 存储库的[方案](https://aka.ms/msal-net-scenarios)部分。

## <a name="sample-on-github"></a>GitHub 上的示例
窗口 GitHub 上的以下示例：[服务总线的基于角色的访问控制](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl)。 

使用“客户端机密登录”选项，而不是“交互用户登录”选项。 使用客户端机密选项时，看不到弹出窗口。 应用程序会使用租户 ID 和应用 ID 进行身份验证。 

### <a name="run-the-sample"></a>运行示例

在运行示例前，请编辑 **app.config** 文件并根据方案设置以下值：

- `tenantId`：设置为 **TenantId** 值。
- `clientId`：设置为 **ApplicationId** 值。
- `clientSecret`：如果希望使用客户端机密进行登录，请在 Azure AD 中创建它。 此外，请使用 Web 应用或 API 而非本机应用。 另外，请在之前创建的命名空间中将该应用添加到“访问控制(IAM)”  下。
- `serviceBusNamespaceFQDN`：设置为新创建的服务总线命名空间的完整 DNS 名称，例如 `example.servicebus.windows.net`。
- `queueName`：设置为所创建的队列的名称。
- 执行前面的步骤时在应用中指定的重定向 URI。

运行该控制台应用程序时，系统会提示你选择一个方案。 请通过键入相应的编号并按 ENTER 来选择“交互用户登录”  。 应用程序会显示一个登录窗口，要求你同意访问服务总线，然后使用登录标识通过该服务来演练发送/接收方案。


## <a name="next-steps"></a>后续步骤
- 若要了解有关 RBAC 的详细信息，请参阅[什么是 AZURE RBAC)  (azure 基于角色的访问控制](../role-based-access-control/overview.md)？
- 若要了解如何分配和管理 Azure PowerShell、Azure CLI 或 REST API 的 Azure 角色分配，请参阅以下文章：
    - [使用 Azure PowerShell 管理基于角色的访问控制 (RBAC)](../role-based-access-control/role-assignments-powershell.md)  
    - [使用 Azure CLI 管理基于角色的访问控制 (RBAC)](../role-based-access-control/role-assignments-cli.md)
    - [使用 REST API 管理基于角色的访问控制 (RBAC)](../role-based-access-control/role-assignments-rest.md)
    - [使用 Azure 资源管理器模板管理基于角色的访问控制 (RBAC)](../role-based-access-control/role-assignments-template.md)

若要了解有关服务总线消息传送的详细信息，请参阅以下主题。

- [服务总线 RBAC 示例](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl)
- [服务总线队列、主题和订阅](service-bus-queues-topics-subscriptions.md)
- [服务总线队列入门](service-bus-dotnet-get-started-with-queues.md)
- [如何使用服务总线主题和订阅](service-bus-dotnet-how-to-use-topics-subscriptions.md)
