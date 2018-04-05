---
title: Azure Active Directory B2C：Facebook 配置 | Microsoft Docs
description: 在 Azure Active Directory B2C 保护的应用程序中向用户提供使用 Facebook 帐户的注册和登录功能。
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 8/7/2017
ms.author: davidmu
ms.openlocfilehash: 899677500b0d33b5f98807a341449199b6b3dcac
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-facebook-accounts"></a>Azure Active Directory B2C：向用户提供使用 Facebook 帐户的注册和登录功能
## <a name="create-a-facebook-application"></a>创建 Facebook 应用程序
要将 Facebook 用作 Azure Active Directory (Azure AD) B2C 中的标识提供者，需要创建 Facebook 应用程序并向其提供合适的参数。 需要使用 Facebook 帐户来完成此操作。 如果没有账户，可在 [https://www.facebook.com/](https://www.facebook.com/) 处获取。

1. 转到 [Facebook 开发人员](https://developers.facebook.com/)网站，并使用 Facebook 帐户凭据登录。
2. 如果以前没有登录过，需要注册为 Facebook 开发人员。 要执行此操作，请单击“注册”（位于页面的右上角），接受 Facebook 的策略，并完成注册步骤。
3. 单击“我的应用”，并单击“添加新应用”。 
4. 在表单中，提供**显示名称**和有效的**联系人电子邮件**。
5. 单击“创建应用 ID”。 这会要求接受 Facebook 平台策略并完成在线安全检查。
6. 在左侧列中，单击“设置”，并选择“基本”（如果尚未选择）。
7. 选择一个**类别**。 
8. 单击“+ 添加平台”，并选择“网站”。
   
    ![Facebook — 设置](./media/active-directory-b2c-setup-fb-app/fb-settings.png)
   
    ![Facebook — 设置 — 网站](./media/active-directory-b2c-setup-fb-app/fb-website.png)
9. 在“站点 URL”字段中输入 `https://login.microsoftonline.com/`，然后单击页面底部的“保存更改”。
   
    ![Facebook — 站点 URL](./media/active-directory-b2c-setup-fb-app/fb-site-url.png)

10. 复制“应用程序 Id”的值。 单击“显示”，并复制“应用程序密码”的值。 将 Facebook 帐户配置为租户中的标识提供者时需要此两项。 **应用程序密码**是一个非常重要的安全凭据。
   
    ![Facebook — 应用程序 ID 和应用程序密码](./media/active-directory-b2c-setup-fb-app/fb-app-id-app-secret.png)
11. 单击左侧导航窗格中的“+ 添加产品”，然后单击“Facebook 登录”对应的“设置”按钮。
   
    ![Facebook — Facebook 登录](./media/active-directory-b2c-setup-fb-app/fb-login.png)
12. 在“Facebook 登录”下的右侧导航栏中单击“设置”

    ![Facebook - Facebook 登录设置](./media/active-directory-b2c-setup-fb-app/fb-login-settings.png)
13. 在“客户端 OAuth 设置”部分的“有效的 OAuth 重定向 URI”字段中输入 `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`。 将 **{tenant}** 替换为租户名称（例如 contosob2c.onmicrosoft.com）。 单击页面底部的“保存更改”。
    
    ![Facebook — OAuth 重定向 URI](./media/active-directory-b2c-setup-fb-app/fb-oauth-redirect-uri.png)
14. 若要使 Facebook 应用程序可供 Azure AD B2C 使用，需要使其公开可用。 要执行此操作，可单击左侧导航窗格上的“应用程序查看”，将该页顶部的开关设置为“是”，并单击“确认”。
    
    ![Facebook — 应用程序公开](./media/active-directory-b2c-setup-fb-app/fb-app-public.png)

## <a name="configure-facebook-as-an-identity-provider-in-your-tenant"></a>将 Facebook 配置为租户中的标识提供者
1. 请按照以下步骤在 Azure 门户上[导航到 B2C 功能边栏选项卡](active-directory-b2c-app-registration.md#navigate-to-b2c-settings)。
2. 在 B2C 功能边栏选项卡上，单击“标识提供者”。
3. 单击边栏选项卡顶部的“ **+添加** ”。
4. 为标识提供者配置提供一个友好**名称**。 例如，输入“Facebook”。
5. 单击“标识提供者类型”，选择“Facebook”，并单击“确定”。
6. 单击“设置此标识提供者”，分别在“客户端 ID”和“客户端密码”字段中输入（之前创建的 Facebook 应用程序的）应用程序 ID 和应用程序密码。
7. 单击“确定”，并单击“创建”以保存 Facebook 配置。

> [!NOTE]
> 向租户添加“标识提供者”不会修改现有策略。 请记住通过包括刚创建的标识提供者来更新策略。
>