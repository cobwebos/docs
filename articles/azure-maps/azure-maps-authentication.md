---
title: Azure Maps 身份验证 | Microsoft Docs
description: 使用 Azure Maps 服务时进行身份验证。
author: walsehgal
ms.author: v-musehg
ms.date: 02/12/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: b97c303433eb8fadcda51257d37447f052ce4a3b
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2019
ms.locfileid: "56118785"
---
# <a name="authentication-with-azure-maps"></a>使用 Azure Maps 进行身份验证

Azure Maps 支持通过两种方式对请求进行身份验证。 共享密钥或 Azure Active Directory (Azure AD) 提供不同的方法用于授权发送到 Azure Maps 的每个请求。 本文旨在解释这两种身份验证方法，以引导你实现身份验证。

## <a name="shared-key-authentication"></a>共享密钥身份验证

共享密钥身份验证依赖于将 Azure Maps 帐户生成的密钥连同每个请求一起传递到 Azure Maps。  创建 Azure Maps 帐户时会生成两个密钥。  发送到 Azure Maps 服务的每个请求要求在 URL 中添加订阅密钥作为参数。

> [!Tip]
> 我们建议定期重新生成密钥。 你有两个密钥，因此，在重新生成其中的一个密钥时，可以使用另一个密钥来保持连接。 重新生成密钥时，必须将访问此帐户的所有应用程序更新为使用新密钥。

若要查看密钥，请参阅[身份验证详细信息](https://aka.ms/amauthdetails)。

## <a name="authentication-with-azure-active-directory-preview"></a>使用 Azure Active Directory 进行身份验证（预览）

Azure Maps 现在提供 [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) 集成用于对 Azure Maps 服务的请求进行身份验证。  Azure AD 提供基于标识的身份验证（包括[基于角色的访问控制 (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview)），以授予对 Azure Maps 资源的用户级或应用程序级访问权限。 本文旨在帮助你了解 Azure Maps Azure AD 集成的概念和组件。

## <a name="authentication-with-oauth-access-tokens"></a>使用 OAuth 访问令牌进行身份验证

对于与包含 Azure Maps 帐户的 Azure 订阅相关联的 Azure AD 租户，Azure Maps 接受 **OAuth 2.0** 访问令牌。  Azure Maps 接受以下对象的令牌：

* Azure AD 用户 
* 使用用户委托权限的第三方应用程序
* Azure 资源的托管标识

Azure Maps 为每个 Azure Maps 帐户生成一个 `unique identifier (client ID)`。  将客户端 ID 与其他参数结合使用时，可以通过指定以下值从 Azure AD 请求令牌：

```
https://login.microsoftonline.com
```
有关如何为 Azure Maps 配置 Azure AD 和请求令牌的详细信息，请参阅[如何管理身份验证](https://review.docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)。

有关从 Azure AD 请求令牌的一般信息，请参阅 [Azure Active Directory 中的身份验证基础知识](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)。

## <a name="requesting-azure-map-resources-with-oauth-tokens"></a>使用 OAuth 令牌请求 Azure Maps 资源

从 Azure AD 获取令牌后，可以使用以下两个必需的请求标头集将请求发送到 Azure Maps：

| 请求标头    |    值    |
|:------------------|:------------|
| x-ms-client-id    | 30d7cc….9f55|
| 授权     | Bearer eyJ0e….HNIVN |

> [!Note]
> `x-ms-client-id` 是基于 Azure Maps 身份验证页上显示的 GUID 的 Azure Maps 帐户

下面是使用 OAuth 令牌的 Azure Maps 路由请求示例：

```
GET /route/directions/json?api-version=1.0&query=52.50931,13.42936:52.50274,13.43872 
Host: atlas.microsoft.com 
x-ms-client-id: 30d7cc….9f55 
Authorization: Bearer eyJ0e….HNIVN 
```

若要查看客户端 ID，请参阅[身份验证详细信息](https://aka.ms/amauthdetails)。

## <a name="control-access-with-role-based-access-control-rbac"></a>使用基于角色的访问控制 (RBAC) 来控制访问

Azure AD 的一项关键功能是通过 RBAC 控制对受保护资源的访问。 创建 Azure Maps 帐户并在 Azure AD 租户中注册 Azure Maps Azure AD 应用程序后，可在 Azure Maps 帐户门户页中针对用户、应用程序或 Azure 资源配置 RBAC。 

Azure Maps 目前支持通过 Azure 资源的托管标识针对单个 Azure AD 用户、应用程序或 Azure 服务进行读取访问控制。

![概念](./media/azure-maps-authentication/concept.png)

若要查看 RBAC 设置，请参阅[如何为 Azure Maps 配置 RBAC](https://aka.ms/amrbac)。

## <a name="managed-identities-for-azure-resources-and-azure-maps"></a>Azure 资源和 Azure Maps 的托管标识

[Azure 资源的托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)为 Azure 服务（Azure 应用服务、Azure Functions、虚拟机等）提供一个自动管理的标识，可以授权该标识访问 Azure Maps 服务。  

## <a name="next-steps"></a>后续步骤

* 若要详细了解如何使用 Azure AD 和 Azure Maps 对应用程序进行身份验证，请参阅[如何管理身份验证](https://review.docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)。

* 若要详细了解如何对 Azure Maps、Map Control 和 Azure AD 进行身份验证，请参阅 [Azure AD 和 Azure Maps Map Control](https://aka.ms/amaadmc)。