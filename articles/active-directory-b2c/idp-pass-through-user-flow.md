---
title: 在 Azure Active Directory B2C 中使用用户流将访问令牌传递给应用程序 | Microsoft Docs
description: 了解如何在 Azure Active Directory B2C 中以用户流中声明的方式传递 OAuth2.0 标识提供者的访问令牌。
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 6e236144909fc45e0e9d49273d7aed1e3e577436
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2019
ms.locfileid: "55161335"
---
# <a name="pass-an-access-token-through-a-user-flow-to-your-application-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中使用用户流将访问令牌传递给应用程序

> [!NOTE]
> 此功能目前处于公开预览状态。

> [!Important]
> 此公共预览功能暂时不可用。

Azure Active Directory (Azure AD) B2C 中的[用户流](active-directory-b2c-reference-policies.md)允许应用程序的用户通过标识提供者进行注册或登录。 此过程开始时，Azure AD B2C 会从标识提供者处收到一个[访问令牌](active-directory-b2c-reference-tokens.md)。 Azure AD B2C 使用该令牌来检索有关用户的信息。 在用户流中启用声明即可将该令牌传递给你在 Azure AD B2C 中注册的应用程序。

Azure AD B2C 当前仅支持传递 [OAuth 2.0](active-directory-b2c-reference-oauth-code.md) 标识提供者（这包括 [Facebook](active-directory-b2c-setup-fb-app.md) 和 [Google](active-directory-b2c-setup-goog-app.md)）的访问令牌。 对于所有其他标识提供者，声明将返回空白。

## <a name="prerequisites"></a>先决条件

- 应用程序必须使用 [v2 用户流](user-flow-versions.md)。
- 用户流是使用 OAuth 2.0 标识提供者配置的。

## <a name="enable-the-claim"></a>启用声明

1. 以 Azure AD B2C 租户的全局管理员身份登录 [Azure 门户](https://portal.azure.com/)。
2. 请确保使用包含 Azure AD B2C 租户的目录，方法是单击顶部菜单中的“目录和订阅筛选器”，然后选择包含租户的目录。
3. 选择 Azure 门户左上角的“所有服务”，搜索并选择 **Azure AD B2C**。
4. 选择“用户流，然后选择你的用户流。 例如 **B2C_1_SignupSignIn**。
5. 选择“应用程序声明”。
6. 启用**标识提供者访问令牌**。

    ![应用程序声明](./media/idp-pass-through-user-flow/idp-pass-through-user-flow-app-claim.png)

7. 单击“保存”，保存用户流。

## <a name="test-the-user-flow"></a>测试用户流

在 Azure AD B2C 中测试应用程序时，可以使 Azure AD B2C 令牌返回到 `https://jwt.ms`，以便在其中查看声明，这可能很有用处。

1. 在用户流的“概览”页上，选择“运行用户流”。
2. 对于“应用程序”，选择你之前注册的应用程序。 “回复 URL”应当显示 `https://jwt.ms` 才能看到以下示例中的令牌。
3. 单击“运行用户流”，然后使用帐户凭据登录。 应该会在 **idp_access_token** 声明中看到标识提供者的访问令牌。

    应会看到类似于以下示例的内容：

    ![已解码的令牌](./media/idp-pass-through-user-flow/idp-pass-through-user-flow-token.png)

## <a name="next-steps"></a>后续步骤

在 [Azure Active Directory 令牌参考](active-directory-b2c-reference-tokens.md)中了解有关令牌的更多信息。




