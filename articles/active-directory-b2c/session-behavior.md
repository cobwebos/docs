---
title: 配置会话行为 - Azure Active Directory B2C | Microsoft Docs
description: 了解如何在 Azure Active Directory B2C 中配置会话行为。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/12/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 091704fabb7b50a0c83625c6ae46d9a807f01ffc
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91961028"
---
# <a name="configure-session-behavior-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中配置会话行为

使用 Azure Active Directory B2C (Azure AD B2C) 中的[单一登录 (SSO) 会话](session-overview.md)管理，管理员可在用户已通过身份验证之后控制与用户的交互。 例如，管理员可以控制是否显示所选的标识提供者，或是否需要再次输入帐户详细信息。 本文介绍如何配置 Azure AD B2C SSO 的设置。

## <a name="session-behavior-properties"></a>会话行为属性

可使用以下属性来管理 Web 应用程序会话：

- **Web 应用会话生存期（分钟）** - 身份验证成功后，存储在用户浏览器上的 Azure AD B2C 会话 Cookie 的生存期。
    - 默认值 = 1440 分钟。
    - 最小值（含）= 15 分钟。
    - 最大值（含）= 1440 分钟。
- **Web 应用会话超时** - [会话过期类型](session-overview.md#session-expiry-type)：“滚动”或“绝对” 。 
- **单一登录配置** - Azure AD B2C 租户中跨多个应用和用户流的单一登录 (SSO) 行为的[会话范围](session-overview.md#session-scope)。


## <a name="configure-the-properties"></a>配置属性

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 请确保使用包含 Azure AD B2C 租户的目录，方法是选择顶部菜单中的“目录 + 订阅”筛选器，然后选择包含 Azure AD B2C 租户的目录。
3. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C” 。
4. 选择“用户流”****。
5. 打开之前创建的用户流。
6. 选择“属性”。
7. 根据需要配置 **Web 应用会话生存期（分钟）** 、**Web 应用会话超时**、**单一登录配置**和**在注销请求中需要 ID 令牌**。

    ![Azure 门户中的会话行为属性设置](./media/session-behavior/session-behavior.png)

8. 单击“保存” 。

## <a name="configure-sign-out-behavior"></a>配置注销行为

### <a name="secure-your-logout-redirect"></a>保护注销重定向

注销后，用户将重定向到 `post_logout_redirect_uri` 参数中指定的 URI，而不管为应用程序指定的回复 URL 为何。 但是，如果传递的 `id_token_hint` 是有效的，并且启用了 **注销请求中的 "需要 ID" 令牌** ，则 Azure AD B2C 在执行重定向之前，验证的值是否 `post_logout_redirect_uri` 与某个应用程序的已配置重定向 uri 相匹配。 如果没有为应用程序配置匹配的回复 URL，则会显示一条错误消息，而用户不会重定向。 若要在注销请求中要求 ID 令牌：

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 请确保使用包含 Azure AD B2C 租户的目录，方法是选择顶部菜单中的“目录 + 订阅”筛选器，然后选择包含 Azure AD B2C 租户的目录。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C” 。
1. 选择“用户流”****。
1. 打开之前创建的用户流。
1. 选择“属性”。
1. **在注销请求中启用 "需要 ID" 令牌**。
1. 返回  **Azure AD B2C**。
1. 选择 " **应用注册**"，然后选择你的应用程序。
1. 选择“身份验证”。
1. 在 " **注销 URL** " 文本框中，键入注销后的重定向 URI，然后选择 " **保存**"。

## <a name="next-steps"></a>后续步骤

- 详细了解 [Azure AD B2C 会话](session-overview.md)。
