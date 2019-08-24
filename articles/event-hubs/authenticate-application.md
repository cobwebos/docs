---
title: 对应用程序进行身份验证以访问 Azure 事件中心资源
description: 本文提供了有关使用 Azure Active Directory 访问 Azure 事件中心资源对应用程序进行身份验证的信息
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: 9ea22dfc83883238923de06a764d7ef1f11cb475
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2019
ms.locfileid: "69992986"
---
# <a name="authenticate-an-application-with-azure-active-directory-to-access-event-hubs-resources"></a>使用 Azure Active Directory 对应用程序进行身份验证以访问事件中心资源
Microsoft Azure 基于 Azure Active Directory (Azure AD) 针对资源和应用程序提供了集成的访问控制管理功能。 将 Azure AD 与 Azure 事件中心结合使用的主要优势是, 无需再将凭据存储在代码中。 相反, 你可以从 Microsoft 标识平台请求 OAuth 2.0 访问令牌。 Azure AD 对运行应用程序的安全主体（用户、组或服务主体）进行身份验证。 如果身份验证成功, Azure AD 会返回应用程序的访问令牌, 然后应用程序可使用访问令牌向 Azure 事件中心资源授权请求。

当角色分配到 Azure AD 安全主体时, Azure 会向该安全主体授予对这些资源的访问权限。 可以将访问权限限定为订阅级别、资源组、事件中心命名空间或其下的任何资源。 Azure AD 安全性可以将角色分配给用户、组、应用程序服务主体或[Azure 资源的托管标识](../active-directory/managed-identities-azure-resources/overview.md)。 

> [!NOTE]
> 角色定义是权限的集合。 基于角色的访问控制 (RBAC) 控制通过角色分配如何强制实施这些权限。 角色分配包含三个要素：安全主体、角色订阅和范围。 有关详细信息, 请参阅[了解不同的角色](../role-based-access-control/overview.md)。

## <a name="built-in-roles-for-azure-event-hubs"></a>Azure 事件中心的内置角色
Azure 提供下列内置 RBAC 角色, 可使用 Azure AD 和 OAuth 授权访问事件中心数据:

- [Azure 事件中心数据所有者](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner-preview):使用此角色可以完全访问事件中心资源。
- [Azure 事件中心数据发送](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender-preview)程序:使用此角色授予对事件中心资源的发送访问权限。
- [Azure 事件中心数据接收方](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver-preview):使用此角色授予接收对事件中心资源的访问权限。   

> [!IMPORTANT]
> 预览版本支持将事件中心数据访问权限添加到所有者或参与者角色。 但是, 不能再遵守所有者和参与者角色的数据访问权限。 如果你使用的是 "所有者" 或 "参与者" 角色, 请切换到使用 Azure 事件中心数据所有者角色。

