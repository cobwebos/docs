---
title: 身份验证方法
titleSuffix: Azure Maps
description: 本文介绍 Azure Active Directory 和共享密钥身份验证。 两者均用于 Microsoft Azure Maps 服务。 了解如何获取 Azure Maps 订阅密钥。
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: e270a9a1a773442a4d1d28d9bbfe37639cac7c65
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2020
ms.locfileid: "87133378"
---
# <a name="authentication-with-azure-maps"></a>使用 Azure Maps 进行身份验证

Azure Maps 支持通过两种方式对请求进行身份验证：共享密钥身份验证和[Azure Active Directory （Azure AD）](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)身份验证。 本文介绍了这些身份验证方法，这些方法有助于指导你实现 Azure Maps 服务。

> [!NOTE]
> 为了改善与 Azure Maps 的安全通信，我们现在支持传输层安全（TLS）1.2，并且我们正在停用对 TLS 1.0 和1.1 的支持。 若要避免任何服务中断，请将**服务器和应用程序更新为使用1.2 年4月 2 2020 日之前的 TLS**。  如果你当前使用 TLS 1.x，请评估你的 TLS 1.2 准备情况，并使用[解决 TLS 1.0 问题](https://docs.microsoft.com/security/solving-tls1-problem)中所述的测试制定迁移计划。

## <a name="shared-key-authentication"></a>共享密钥身份验证

 创建 Azure Maps 帐户后，将生成主密钥和辅助密钥。 建议你在使用共享密钥身份验证调用 Azure Maps 时使用主密钥作为订阅密钥。 共享密钥身份验证将 Azure Maps 帐户生成的密钥传递到 Azure Maps 服务。 对于每个 Azure Maps 服务请求，请将*订阅密钥*作为参数添加到 URL。 辅助密钥可用于应用滚动密钥更改等方案。  

有关在 Azure 门户中查看密钥的信息，请参阅[管理身份验证](https://aka.ms/amauthdetails)。

> [!Tip]
> 我们建议定期重新生成密钥。 提供了两个密钥，因此可以在重新生成其他密钥的同时保持与一个密钥的连接。 重新生成密钥时，需要使用新密钥更新访问你的帐户的所有应用程序。

## <a name="azure-ad-authentication"></a>Azure AD 身份验证

提供 Azure 订阅与 Azure AD 租户一起实现精细的访问控制。 Azure Maps 使用 Azure AD 提供 Azure Maps 服务的身份验证。 Azure AD 为在 Azure AD 租户中注册的用户和应用程序提供基于身份的身份验证。

对于与包含 Azure Maps 帐户的 Azure 订阅相关联的 Azure AD 租户，Azure Maps 接受 **OAuth 2.0** 访问令牌。 Azure Maps 还接受的令牌：

* Azure AD 用户
* 使用用户委派的权限的合作伙伴应用程序
* Azure 资源的托管标识

Azure Maps 为每个 Azure Maps 帐户生成一个唯一的标识符（客户端 ID）。** 当你将此客户端 ID 与其他参数组合时，你可以从 Azure AD 请求令牌。

有关如何为 Azure Maps 配置 Azure AD 和请求令牌的详细信息，请参阅[在 Azure Maps 中管理身份验证](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)。

有关在 Azure AD 中进行身份验证的常规信息，请参阅[什么是身份验证？](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)。

### <a name="managed-identities-for-azure-resources-and-azure-maps"></a>Azure 资源和 Azure Maps 的托管标识

[Azure 资源的托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)为 azure 服务提供了一个自动托管的基于应用程序的安全主体，该主体可使用 Azure AD 进行身份验证。 使用基于角色的访问控制（RBAC），可以授权托管标识安全主体访问 Azure Maps 服务。 托管标识的一些示例包括： Azure App Service、Azure Functions 和 Azure 虚拟机。 有关托管标识的列表，请参阅[Azure 资源的托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities)。

### <a name="configuring-application-azure-ad-authentication"></a>配置应用程序 Azure AD 身份验证

应用程序将使用 Azure AD 提供的一个或多个受支持方案，通过 Azure AD 租户进行身份验证。 每个 Azure AD 应用程序方案根据业务需求表示不同的要求。 某些应用程序可能需要用户登录体验，而其他应用程序可能需要应用程序登录体验。 有关详细信息，请参阅[身份验证流和应用程序方案](https://docs.microsoft.com/azure/active-directory/develop/authentication-flows-app-scenarios)。

在应用程序收到访问令牌后，SDK 和/或应用程序将使用以下一组所需的 HTTP 标头以及其他 REST API HTTP 标头发送 HTTPS 请求：

| 标头名称    | 值               |
| :------------- | :------------------ |
| x-ms-client-id | 30d7cc….9f55        |
| 授权  | Bearer eyJ0e….HNIVN |

> [!Note]
> `x-ms-client-id` 是 Azure Maps 身份验证页上显示的基于 Azure Maps 帐户的 GUID。

下面是使用 Azure AD OAuth 持有者令牌的 Azure Maps 路由请求的示例：

```http
GET /route/directions/json?api-version=1.0&query=52.50931,13.42936:52.50274,13.43872
Host: atlas.microsoft.com
x-ms-client-id: 30d7cc….9f55
Authorization: Bearer eyJ0e….HNIVN
```

有关如何查看客户端 ID 的信息，请参阅[查看身份验证详细信息](https://aka.ms/amauthdetails)。

## <a name="authorization-with-role-based-access-control"></a>使用基于角色的访问控制进行授权

Azure Maps 支持访问 Azure[基于角色的访问控制](https://docs.microsoft.com/azure/role-based-access-control/overview)的所有主要类型，包括：个体 Azure AD 用户、组、应用程序、azure 资源和 azure 托管标识。 向主体类型授予一组权限（也称为角色定义）。 角色定义提供 REST API 操作的权限。 将访问权限应用到一个或多个 Azure Maps 帐户称为 "作用域"。 应用主体、角色定义和作用域时，将创建角色分配。 

以下各节讨论 Azure Maps 与 Azure AD 基于角色的访问控制集成的概念和组件。 作为 Azure Maps 帐户设置过程的一部分，Azure AD 目录关联到 Azure Maps 帐户所在的 Azure 订阅。 

配置 Azure RBAC 时，选择安全主体并将其应用于角色分配。 若要了解如何将角色分配添加到 Azure 门户，请参阅[添加或删除 Azure 角色分配](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)。

### <a name="picking-a-role-definition"></a>选取角色定义

存在下列角色定义类型以支持应用程序方案。

| Azure 角色定义       | 描述                                                                                              |
| :-------------------------- | :------------------------------------------------------------------------------------------------------- |
| Azure Maps 数据读取器      | 提供对不可变 Azure Maps REST Api 的访问。                                                       |
| Azure Maps 数据参与者 | 提供对可变 Azure Maps REST Api 的访问。 可变性是由 "操作：写入" 和 "删除" 定义的。 |
| 自定义角色定义      | 创建精心设计的角色，以启用对 Azure Maps REST Api 的灵活限制的访问。                      |

某些 Azure Maps 服务可能需要提升的权限才能在 Azure Maps REST Api 上执行写入或删除操作。 Azure Maps 数据参与者角色是提供写入或删除操作的服务所必需的。 下表描述了在给定服务上使用写入或删除操作时，Azure Maps 数据参与者适用的服务。 如果服务上只使用了读取操作，则可以使用 Azure Maps 数据读取器，而不是 Azure Maps 数据参与者。

| Azure Maps 服务 | Azure Maps 角色定义  |
| :----------------- | :-------------------------- |
| 数据               | Azure Maps 数据参与者 |
| 创建者            | Azure Maps 数据参与者 |
| 空间            | Azure Maps 数据参与者 |

有关如何查看 RBAC 设置的信息，请参阅[如何为 Azure Maps 配置 RBAC](https://aka.ms/amrbac)。

#### <a name="custom-role-definitions"></a>自定义角色定义

应用程序安全的一个方面是应用最小特权原则。 原则是，安全主体只允许使用所需的访问权限，而无需其他访问权限。 创建自定义角色定义可以支持需要进一步细化访问控制的用例。 若要创建自定义角色定义，您可以选择要包含或排除定义的特定数据操作。 

然后，可以在任何安全主体的角色分配中使用该自定义角色定义。 若要了解有关 Azure 自定义角色定义的详细信息，请参阅[azure 自定义角色](https://docs.microsoft.com/azure/role-based-access-control/custom-roles)。

下面是一些示例方案，其中的自定义角色可以提高应用程序的安全性。

| 方案                                                                                                                                                                                                                 | 自定义角色数据操作                                                                                                                  |
| :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------ |
| 使用基本地图磁贴的公共或交互式登录网页，无其他 REST Api。                                                                                                                              | `Microsoft.Maps/accounts/services/render/read`                                                                                              |
| 只需要反向地理编码和其他 REST Api 的应用程序。                                                                                                                                             | `Microsoft.Maps/accounts/services/search/read`                                                                                              |
| 安全主体的一个角色，它请求读取基于 Azure Maps 创建者的映射数据和基本地图磁贴 REST Api。                                                                                                 | `Microsoft.Maps/accounts/services/data/read`, `Microsoft.Maps/accounts/services/render/read`                                                |
| 安全主体的角色，需要读取、写入和删除基于创建者的映射数据。 这可以定义为地图数据编辑器角色，但不允许访问其他 REST Api，如基本地图图块。 | `Microsoft.Maps/accounts/services/data/read`, `Microsoft.Maps/accounts/services/data/write`, `Microsoft.Maps/accounts/services/data/delete` |

### <a name="understanding-scope"></a>了解作用域

创建角色分配时，将在 Azure 资源层次结构中定义它。 层次结构的顶部是[管理组](https://docs.microsoft.com/azure/governance/management-groups/overview)，最低级别是 Azure 资源，如 Azure Maps 帐户。
将角色分配分配给资源组可允许访问多个 Azure Maps 帐户或组中的资源。

> [!Tip]
> Microsoft 的常规建议是将访问权限分配给 Azure Maps 帐户范围，因为它可防止对同一 Azure 订阅中现有的**其他 Azure Maps 帐户进行意外的访问**。

## <a name="next-steps"></a>后续步骤

* 若要了解有关 RBAC 的详细信息，请参阅[基于角色的访问控制概述](https://docs.microsoft.com/azure/role-based-access-control/overview)

* 若要详细了解如何使用 Azure AD 和 Azure Maps 对应用程序进行身份验证，请参阅[在 Azure Maps 中管理身份验证](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)。

* 若要详细了解如何对 Azure Maps、Map Control 和 Azure AD 进行身份验证，请参阅[使用 Azure Maps Map Control](https://aka.ms/amaadmc)。
