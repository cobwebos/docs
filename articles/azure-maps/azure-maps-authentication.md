---
title: 身份验证方法 |Microsoft Azure 映射
description: 在本文中，你将了解 Azure Active Directory （Azure AD）和共享密钥身份验证。 两者均用于 Microsoft Azure Maps 服务。 了解如何获取 Azure Maps 订阅密钥。
author: farah-alyasari
ms.author: v-faalya
ms.date: 01/28/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: ee8b166077c64ae5e0f2cce18ee0bc77e8c996f4
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210117"
---
# <a name="authentication-with-azure-maps"></a>使用 Azure Maps 进行身份验证

Azure Maps 支持通过两种方式对请求进行身份验证：共享密钥身份验证和 Azure Active Directory 身份验证。 本文介绍了这些身份验证方法，这些方法有助于指导你实现 Azure Maps 服务。

## <a name="shared-key-authentication"></a>共享密钥身份验证

 创建 Azure Maps 帐户后，将生成主密钥和辅助密钥。 建议你在使用共享密钥身份验证调用 Azure Maps 时使用主密钥作为订阅密钥。 共享密钥身份验证将 Azure Maps 帐户生成的密钥传递到 Azure Maps 服务。 对于每个 Azure Maps 服务请求，请将*订阅密钥*作为参数添加到 URL。 辅助密钥可用于应用滚动密钥更改等方案。  

有关在 Azure 门户中查看密钥的信息，请参阅[管理身份验证](https://aka.ms/amauthdetails)。

> [!Tip]
> 我们建议定期重新生成密钥。 提供了两个密钥，因此可以在重新生成其他密钥的同时保持与一个密钥的连接。 重新生成密钥时，需要使用新密钥更新访问你的帐户的所有应用程序。

## <a name="authentication-with-azure-active-directory-preview"></a>使用 Azure Active Directory 进行身份验证（预览）

Azure Maps 现在为使用[Azure Active Directory （Azure AD）](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)的 Azure Maps 服务提供请求身份验证。 Azure AD 提供基于身份的身份验证，包括[基于角色的访问控制（RBAC）](https://docs.microsoft.com/azure/role-based-access-control/overview)。 RBAC 用于授予对 Azure Maps 资源的用户级别、组级别或应用程序级别的访问权限。 以下部分讨论与 Azure AD Azure Maps 集成的概念和组件。

## <a name="authentication-with-oauth-access-tokens"></a>使用 OAuth 访问令牌进行身份验证

对于与包含 Azure Maps 帐户的 Azure 订阅相关联的 Azure AD 租户，Azure Maps 接受 **OAuth 2.0** 访问令牌。 Azure Maps 还接受的令牌：

* Azure AD 用户
* 使用用户委派的权限的合作伙伴应用程序
* Azure 资源的托管标识

Azure Maps 为每个 Azure Maps 帐户生成一个唯一的标识符（客户端 ID）。 当你将此客户端 ID 与其他参数组合时，你可以从 Azure AD 请求令牌。 要请求令牌，请根据 Azure 环境指定下表中的值。

| Azure 环境   | Azure AD 令牌终结点 |
| --------------------|-------------------------|
| Azure Public        | https://login.microsoftonline.com |
| Azure Government    | https://login.microsoftonline.us |


有关如何为 Azure Maps 配置 Azure AD 和请求令牌的详细信息，请参阅[在 Azure Maps 中管理身份验证](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)。

有关如何从 Azure AD 请求令牌的一般信息，请参阅[什么是身份验证？](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)。

## <a name="request-azure-map-resources-with-oauth-tokens"></a>使用 OAuth 令牌请求 Azure Maps 资源

Azure AD 收到令牌后，Azure Maps 使用以下一组所需的请求标头发送请求：

| 请求标头    |    值    |
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

在 Azure AD 中，使用 RBAC 控制对受保护资源的访问权限。 设置 Azure Maps 帐户，并向 Azure AD 租户注册 Azure Maps。 Azure Maps 通过 Azure 资源的托管标识，支持单个 Azure AD 用户、组、应用程序、Azure 资源和 Azure 服务的读取访问控制。 在 Azure Maps 门户 "页上，可以为所选角色设置 RBAC。

![Azure Maps 数据读取器(预览版)](./media/azure-maps-authentication/concept.png)

有关如何查看 RBAC 设置的信息，请参阅[如何为 Azure Maps 配置 RBAC](https://aka.ms/amrbac)。

## <a name="managed-identities-for-azure-resources-and-azure-maps"></a>Azure 资源和 Azure Maps 的托管标识

[Azure 资源的托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)为 azure 服务提供自动管理的标识，该标识可以获得访问 Azure Maps 服务的授权。 托管标识的一些示例包括： Azure App Service、Azure Functions 和 Azure 虚拟机。

## <a name="next-steps"></a>后续步骤

* 若要详细了解如何使用 Azure AD 和 Azure Maps 对应用程序进行身份验证，请参阅[在 Azure Maps 中管理身份验证](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)。

* 若要详细了解如何对 Azure Maps、Map Control 和 Azure AD 进行身份验证，请参阅[使用 Azure Maps Map Control](https://aka.ms/amaadmc)。