## <a name="assign-rbac-roles-using-the-azure-portal"></a>使用 Azure 门户分配 RBAC 角色  
若要了解有关使用 RBAC 和 Azure 门户管理对 Azure 资源的访问的详细信息, 请参阅[此文](..//role-based-access-control/role-assignments-portal.md)。 

确定角色分配的适当作用域后, 请导航到 Azure 门户中的相应资源。 显示资源的 "访问控制 (IAM)" 设置, 并按照这些说明来管理角色分配:

> [!NOTE]
> 下面所述的步骤会在事件中心命名空间下将角色分配给事件中心, 但你可以执行相同的步骤来分配作用域为任何事件中心资源的角色。

1. 在[Azure 门户](https://portal.azure.com/)中, 导航到事件中心命名空间。
2. 在 "**概述**" 页上, 选择要为其分配角色的事件中心。

    ![选择事件中心](./media/authenticate-application/select-event-hub.png)
1. 选择 "**访问控制 (IAM)** " 可显示事件中心的访问控制设置。 
1. 选择“角色分配”选项卡以查看角色分配列表。 选择工具栏上的 "**添加**" 按钮, 然后选择 "**添加角色分配**"。 

    ![工具栏上的 "添加" 按钮](./media/authenticate-application/role-assignments-add-button.png)
1. 在“添加角色分配”页上，执行以下步骤：
    1. 选择要分配的**事件中心角色**。 
    1. 搜索以查找要向其分配角色的**安全主体**(用户、组和服务主体)。
    1. 选择 "**保存**" 以保存角色分配。 

        ![向用户分配角色](./media/authenticate-application/assign-role-to-user.png)
    4. 分配有该角色的标识列出在该角色下。 例如, 下图显示 Azure-用户位于 Azure 事件中心数据所有者角色中。 
        
        ![列表中的用户](./media/authenticate-application/user-in-list.png)

可以执行类似的步骤来分配作用域为事件中心命名空间、资源组或订阅的角色。 定义角色及其作用域后, 可以[在此 GitHub 位置中](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac)测试此行为和示例。


## <a name="authenticate-from-an-application"></a>通过应用程序进行身份验证
将 Azure AD 与事件中心结合使用的主要优点是, 你的凭据不再需要存储在代码中。 相反, 你可以从 Microsoft 标识平台请求 OAuth 2.0 访问令牌。 Azure AD 对运行应用程序的安全主体 (用户、组或服务主体) 进行身份验证。 如果身份验证成功, Azure AD 会返回应用程序的访问令牌, 应用程序随后可以使用访问令牌向 Azure 事件中心授权请求。

以下部分介绍了如何配置本机应用程序或 web 应用程序, 以便通过 Microsoft 标识平台2.0 进行身份验证。 有关 Microsoft 标识平台 2.0 的详细信息，请参阅 [Microsoft 标识平台 (v2.0) 概述](../active-directory/develop/v2-overview.md)。

有关 OAuth 2.0 代码授权流的概述，请参阅[使用 OAuth 2.0 代码授权流来授权访问 Azure Active Directory Web 应用程序](../active-directory/develop/v2-oauth2-auth-code-flow.md)。

### <a name="register-your-application-with-an-azure-ad-tenant"></a>将应用程序注册到 Azure AD 租户
使用 Azure AD 向事件中心资源授权的第一步是从[Azure 门户](https://portal.azure.com/)向 Azure AD 租户注册客户端应用程序。 注册客户端应用程序时, 会向 AD 提供有关应用程序的信息。 然后 Azure AD 提供了一个客户端 ID (也称为应用程序 ID), 您可以使用该 ID 将应用程序与 Azure AD 运行时关联。 若要详细了解客户端 ID，请参阅 [Azure Active Directory 中的应用程序对象和服务主体对象](../active-directory/develop/app-objects-and-service-principals.md)。 

下面的图像显示了用于注册 web 应用程序的步骤:

![注册应用程序](./media/authenticate-application/app-registrations-register.png)

> [!Note]
> 如果将应用程序注册为本机应用程序, 则可以为重定向 URI 指定任何有效的 URI。 对于本机应用程序，此值不一定要是实际的 URL。 对于 Web 应用程序，重定向 URI 必须是有效的 URI，因为它指定了要向哪个 URL 提供令牌。

注册应用程序后, 你将在 "**设置**" 下看到**应用程序 (客户端) ID** :

![已注册应用程序的应用程序 ID](./media/authenticate-application/application-id.png)

有关向 Azure AD 注册应用程序的详细信息，请参阅[将应用程序与 Azure Active Directory](../active-directory/develop/quickstart-v2-register-an-app.md) 集成。


### <a name="create-a-client-secret"></a>创建客户端机密   
请求令牌时，应用程序需要使用客户端机密来证明其身份。 若要添加客户端密钥, 请执行以下步骤。

1. 在 Azure 门户中导航到你的应用注册。
1. 选择“证书和机密”设置。
1. 在 "**客户端密码**" 下, 选择 "**新建客户端密码**" 以创建新密钥。
1. 提供机密的说明, 并选择所需的过期时间间隔。
1. 请马上将新机密的值复制到安全位置。 填充值仅显示一次。

    ![客户端密码](./media/authenticate-application/client-secret.png)


### <a name="client-libraries-for-token-acquisition"></a>用于获取令牌的客户端库  
注册应用程序并向其授予了在 Azure 事件中心中发送/接收数据的权限后, 你可以向应用程序添加代码以对安全主体进行身份验证并获取 OAuth 2.0 令牌。 若要进行身份验证并获取令牌, 你可以使用其中一个[Microsoft 标识平台身份验证库](../active-directory/develop/reference-v2-libraries.md)或另一个支持 OpenID 或 connect 1.0 的开源库。 然后, 应用程序可以使用访问令牌对 Azure 事件中心的请求进行授权。

有关支持获取令牌的方案列表，请参阅[适用于 .NET 的 Microsoft 身份验证库 (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) GitHub 存储库的[方案](https://aka.ms/msal-net-scenarios)部分。


## <a name="next-steps"></a>后续步骤
- 若要了解有关 RBAC 的详细信息, 请参阅[什么是基于角色的访问控制 (RBAC)](../role-based-access-control/overview.md)？
- 若要了解如何使用 Azure PowerShell、Azure CLI 或 REST API 分配和管理 RBAC 角色分配，请参阅以下文章：
    - [使用 Azure PowerShell 管理基于角色的访问控制 (RBAC)](../role-based-access-control/role-assignments-powershell.md)  
    - [使用 Azure CLI 管理基于角色的访问控制 (RBAC)](../role-based-access-control/role-assignments-cli.md)
    - [使用 REST API 管理基于角色的访问控制 (RBAC)](../role-based-access-control/role-assignments-rest.md)
    - [使用 Azure 资源管理器模板管理基于角色的访问控制 (RBAC)](../role-based-access-control/role-assignments-template.md)

请参阅以下相关文章:
- [使用 Azure Active Directory 验证托管标识, 以访问事件中心资源](authenticate-managed-identity.md)
- [使用共享访问签名对 Azure 事件中心的请求进行身份验证](authenticate-shared-access-signature.md)
- [使用 Azure Active Directory 授权访问事件中心资源](authorize-access-azure-active-directory.md)
- [使用共享访问签名授予对事件中心资源的访问权限](authorize-access-shared-access-signature.md)

