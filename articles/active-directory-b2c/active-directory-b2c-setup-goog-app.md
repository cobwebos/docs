---
title: "Azure Active Directory B2C：Google+ 配置 | Microsoft Docs"
description: "在 Azure Active Directory B2C 保护的应用程序中向用户提供使用 Google+ 帐户的注册和登录功能。"
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: 4dcca66f-29e4-4b4d-8840-50baad736bd7
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: swkrish
translationtype: Human Translation
ms.sourcegitcommit: e37c48d6c92a8a2cd480458abdff0a3a1ca9338f
ms.openlocfilehash: dbc544ad4dbb651aeb24faf2f89074c7aa59cf2a
ms.lasthandoff: 05/05/2017


---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-google-accounts"></a>Azure Active Directory B2C：向用户提供使用 Google+ 帐户的注册和登录功能
## <a name="create-a-google-application"></a>创建 Google+ 应用程序
若要将 Google+ 用作 Azure Active Directory (Azure AD) B2C 中的标识提供者，需要创建 Google+ 应用程序并向其提供合适的参数。 需要使用 Google+ 帐户来完成此操作。 如果没有此帐户，可以在 [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp) 上获取。

1. 导航到 [Google 开发人员控制台](https://console.developers.google.com/)，然后使用 Google+ 帐户凭据登录。
2. 单击“创建项目”，输入**项目名称**，然后单击“创建”。
   
    ![Google+ - 入门](./media/active-directory-b2c-setup-goog-app/google-get-started.png)
   
    ![Google+ - 新建项目](./media/active-directory-b2c-setup-goog-app/google-new-project.png)
3. 单击“API 管理器”，然后在左侧导航区域中单击“凭据”。
4. 单击顶部的“OAuth 许可屏幕”选项卡。
   
    ![Google+ - 凭据](./media/active-directory-b2c-setup-goog-app/google-add-cred.png)
5. 选择或指定一个有效的**电子邮件地址**，提供**产品名称**，然后单击“保存”。
   
    ![Google+ - OAuth 许可屏幕](./media/active-directory-b2c-setup-goog-app/google-consent-screen.png)
6. 单击“新建凭据”，然后选择“OAuth 客户端 ID”。
   
    ![Google+ - OAuth 许可屏幕](./media/active-directory-b2c-setup-goog-app/google-add-oauth2-client-id.png)
7. 在“应用程序类型”下，选择“Web 应用程序”。
   
    ![Google+ - OAuth 许可屏幕](./media/active-directory-b2c-setup-goog-app/google-web-app.png)
8. 提供应用程序的**名称**，在“已授权 JavaScript 来源”字段中输入 `https://login.microsoftonline.com`，然后在“已授权重定向 URI”字段中输入 `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`。 将 **{tenant}** 替换为你的租户名称（例如 contosob2c.onmicrosoft.com）。 **{tenant}** 值区分大小写。 单击“创建” 。
   
    ![Google+ - 创建客户端 ID](./media/active-directory-b2c-setup-goog-app/google-create-client-id.png)
9. 复制“客户端 ID”和“客户端密码”的值。 将 Google+ 配置为租户中的标识提供者时需要这两个值。 **客户端密码**是一个重要的安全凭据。
   
    ![Google+ - 客户端密码](./media/active-directory-b2c-setup-goog-app/google-client-secret.png)

## <a name="configure-google-as-an-identity-provider-in-your-tenant"></a>将 Google+ 配置为租户中的标识提供者
1. 请按照以下步骤在 Azure 门户上[导航到 B2C 功能边栏选项卡](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)。
2. 在 B2C 功能边栏选项卡上，单击“标识提供者”。
3. 单击边栏选项卡顶部的“ **+添加** ”。
4. 为标识提供者配置提供一个友好**名称**。 例如，输入“G+”。
5. 单击“标识提供者类型”，选择“Google”，然后单击“确定”。
6. 单击“设置此标识提供者”，并输入之前创建的 Google+ 应用程序的客户端 ID 和客户端密码。
7. 单击“确定”，然后单击“创建”以保存 Google+ 配置。


