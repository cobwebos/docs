---
title: 为 Azure AD 组织设置登录
titleSuffix: Azure AD B2C
description: 在 Azure Active Directory B2C 中设置登录特定 Azure Active Directory 组织。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: b0ff1c2d913d0a4402b491f3c84ce0d35cd081df
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76847584"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中设置登录特定 Azure Active Directory 组织

若要将 Azure Active Directory (Azure AD) 用作 Azure AD B2C 中的[标识提供者](authorization-code-flow.md)，需要创建一个表示它的应用程序。 本文介绍如何使用 Azure AD B2C 中的用户流为特定 Azure AD 组织中的用户启用登录。

## <a name="create-an-azure-ad-app"></a>创建 Azure AD 应用

若要让用户从特定的 Azure AD 组织登录，需要在与 Azure AD B2C 租户不同的组织 Azure AD 租户中注册一个应用程序。

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 请确保使用的是包含 Azure AD 租户的目录。 在顶部菜单中选择 "**目录 + 订阅**" 筛选器，然后选择包含 Azure AD 租户的目录。 这与 Azure AD B2C 租户不是同一租户。
3. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“应用注册”。
4. 选择“新注册”。
5. 输入应用程序的名称。 例如，`Azure AD B2C App` 。
6. 仅对此应用程序接受**此组织目录中的帐户**选择。
7. 对于 "**重定向 URI**"，接受 " **Web**" 的值，并以所有小写字母输入以下 URL，其中 `your-B2C-tenant-name` 替换为 Azure AD B2C 租户的名称。 例如，`https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`：

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    现在，所有 URL 都应使用 [b2clogin.com](b2clogin.md)。

8. 单击“注册”。 复制以后要使用的**应用程序（客户端） ID** 。
9. 在 "应用程序" 菜单中选择 "**证书" & 机密**，然后选择 "**新建客户端密码**"。
10. 输入客户端机密的名称。 例如，`Azure AD B2C App Secret` 。
11. 选择过期期限。 对于此应用程序，接受选择 " **1 年**"。
12. 选择 "**添加**"，然后复制显示的新客户端密码的值，供稍后使用。

## <a name="configure-azure-ad-as-an-identity-provider"></a>将 Azure AD 配置为标识提供者

1. 请确保使用的是包含 Azure AD B2C 租户的目录。 在顶部菜单中选择 "**目录 + 订阅**" 筛选器，然后选择包含 Azure AD B2C 租户的目录。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C”。
1. 选择 "**标识提供者**"，然后选择 "**新建 OpenID connect 提供程序**"。
1. 输入“名称”。 例如，输入“Contoso Azure AD”。
1. 对于 "**元数据 url**"，请输入以下 url，将 `your-AD-tenant-domain` 替换为 Azure AD 租户的域名：

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

    例如，`https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration` 。

    **不要**使用 Azure AD v2.0 元数据终结点，例如 `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`。 这样做会导致在尝试登录时出现类似于 `AADB2C: A claim with id 'UserId' was not found, which is required by ClaimsTransformation 'CreateAlternativeSecurityId' with id 'CreateAlternativeSecurityId' in policy 'B2C_1_SignUpOrIn' of tenant 'contoso.onmicrosoft.com'` 的错误。

1. 对于 "**客户端 ID**"，请输入之前记录的应用程序 id。
1. 对于 "**客户端密码**"，请输入之前记录的客户端密码。
1. 保留 "**作用域**"、"**响应类型**" 和 "**响应" 模式**的默认值。
1. 可有可无输入**Domain_hint**的值。 例如， *ContosoAD*。 在请求中使用 domain_hint 引用此标识提供者时会用到该值。
1. 在 "**标识提供者声明映射**" 下，输入以下声明映射值：

    * **用户 ID**： *oid*
    * **显示名称**：*名称*
    * **给定名称**： *given_name*
    * **姓氏**： *family_name*
    * **电子邮件**： *unique_name*

1. 选择“保存”。
