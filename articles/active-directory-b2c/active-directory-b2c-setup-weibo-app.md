---
title: "Azure Active Directory B2C：Weibo 配置 | Microsoft Docs"
description: "在 Azure Active Directory B2C 保护的应用程序中向用户提供使用 Weibo 帐户的注册和登录功能。"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: 1860de34-94cb-4ceb-851e-102f930f7230
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/26/2017
ms.author: parakhj
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: b7283da1a3774b06d782fbac204b18cc074d413f
ms.lasthandoff: 03/29/2017


---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-weibo-accounts"></a>Azure Active Directory B2C：向用户提供使用 Weibo 帐户的注册和登录功能

> [!NOTE]
> 该功能仍以预览版提供。
> 

## <a name="create-a-weibo-application"></a>创建 Weibo 应用程序

若要将 Weibo 用作 Azure Active Directory (Azure AD) B2C 中的标识提供者，需要创建 Weibo 应用程序并向其提供合适的参数。 需要使用 Weibo 帐户来完成此操作。 如果没有帐户，可以在以下网址获取一个：[http://weibo.com/signup/signup.php?lang=zh-cn](http://weibo.com/signup/signup.php?lang=en-us)。

### <a name="register-for-the-weibo-developer-program"></a>注册 Weibo 开发人员计划

1. 转到 [Weibo 开发人员门户](http://open.weibo.com/)并使用 Weibo 帐户凭据登录。
2. 登录后，单击右上角的显示名称。
3. 在下拉列表中，选择“编辑开发者信息”(edit developer information)。
4. 在表单中输入所需的信息，然后单击“提交”(submit)。
5. 完成电子邮件验证过程。
6. 转到[“身份验证”页](http://open.weibo.com/developers/identity/edit)。
7. 在表单中输入所需的信息，然后单击“提交”(submit)。

### <a name="register-a-weibo-application"></a>注册 Weibo 应用程序

1. 转到[“新 Weibo 应用注册”页](http://open.weibo.com/apps/new)。
2. 输入所需的应用程序信息。
3. 单击“创建”(create)。
4. 复制**应用密钥**和**应用机密**的值。 稍后你将需要此项。
5. 上载所需的照片，并输入所需的信息。
6. 单击“保存以上信息”(save)。
7. 单击“高级信息”(advanced information)。
8. 单击 OAuth2.0“授权设置”(redirect URL) 字段旁边的“编辑”(edit)。
9. 为 OAuth2.0“授权设置”(redirect URL) 输入 `https://login.microsoftonline.com/te/{tenant_name}/oauth2/authresp`。 例如，如果 `tenant_name` 是 contoso.onmicrosoft.com，请将 URL 设置为 `https://login.microsoftonline.com/te/contoso.onmicrosoft.com/oauth2/authresp`。
10. 单击“提交”(submit)。  

## <a name="configure-weibo-as-an-identity-provider-in-your-tenant"></a>将 Weibo 配置为租户中的标识提供者
1. 请按照以下步骤在 Azure 门户上[导航到 B2C 功能边栏选项卡](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)。
2. 在 B2C 功能边栏选项卡上，单击“标识提供者”。
3. 单击边栏选项卡顶部的“ **+添加** ”。
4. 为标识提供者配置提供一个友好**名称**。 例如，输入“Weibo”。
5. 单击“标识提供者类型”，选择“Weibo”，然后单击“确定”。
6. 单击“设置此标识提供者”
7. 输入先前作为**客户端 ID** 复制的**应用密钥**。
8. 输入先前作为**客户端机密**复制的**应用机密**。
9. 单击“确定”，然后单击“创建”以保存 Weibo 配置。


