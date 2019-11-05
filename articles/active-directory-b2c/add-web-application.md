---
title: 添加 web API 应用程序-Azure Active Directory B2C |Microsoft Docs
description: 了解如何将 web API 应用程序添加到 Active Directory B2C 租户。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 04/16/2019
ms.custom: mvc
ms.topic: conceptual
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 0fd6af26390778491a127ce1bd0a58846b87f721
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73474843"
---
# <a name="add-a-web-api-application-to-your-azure-active-directory-b2c-tenant"></a>向 Azure Active Directory B2C 租户添加 Web API 应用程序

 在租户中注册 Web API 资源，以便他们可以接受并响应提供访问令牌的客户端应用程序的请求。 本文介绍如何在 Azure Active Directory B2C （Azure AD B2C）中注册 web API。

若要在 Azure AD B2C 租户中注册应用程序，你可以使用当前**应用程序**体验或我们的新的统一**应用注册（预览版）** 体验。 [了解有关预览版体验的详细信息](https://aka.ms/b2cappregintro)。

#### <a name="applicationstabapplications"></a>[应用程序](#tab/applications/)

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 请确保使用的是包含 Azure AD B2C 租户的目录。 选择顶部菜单中的“目录 + 订阅”筛选器，然后选择包含租户的目录。
3. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C”。
4. 选择“应用程序”，然后选择“添加”。
5. 输入应用程序的名称。 例如，“webapi1”。
6. 对于“包括 Web 应用/Web API”和“允许隐式流”，请选择“是”。
7. 对于“回复 URL”，请输入 Azure AD B2C 要将应用程序请求的任何令牌返回到的终结点。 在生产应用程序中，可以将回复 URL 设置为 `https://localhost:44332` 之类的值。 出于测试目的，将回复 URL 设置为 `https://jwt.ms`。
8. 对于“应用 ID URI”，请输入 Web API 使用的标识符。 包括域在内的完整标识符 URI 是为你生成的。 例如，`https://contosotenant.onmicrosoft.com/api`。
9. 单击“创建”。
10. 在属性页上，记录在配置 Web 应用程序时要使用的应用程序 ID。

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[应用注册（预览）](#tab/app-reg-preview/)

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在顶部菜单中选择 "**目录 + 订阅**" 筛选器，然后选择包含 Azure AD B2C 租户的目录。
1. 在左侧菜单中，选择 " **Azure AD B2C**"。 或者选择 "**所有服务**"，搜索并选择 " **Azure AD B2C**"。
1. 选择 "**应用注册（预览版）** "，然后选择 "**新注册**"。
1. 输入应用程序的**名称**。 例如，“webapi1”。
1. 在 "**重定向 URI**" 下，选择 " **Web**"，然后输入终结点，Azure AD B2C 应返回应用程序请求的任何令牌。 在生产应用程序中，可以将 "重定向 URI" 设置为 `https://localhost:5000`的端点。 在开发或测试过程中，你可以将其设置为 `https://jwt.ms`，这是 Microsoft 拥有的 web 应用程序，用于显示令牌的已解码内容（令牌的内容绝不会离开你的浏览器）。 随时可以添加和修改已注册应用程序中的重定向 Uri。
1. 选择“注册”。
1. 记录**应用程序（客户端） ID**以在 web API 的代码中使用。

如果你有一个实现隐式授权流的应用程序（例如基于 JavaScript 的单页应用程序（SPA）），则可以通过执行以下步骤来启用此流：

1. 在 "**管理**" 下选择 "**身份验证**"。
1. 选择 **"试用新体验**（如果已显示）"。
1. 在 "**隐式授予**" 下，同时选择 "**访问令牌**" 和 " **ID 令牌**" 复选框。
1. 选择“保存”。

* * *

## <a name="configure-scopes"></a>配置范围

可通过范围控制对受保护资源的访问。 Web API 使用作用域实施基于作用域的访问控制。 例如，可以让 Web API 用户拥有读取和写入访问权限，或者只拥有读取访问权限。 在本教程中，请使用作用域为 Web API 定义读取和写入权限。

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

## <a name="grant-permissions"></a>授予权限

若要从应用程序调用受保护的 Web API，需授予应用程序访问该 API 的权限。 例如，在[教程：在 Azure Active Directory B2C 中注册应用程序](tutorial-register-applications.md)时，Azure AD B2C 中注册了一个名为*webapp1*的 web 应用程序。 可使用此应用程序调用 Web API。

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

注册应用程序以调用受保护的 Web API。 用户通过 Azure AD B2C 进行身份验证，以便使用该应用程序。 该应用程序从 Azure AD B2C 获取授权，以访问受保护的 Web API。
