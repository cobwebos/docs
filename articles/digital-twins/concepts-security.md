---
title: Azure 数字孪生解决方案的安全性
titleSuffix: Azure Digital Twins
description: 了解 Azure 数字孪生的最佳安全方案。
author: baanders
ms.author: baanders
ms.date: 3/18/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 973eeebfdf9164cb50cf98ae8edc845a80a7e080
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2020
ms.locfileid: "88794512"
---
# <a name="secure-azure-digital-twins"></a>保护 Azure 数字孪生

为了安全起见，Azure 数字孪生支持对部署中特定数据、资源和操作的精确访问控制。 它通过称为 **基于角色的访问控制 (RBAC) **的粒度角色和权限管理策略来实现此功能。 可在 [此处](../role-based-access-control/overview.md)阅读有关适用于 AZURE 的 RBAC 的一般原则。

Azure 数字孪生还支持静态数据加密。

## <a name="granting-permissions-with-rbac"></a>通过 RBAC 授予权限

通过与 [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) 集成，可向 Azure 数字孪生提供 RBAC。

可以使用 RBAC 向 *安全主体*（可以是用户、组或应用程序服务主体）授予权限。 安全主体由 Azure AD 进行身份验证，并在返回时接收 OAuth 2.0 令牌。 此令牌可用于授权对 Azure 数字孪生实例的访问请求。

### <a name="authentication-and-authorization"></a>身份验证和授权

使用 Azure AD，访问过程分为两个步骤。 如果 (用户、组或应用程序的安全主体) 尝试访问 Azure 数字孪生，则必须对该请求进行 *身份验证* 和 *授权*。 

1. 首先，对安全主体的身份进行 *身份验证*，并返回 OAuth 2.0 令牌。
2. 接下来，令牌作为请求的一部分传递到 Azure 数字孪生服务，以 *授权* 访问指定的资源。

身份验证步骤要求在运行时将任何应用程序请求包含 OAuth 2.0 访问令牌。 如果应用程序在 Azure 实体（如 [Azure Functions](../azure-functions/functions-overview.md) 应用）内运行，则它可以使用 **托管标识** 来访问资源。 在下一部分中了解有关托管标识的详细信息。

授权步骤要求向安全主体分配 Azure 角色。 分配给安全主体的角色确定了该主体拥有的权限。 Azure 数字孪生提供 azure 角色，其中包含 Azure 数字孪生资源的权限集。 本文稍后将介绍这些角色。

若要了解有关 Azure 中支持的角色和角色分配的详细信息，请参阅了解 Azure RBAC 文档中 [*的不同角色*](../role-based-access-control/rbac-and-directory-admin-roles.md) 。

#### <a name="authentication-with-managed-identities"></a>使用托管标识进行身份验证

[Azure 资源的托管标识](../active-directory/managed-identities-azure-resources/overview.md) 是一项跨 Azure 功能，可用于创建与应用程序代码运行的部署关联的安全标识。 然后，你可以将该标识与访问控制角色相关联，以授予用于访问应用程序所需的特定 Azure 资源的自定义权限。

借助托管标识，Azure 平台可管理此运行时标识。 对于标识本身和需要访问的资源，都不需要在应用程序代码或配置中存储和保护访问密钥。 在 Azure App Service 应用程序中运行的 Azure 数字孪生客户端应用程序不需要处理 SAS 规则和密钥，也不需要处理任何其他访问令牌。 客户端应用只需要 Azure 数字孪生命名空间的终结点地址。 当应用进行连接时，Azure 数字孪生会将托管实体的上下文绑定到客户端。 一旦将其与托管标识相关联，Azure 数字孪生客户端就可以执行所有授权的操作。 然后，将通过将托管实体与 Azure 数字孪生 Azure 角色关联来授予授权 (如下) 所述。

#### <a name="authorization-azure-roles-for-azure-digital-twins"></a>授权： azure 数字孪生的 Azure 角色

Azure 提供以下 Azure 内置角色，用于授权访问 Azure 数字孪生资源：
* *Azure 数字孪生所有者 (预览版) * –使用此角色授予对 Azure 数字孪生资源的完全访问权限。
* *Azure 数字孪生读者 (预览版) * –使用此角色授予对 Azure 数字孪生资源的只读访问权限。

> [!TIP]
> *Azure 数字孪生 Reader (预览版) *角色现在还支持浏览关系。

有关如何定义内置角色的详细信息，请参阅了解 Azure RBAC 文档中的 [*角色定义*](../role-based-access-control/role-definitions.md) 。 有关创建 Azure 自定义角色的详细信息，请参阅 [*azure 自定义角色*](../role-based-access-control/custom-roles.md)。

可以通过两种方式分配角色：
* 通过访问控制 (IAM) 窗格中的 Azure 数字孪生在 Azure 门户中 (参阅 [*使用 AZURE RBAC 和 Azure 门户添加或删除角色分配*](../role-based-access-control/role-assignments-portal.md)) 
* 通过 CLI 命令添加或删除角色

有关如何执行此操作的更多详细步骤，请在 Azure 数字孪生教程中试用 [*：连接端到端解决方案*](tutorial-end-to-end.md)。

### <a name="permission-scopes"></a>权限范围

向安全主体分配 Azure 角色之前，请确定安全主体应具有的访问权限的范围。 最佳做法规定最好只授予最小的可能范围。

以下列表描述了可对 Azure 数字孪生资源的访问进行范围的级别。
* 模型：对此资源的操作规定对 Azure 数字孪生中上载的 [模型](concepts-models.md) 的控制。
* Query 数字孪生 Graph：此资源的操作决定了在 Azure 数字孪生 Graph 内对数字孪生运行 [查询操作](concepts-query-language.md) 的能力。
* 数字超源：此资源的操作可在 "超过程" 图中对 [数字孪生](concepts-twins-graph.md) 上的 CRUD 操作提供控制。
* 数字超并行关系：此资源的操作定义对孪生中数字之间的 [关系](concepts-twins-graph.md) 的 CRUD 操作的控制。
* 事件路由：此资源的操作确定将 [事件](concepts-route-events.md) 从 Azure 数字孪生路由到终结点服务（如 [事件中心](../event-hubs/event-hubs-about.md)、 [事件网格](../event-grid/overview.md)或 [服务总线](../service-bus-messaging/service-bus-messaging-overview.md)）的权限。

### <a name="troubleshooting-permissions"></a>疑难解答权限

如果用户尝试执行其角色不允许的操作，则可能会收到来自服务请求读取的错误 `403 (Forbidden)` 。 有关详细信息和疑难解答步骤，请参阅 [*故障排除： Azure 数字孪生请求失败，状态为： 403 (禁止访问) *](troubleshoot-error-403.md)。

## <a name="encryption-of-data-at-rest"></a>静态数据加密

Azure 数字孪生提供静态数据和传输中的数据加密，因为它是在我们的数据中心编写的，在你访问数据中心时，会对其进行解密。

## <a name="next-steps"></a>后续步骤

* 请参阅操作 [*方法：设置实例和身份验证*](how-to-set-up-instance-scripted.md)中的操作中的这些概念。

* 请参阅如何 [*：编写应用身份验证代码*](how-to-authenticate-client.md)中的客户端应用程序代码中的这些概念的交互。

* 阅读有关 [适用于 Azure 的 RBAC 的](../role-based-access-control/overview.md)详细信息。
