---
title: 添加 Web API 应用程序 - Azure Active Directory B2C | Microsoft Docs
description: 了解如何向 Active Directory B2C 租户添加 Web API 应用程序。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 04/16/2019
ms.custom: mvc
ms.topic: how-to
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 438d2fec9efc6a1ad3fcdaa2829573e7205820ff
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85385954"
---
# <a name="add-a-web-api-application-to-your-azure-active-directory-b2c-tenant"></a>向 Azure Active Directory B2C 租户添加 Web API 应用程序

 在租户中注册 Web API 资源，以便他们可以接受并响应提供访问令牌的客户端应用程序的请求。 本文介绍如何在 Azure Active Directory B2C (Azure AD B2C) 中注册 Web API。

要在 Azure AD B2C 租户中注册应用程序，可以使用新的统一“应用注册”体验或旧版“应用程序(旧版)”体验 。 [详细了解此新体验](https://aka.ms/b2cappregtraining)。

#### <a name="app-registrations"></a>[应用注册](#tab/app-reg-ga/)

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在顶部菜单中选择“目录 + 订阅”筛选器，然后选择包含Azure AD B2C 租户的目录。
1. 在左侧菜单中，选择“Azure AD B2C”。 或者，选择“所有服务”并搜索并选择“Azure AD B2C”。
1. 选择“应用注册”，然后选择“新建注册” 。
1. 输入应用程序的“名称”。 例如，“webapi1”。
1. 在“重定向 URI”下选择“Web”，然后输入 Azure AD B2C 会将应用程序请求的任何令牌返回到其中的终结点。 在生产应用程序中，可以将重定向 URI 设置为类似的终结点 `https://localhost:5000` 。 在开发或测试过程中，你可以将其设置为 `https://jwt.ms` ，这是一个 Microsoft 拥有的 web 应用程序，它显示令牌的已解码内容（该令牌的内容绝不会离开你的浏览器）。 随时可以添加和修改已注册应用程序中的重定向 URI。
1. 选择“注册”。
1. 记录**应用程序（客户端） ID**以在 web API 的代码中使用。

如果你有一个实现隐式授权流的应用程序（例如基于 JavaScript 的单页应用程序（SPA）），则可以通过执行以下步骤来启用此流：

1. 在“管理”下，选择“身份验证”。 
1. 在“隐式授权”下，选中“访问令牌”和“ID 令牌”复选框  。
1. 选择“保存”。

#### <a name="applications-legacy"></a>[应用程序(旧版)](#tab/applications-legacy/)

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 请确保使用的是包含 Azure AD B2C 租户的目录。 选择顶部菜单中的“目录 + 订阅”筛选器，然后选择包含租户的目录。
3. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C” 。
4. 选择“应用程序(旧版)”，然后选择“添加” 。
5. 输入应用程序的名称。 例如，“webapi1”。
6. 对于“包括 Web 应用/Web API”和“允许隐式流”，请选择“是”。  
7. 对于“回复 URL”，请输入 Azure AD B2C 要将应用程序请求的任何令牌返回到的终结点。 在生产应用程序中，可以将回复 URL 设置为 `https://localhost:44332` 之类的值。 出于测试目的，将回复 URL 设置为 `https://jwt.ms`。
8. 对于“应用 ID URI”，请输入 Web API 使用的标识符。 包括域在内的完整标识符 URI 是为你生成的。 例如，`https://contosotenant.onmicrosoft.com/api`。
9. 单击“创建”。
10. 在属性页上，记录在配置 Web 应用程序时要使用的应用程序 ID。

* * *

## <a name="configure-scopes"></a>配置范围

可通过范围控制对受保护资源的访问。 Web API 使用作用域实施基于作用域的访问控制。 例如，可以让 Web API 用户拥有读取和写入访问权限，或者只拥有读取访问权限。 在本教程中，请使用作用域为 Web API 定义读取和写入权限。

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

## <a name="grant-permissions"></a>授予权限

若要从应用程序调用受保护的 Web API，需授予应用程序访问该 API 的权限。 例如，在[教程：在 Azure Active Directory B2C 中注册应用程序](tutorial-register-applications.md)时，Azure AD B2C 中注册了一个名为*webapp1*的 web 应用程序。 可使用此应用程序调用 Web API。

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

注册应用程序以调用受保护的 Web API。 用户通过 Azure AD B2C 进行身份验证，以便使用该应用程序。 应用程序从 Azure AD B2C 获取授权，以访问受保护的 Web API。
