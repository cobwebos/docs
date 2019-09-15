---
title: 对应用程序进行身份验证以访问 Azure 服务总线实体
description: 本文提供了有关使用 Azure Active Directory 访问 Azure 服务总线实体（队列、主题等）的应用程序的身份验证信息。
services: service-bus-messaging
ms.service: event-hubs
documentationcenter: ''
author: axisc
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: aschhab
ms.openlocfilehash: 6a78e4d81921fae8dcb325e9d72df1eee7b99a3b
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996991"
---
# <a name="authenticate-and-authorize-an-application-with-azure-active-directory-to-access-azure-service-bus-entities"></a>使用 Azure Active Directory 访问 Azure 服务总线实体，对应用程序进行身份验证和授权
Azure 服务总线支持使用 Azure Active Directory （Azure AD）对服务总线实体（队列、主题、订阅或筛选器）的请求进行授权。 可以通过 Azure AD 使用基于角色的访问控制 (RBAC) 授予对服务主体的访问权限，该服务主体可能是用户、组或应用程序服务主体。 若要了解有关角色和角色分配的详细信息，请参阅[了解不同的角色](../role-based-access-control/overview.md)。

## <a name="overview"></a>概述
当安全主体（用户、组或应用程序）尝试访问服务总线实体时，必须对该请求进行授权。 使用 Azure AD 是，访问资源的过程包括两个步骤。 

 1. 首先，对安全主体的身份进行身份验证，并返回 OAuth 2.0 令牌。 请求令牌的资源名称为`https://servicebus.azure.net`。
 1. 接下来，令牌作为请求的一部分传递到服务总线服务，以授权访问指定的资源。

身份验证步骤要求应用程序请求在运行时包含 OAuth 2.0 访问令牌。 如果应用程序在 Azure 实体（如 Azure VM、虚拟机规模集或 Azure Function app）内运行，则它可以使用托管标识来访问资源。 若要了解如何对服务总线服务的托管标识发出的请求进行身份验证，请参阅[使用 Azure 资源的 Azure Active Directory 和托管标识对 Azure 服务总线资源的访问权限进行身份验证](service-bus-managed-service-identity.md)。 

