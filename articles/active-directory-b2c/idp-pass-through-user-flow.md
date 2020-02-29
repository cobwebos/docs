---
title: 通过用户流将访问令牌传递到你的应用
titleSuffix: Azure AD B2C
description: 了解如何在 Azure Active Directory B2C 中，以用户流中的声明的形式传递 OAuth 2.0 标识提供程序的访问令牌。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/17/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 312d093548b6e3cf3654f45d7610e8fc474a87b8
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78187773"
---
# <a name="pass-an-access-token-through-a-user-flow-to-your-application-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中使用用户流将访问令牌传递给应用程序

Azure Active Directory B2C （Azure AD B2C）中的[用户流](user-flow-overview.md)为应用程序的用户提供了使用标识提供者注册或登录的机会。 此过程开始时，Azure AD B2C 会从标识提供者处收到一个[访问令牌](tokens-overview.md)。 Azure AD B2C 使用该令牌来检索有关用户的信息。 在用户流中启用声明即可将该令牌传递给你在 Azure AD B2C 中注册的应用程序。

Azure AD B2C 当前仅支持传递 [OAuth 2.0](authorization-code-flow.md) 标识提供者（这包括 [Facebook](identity-provider-facebook.md) 和 [Google](identity-provider-google.md)）的访问令牌。 对于所有其他标识提供者，声明将返回空白。

## <a name="prerequisites"></a>必备条件

* 应用程序必须使用 [v2 用户流](user-flow-versions.md)。
* 用户流是使用 OAuth 2.0 标识提供者配置的。

## <a name="enable-the-claim"></a>启用声明

1. 以 Azure AD B2C 租户的全局管理员身份登录 [Azure 门户](https://portal.azure.com/)。
2. 请确保使用的是包含 Azure AD B2C 租户的目录。 在顶部菜单中选择 "**目录 + 订阅**" 筛选器，然后选择包含你的租户的目录。
3. 选择 Azure 门户左上角的“所有服务”，搜索并选择 **Azure AD B2C**。
4. 选择 "**用户流（策略）** "，然后选择用户流。 例如， **B2C_1_signupsignin1**。
5. 选择“应用程序声明”。
6. 启用**标识提供者访问令牌**声明。

    ![启用标识提供者访问令牌声明](./media/idp-pass-through-user-flow/idp-pass-through-user-flow-app-claim.png)

7. 单击“保存”，保存用户流。

## <a name="test-the-user-flow"></a>测试用户流

在 Azure AD B2C 中测试应用程序时，可以使 Azure AD B2C 令牌返回到 `https://jwt.ms`，以便在其中查看声明，这可能很有用处。

1. 在用户流的“概览”页上，选择“运行用户流”。
2. 对于“应用程序”，选择你之前注册的应用程序。 “回复 URL”应当显示 `https://jwt.ms` 才能看到以下示例中的令牌。
3. 单击“运行用户流”，然后使用帐户凭据登录。 应该会在 **idp_access_token** 声明中看到标识提供者的访问令牌。

    应会看到类似于以下示例的内容：

    ![突出显示了 idp_access_token 块的 jwt.ms 中已解码的令牌](./media/idp-pass-through-user-flow/idp-pass-through-user-flow-token.PNG)

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅[Azure AD B2C 令牌概述](tokens-overview.md)。
