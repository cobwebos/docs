---
title: 身份验证方法 |微软 Azure 地图
description: 在本文中，您将了解 Azure 活动目录 （Azure AD） 和共享密钥身份验证。 两者都用于 Microsoft Azure 映射服务。 了解如何获取 Azure 地图订阅密钥。
author: philmea
ms.author: philmea
ms.date: 01/28/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 21d29cba85adfc147ec9deb6ab362a5da943bf10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335703"
---
# <a name="authentication-with-azure-maps"></a>使用 Azure Maps 进行身份验证

Azure 映射支持两种对请求进行身份验证的方法：共享密钥身份验证和 Azure 活动目录身份验证。 本文介绍这些身份验证方法，以帮助指导 Azure 地图服务的实现。

> [!NOTE]
> 为了改进与 Azure 地图的安全通信，我们现在支持传输层安全 （TLS） 1.2，并且我们将停用对 TLS 1.0 和 1.1 的支持。 为避免任何服务中断，**请更新服务器和应用程序，在 2020 年 4 月 2 日之前使用 TLS 1.2**。  如果您当前使用 TLS 1.x，请评估 TLS 1.2 就绪性，并使用[解决 TLS 1.0 问题](https://docs.microsoft.com/security/solving-tls1-problem)中描述的测试制定迁移计划。

## <a name="shared-key-authentication"></a>共享密钥身份验证

 创建 Azure 地图帐户后生成主键和辅助键。 使用共享密钥身份验证调用 Azure 映射时，鼓励使用主密钥作为订阅密钥。 共享密钥身份验证将 Azure 映射帐户生成的密钥传递给 Azure 映射服务。 对于对 Azure 映射服务的每个请求，将*订阅密钥*作为参数添加到 URL。 辅助键可用于滚动键更改等方案。  

有关在 Azure 门户中查看密钥的信息，请参阅[管理身份验证](https://aka.ms/amauthdetails)。

> [!Tip]
> 我们建议定期重新生成密钥。 为您提供两个密钥，以便在重新生成另一个密钥时，可以使用一个密钥保持连接。 重新生成密钥时，需要使用新密钥更新访问帐户的任何应用程序。

## <a name="authentication-with-azure-active-directory-preview"></a>使用 Azure Active Directory 进行身份验证（预览）

Azure 地图现在使用[Azure 活动目录 （Azure AD）](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)为 Azure 地图服务提供请求身份验证。 Azure AD 提供基于标识的身份验证，包括[基于角色的访问控制 （RBAC）。](https://docs.microsoft.com/azure/role-based-access-control/overview) RBAC 用于授予对 Azure 地图资源的用户级、组级或应用程序级访问权限。 接下来的部分将讨论 Azure 映射与 Azure AD 集成的概念和组件。

## <a name="authentication-with-oauth-access-tokens"></a>使用 OAuth 访问令牌进行身份验证

对于与包含 Azure Maps 帐户的 Azure 订阅相关联的 Azure AD 租户，Azure Maps 接受 **OAuth 2.0** 访问令牌。 Azure 地图还接受用于：

* Azure AD 用户
* 使用用户委派的权限的合作伙伴应用程序
* Azure 资源的托管标识

Azure Maps 为每个 Azure Maps 帐户生成一个唯一的标识符（客户端 ID）。** 将此客户端 ID 与其他参数合并时，可以从 Azure AD 请求令牌。 要请求令牌，请根据 Azure 环境指定下表中的值。

| Azure 环境   | Azure AD 令牌终结点 |
| --------------------|-------------------------|
| Azure Public        | https://login.microsoftonline.com |
| Azure Government     | https://login.microsoftonline.us |


有关如何为 Azure Maps 配置 Azure AD 和请求令牌的详细信息，请参阅[在 Azure Maps 中管理身份验证](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)。

有关如何从 Azure AD 请求令牌的一般信息，请参阅[什么是身份验证？](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)。

## <a name="request-azure-map-resources-with-oauth-tokens"></a>使用 OAuth 令牌请求 Azure Maps 资源

Azure AD 收到令牌后，Azure 地图会发送具有以下一组所需请求标头的请求：

| 请求标头    |    “值”    |
|:------------------|:------------|
| x-ms-client-id    | 30d7cc….9f55|
| 授权     | Bearer eyJ0e….HNIVN |

> [!Note]
> `x-ms-client-id` 是 Azure Maps 身份验证页上显示的基于 Azure Maps 帐户的 GUID。

下面是使用 OAuth 令牌的 Azure Maps 路由请求示例：

```
GET /route/directions/json?api-version=1.0&query=52.50931,13.42936:52.50274,13.43872 
Host: atlas.microsoft.com 
x-ms-client-id: 30d7cc….9f55 
Authorization: Bearer eyJ0e….HNIVN 
```

有关如何查看客户端 ID 的信息，请参阅[查看身份验证详细信息](https://aka.ms/amauthdetails)。

## <a name="control-access-with-rbac"></a>使用 RBAC 控制访问

在 Azure AD 中，使用 RBAC 控制对安全资源的访问。 设置 Azure 地图帐户并注册 Azure 映射 Azure AD 租户。 Azure 地图支持通过 Azure 资源的托管标识对单个 Azure AD 用户、组、应用程序、Azure 资源和 Azure 服务进行读取访问控制。 在 Azure 地图门户页上，可以为所选角色设置 RBAC。

![Azure Maps 数据读取器(预览版)](./media/azure-maps-authentication/concept.png)

有关如何查看 RBAC 设置的信息，请参阅[如何为 Azure Maps 配置 RBAC](https://aka.ms/amrbac)。

## <a name="managed-identities-for-azure-resources-and-azure-maps"></a>Azure 资源和 Azure Maps 的托管标识

[Azure 资源的托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)为 Azure 服务提供自动托管标识，该标识可以授权访问 Azure 地图服务。 托管标识的一些示例包括：Azure 应用服务、Azure 函数和 Azure 虚拟机。

## <a name="next-steps"></a>后续步骤

* 若要详细了解如何使用 Azure AD 和 Azure Maps 对应用程序进行身份验证，请参阅[在 Azure Maps 中管理身份验证](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)。

* 若要详细了解如何对 Azure Maps、Map Control 和 Azure AD 进行身份验证，请参阅[使用 Azure Maps Map Control](https://aka.ms/amaadmc)。