授权步骤需要将一个或多个 RBAC 角色分配给安全主体。 Azure 服务总线提供了包含服务总线资源的权限集的 RBAC 角色。 分配给安全主体的角色确定了该主体拥有的权限。 若要详细了解如何将 RBAC 角色分配到 Azure 服务总线，请参阅[Azure 服务总线的内置 RBAC 角色](#built-in-rbac-roles-for-azure-service-bus)。 

向服务总线发出请求的本机应用程序和 web 应用程序还可以授权 Azure AD。 本文介绍如何请求访问令牌，并使用它对服务总线资源的请求进行授权。 


## <a name="assigning-rbac-roles-for-access-rights"></a>分配 RBAC 角色以授予访问权限
Azure Active Directory (Azure AD) 通过[基于角色的访问控制 (RBAC)](../role-based-access-control/overview.md) 授权访问受保护的资源。 Azure 服务总线定义一组内置 RBAC 角色，其中包含用于访问服务总线实体的常用权限集，还可以定义用于访问数据的自定义角色。

将 RBAC 角色分配到 Azure AD 安全主体后，Azure 会向该安全主体授予对这些资源的访问权限。 访问的范围可以是订阅、资源组或服务总线命名空间的级别。 Azure AD 安全主体可以是用户、组、应用程序服务主体，也可以是 [Azure 资源的托管标识](../active-directory/managed-identities-azure-resources/overview.md)。

## <a name="built-in-rbac-roles-for-azure-service-bus"></a>Azure 服务总线的内置 RBAC 角色
对于 Azure 服务总线，通过 Azure 门户和 Azure 资源管理 API 对命名空间和所有相关资源的管理已使用*基于角色的访问控制* (RBAC) 模型进行了保护。 Azure 提供以下内置 RBAC 角色，用于授权对服务总线命名空间的访问：

- [Azure 服务总线数据所有者](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner)：启用对服务总线命名空间及其实体（队列、主题、订阅和筛选器）的数据访问
- [Azure 服务总线数据发送](../role-based-access-control/built-in-roles.md#azure-service-bus-data-sender)程序：使用此角色授予对服务总线命名空间及其实体的发送访问权限。
- [Azure 服务总线数据接收方](../role-based-access-control/built-in-roles.md#azure-service-bus-data-receiver)：使用此角色可授予对服务总线命名空间及其实体的接收访问权限。 

## <a name="resource-scope"></a>资源范围 
在将 RBAC 角色分配到某个安全主体之前，请确定该安全主体应该获取的访问范围。 最佳做法指出，最好是授予尽可能小的范围。

以下列表描述了从最窄的范围开始，可以将对服务总线资源的访问范围限定为的级别：

- **队列**、**主题**或**订阅**：角色分配适用于特定的服务总线实体。 目前，Azure 门户不支持在订阅级别将用户/组/托管标识分配到服务总线 RBAC 角色。 
- **服务总线命名空间**：角色分配跨越命名空间和与其关联的使用者组中的服务总线的整个拓扑。
- **资源组**：角色分配适用于资源组下的所有服务总线资源。
- **订阅**：角色分配适用于订阅中所有资源组中的所有服务总线资源。

> [!NOTE]
> 请记住，RBAC 角色分配可能需要最多五分钟的时间进行传播。 

有关如何定义内置角色的详细信息，请参阅[了解角色定义](../role-based-access-control/role-definitions.md#management-and-data-operations)。 若要了解如何创建自定义 RBAC 角色，请参阅[针对 Azure 基于角色的访问控制创建自定义角色](../role-based-access-control/custom-roles.md)。


## <a name="assign-rbac-roles-using-the-azure-portal"></a>使用 Azure 门户分配 RBAC 角色  
若要了解有关使用 RBAC 和 Azure 门户管理对 Azure 资源的访问的详细信息，请参阅[此文](..//role-based-access-control/role-assignments-portal.md)。 

确定角色分配的适当作用域后，请导航到 Azure 门户中的相应资源。 显示资源的 "访问控制（IAM）" 设置，并按照这些说明来管理角色分配：

> [!NOTE]
> 下面所述的步骤将角色分配给服务总线命名空间。 可以按照相同的步骤将角色分配到其他受支持的作用域（资源组、订阅等）。

1. 在[Azure 门户](https://portal.azure.com/)中，导航到服务总线命名空间。 选择左侧菜单中的 "**访问控制（IAM）** "，显示该命名空间的访问控制设置。 如果需要创建服务总线命名空间，请按照本文中的说明进行操作：[创建一个服务总线消息传送命名空间](service-bus-create-namespace-portal.md)。

    ![选择左侧菜单中的 "访问控制"](./media/authenticate-application/select-access-control-menu.png)
1. 选择“角色分配”选项卡以查看角色分配列表。 选择工具栏上的 "**添加**" 按钮，然后选择 "**添加角色分配**"。 

    ![工具栏上的 "添加" 按钮](./media/authenticate-application/role-assignments-add-button.png)
1. 在“添加角色分配”页上，执行以下步骤：
    1. 选择要分配的**服务总线角色**。 
    1. 搜索以查找要向其分配角色的**安全主体**（用户、组和服务主体）。
    1. 选择 "**保存**" 以保存角色分配。 

        ![向用户分配角色](./media/authenticate-application/assign-role-to-user.png)
    4. 分配有该角色的标识列出在该角色下。 例如，下图显示 Azure-用户是 Azure 服务总线数据所有者角色。 
        
        ![列表中的用户](./media/authenticate-application/user-in-list.png)

可以执行类似的步骤来分配作用域为资源组或订阅的角色。 定义角色及其作用域后，可以通过[GitHub 上的示例](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl)测试此行为。


## <a name="authenticate-from-an-application"></a>通过应用程序进行身份验证
将 Azure AD 与 Service Bus 结合使用的主要优点是，您的凭据不再需要存储在代码中。 相反，你可以从 Microsoft 标识平台请求 OAuth 2.0 访问令牌。 Azure AD 对运行应用程序的安全主体（用户、组或服务主体）进行身份验证。 如果身份验证成功，Azure AD 会返回应用程序的访问令牌，应用程序随后可以使用访问令牌向 Azure 服务总线授权请求。

以下部分介绍了如何配置本机应用程序或 web 应用程序，以便通过 Microsoft 标识平台2.0 进行身份验证。 有关 Microsoft 标识平台 2.0 的详细信息，请参阅 [Microsoft 标识平台 (v2.0) 概述](../active-directory/develop/v2-overview.md)。

有关 OAuth 2.0 代码授权流的概述，请参阅[使用 OAuth 2.0 代码授权流来授权访问 Azure Active Directory Web 应用程序](../active-directory/develop/v2-oauth2-auth-code-flow.md)。

### <a name="register-your-application-with-an-azure-ad-tenant"></a>将应用程序注册到 Azure AD 租户
使用 Azure AD 来授权服务总线实体的第一步是从[Azure 门户](https://portal.azure.com/)向 Azure AD 租户注册客户端应用程序。 注册客户端应用程序时，会向 AD 提供有关应用程序的信息。 然后 Azure AD 提供了一个客户端 ID （也称为应用程序 ID），您可以使用该 ID 将应用程序与 Azure AD 运行时关联。 若要详细了解客户端 ID，请参阅 [Azure Active Directory 中的应用程序对象和服务主体对象](../active-directory/develop/app-objects-and-service-principals.md)。 

下面的图像显示了用于注册 web 应用程序的步骤：

![注册应用程序](./media/authenticate-application/app-registrations-register.png)

> [!Note]
> 如果将应用程序注册为本机应用程序，则可以为重定向 URI 指定任何有效的 URI。 对于本机应用程序，此值不一定要是实际的 URL。 对于 Web 应用程序，重定向 URI 必须是有效的 URI，因为它指定了要向哪个 URL 提供令牌。

注册应用程序后，你将在 "**设置**" 下看到**应用程序（客户端） ID** ：

![已注册应用程序的应用程序 ID](./media/authenticate-application/application-id.png)

有关向 Azure AD 注册应用程序的详细信息，请参阅[将应用程序与 Azure Active Directory](../active-directory/develop/quickstart-v2-register-an-app.md) 集成。

> [!IMPORTANT]
> 请记下**TenantId**和**ApplicationId**。 你将需要这些值来运行应用程序。

### <a name="create-a-client-secret"></a>创建客户端机密   
请求令牌时，应用程序需要使用客户端机密来证明其身份。 若要添加客户端密钥，请执行以下步骤。

1. 如果你尚未在页面上，请导航到 Azure 门户中的应用注册。
1. 在左侧菜单中选择 "**证书 & 机密**"。
1. 在 "**客户端密码**" 下，选择 "**新建客户端密码**" 以创建新密钥。

    ![新客户端密码-按钮](./media/authenticate-application/new-client-secret-button.png)
1. 提供机密的说明，并选择所需的过期时间间隔，然后选择 "**添加**"。

    ![添加客户端密码页](./media/authenticate-application/add-client-secret-page.png)
1. 请马上将新机密的值复制到安全位置。 填充值仅显示一次。

    ![客户端密码](./media/authenticate-application/client-secret.png)

### <a name="permissions-for-the-service-bus-api"></a>服务总线 API 的权限
如果你的应用程序是一个控制台应用程序，则必须注册一个本机应用程序，并向**所需权限**集添加针对**Microsoft**的 API 权限。 本机应用程序在 Azure AD 中还需要一个**重定向 URI** ，用作标识符;该 URI 不需要是网络目标。 对于此示例请使用 `https://servicebus.microsoft.com`，因为示例代码已使用了该 URI。

### <a name="client-libraries-for-token-acquisition"></a>用于获取令牌的客户端库  
注册应用程序并向其授予在 Azure 服务总线中发送/接收数据的权限后，你可以将代码添加到应用程序以对安全主体进行身份验证并获取 OAuth 2.0 令牌。 若要进行身份验证并获取令牌，你可以使用其中一个[Microsoft 标识平台身份验证库](../active-directory/develop/reference-v2-libraries.md)或另一个支持 OpenID 或 connect 1.0 的开源库。 然后，应用程序可以使用访问令牌对 Azure 服务总线的请求进行授权。

有关支持获取令牌的方案列表，请参阅[适用于 .NET 的 Microsoft 身份验证库 (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) GitHub 存储库的[方案](https://aka.ms/msal-net-scenarios)部分。

## <a name="sample-on-github"></a>GitHub 上的示例
请参阅 GitHub 上的以下示例：[Service Bus 的角色基本访问控制](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl)。 

使用 "**客户端机密" 登录**选项，而不是**交互式用户登录**选项。 使用 "客户端密钥" 选项时，不会显示弹出窗口。 应用程序使用租户 ID 和应用 ID 进行身份验证。 

### <a name="run-the-sample"></a>运行示例

在运行该示例之前，请编辑**app.config**文件，并根据方案设置以下值：

- `tenantId`：设置为 **TenantId** 值。
- `clientId`：设置为 **ApplicationId** 值。
- `clientSecret`：如果希望使用客户端机密进行登录，请在 Azure AD 中创建它。 此外，请使用 Web 应用或 API 而非本机应用。 另外，请在之前创建的命名空间中将该应用添加到“访问控制(IAM)”下。
- `serviceBusNamespaceFQDN`：设置为新创建的服务总线命名空间的完整 DNS 名称，例如 `example.servicebus.windows.net`。
- `queueName`：设置为所创建的队列的名称。
- 执行前面的步骤时在应用中指定的重定向 URI。

运行控制台应用程序时，系统将提示你选择一个方案。 键入**交互式用户登录名**，然后按 enter。 应用程序会显示一个登录窗口，要求你同意访问服务总线，然后使用登录标识通过该服务来演练发送/接收方案。


## <a name="next-steps"></a>后续步骤
- 若要了解有关 RBAC 的详细信息，请参阅[什么是基于角色的访问控制（RBAC）](../role-based-access-control/overview.md)？
- 若要了解如何使用 Azure PowerShell、Azure CLI 或 REST API 分配和管理 RBAC 角色分配，请参阅以下文章：
    - [使用 Azure PowerShell 管理基于角色的访问控制 (RBAC)](../role-based-access-control/role-assignments-powershell.md)  
    - [使用 Azure CLI 管理基于角色的访问控制 (RBAC)](../role-based-access-control/role-assignments-cli.md)
    - [使用 REST API 管理基于角色的访问控制 (RBAC)](../role-based-access-control/role-assignments-rest.md)
    - [使用 Azure 资源管理器模板管理基于角色的访问控制（RBAC）](../role-based-access-control/role-assignments-template.md)

若要了解有关服务总线消息传送的详细信息，请参阅以下主题。

- [Service Bus RBAC 示例](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl)
- [服务总线队列、主题和订阅](service-bus-queues-topics-subscriptions.md)
- [服务总线队列入门](service-bus-dotnet-get-started-with-queues.md)
- [如何使用服务总线主题和订阅](service-bus-dotnet-how-to-use-topics-subscriptions.md)
