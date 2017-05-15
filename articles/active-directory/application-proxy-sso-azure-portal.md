---
title: "使用 Azure AD 应用程序代理进行应用的单一登录 | Microsoft 文档"
description: "在 Azure 门户中使用 Azure AD 应用程序代理为已发布的本地应用程序启用单一登录。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: asteen
ms.assetid: d94ac3f4-cd33-4c51-9d19-544a528637d4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: kgremban
ms.translationtype: Human Translation
ms.sourcegitcommit: 54b5b8d0040dc30651a98b3f0d02f5374bf2f873
ms.openlocfilehash: b0454ad51a2d0edecb6f7e7748ec3b74eeefa1da
ms.contentlocale: zh-cn
ms.lasthandoff: 04/28/2017

---


# <a name="provide-single-sign-on-with-azure-ad-application-proxy---public-preview"></a>通过 Azure AD 应用程序代理提供单一登录 - 公共预览

Azure Active Directory 应用程序代理还可以发布本地应用程序供远程员工进行安全访问，从而提高工作效率。 在 Azure 门户中，还可以设置针对这些应用的单一登录 (SSO)。 现在，用户只需使用 Azure AD 进行身份验证，然后不需再次登录即可访问企业应用程序。

在本文中，我们以基于密码的应用为例，介绍如何通过密码存储为用户提供简单的 SSO 体验。 

你应该已经通过应用程序代理发布和测试了应用。 否则，请执行[使用 Azure AD 应用程序代理（公共预览版）发布应用程序](application-proxy-publish-azure-portal.md)中的步骤，然后再返回到此处。 

或者，如果不熟悉应用程序代理，可通过[如何提供对本地应用程序的安全远程访问](active-directory-application-proxy-get-started.md)一文详细了解此功能。

## <a name="set-up-password-vaulting-for-your-application"></a>为应用程序设置密码存储

1. 以管理员身份登录到 [Azure 门户](https://portal.azure.com)。
2. 选择“Azure Active Directory” > “企业应用程序” > “所有应用程序”。
3. 从列表中选择要设置 SSO 的应用。 如果有许多应用，则可使用搜索框筛选正确的应用。  
4. 在“管理”部分选择“单一登录”。

   ![选择“单一登录”](./media/application-proxy-sso-azure-portal/select-sso.png)

5. 对于 SSO 模式，请选择“基于密码的登录”。
6. 对于登录 URL，请输入用户登录应用时在其中输入用户名和密码的页面的 URL。 该 URL 应该是通过应用程序代理发布应用时创建的外部 URL。 

   ![选择“基于密码的登录”并输入 URL](./media/application-proxy-sso-azure-portal/password-sso.png)

7. 选择“保存”。

<!-- Need to repro?
7. The page should tell you that a sign-in form was successfully detected at the provided URL. If it doesn't, select **Configure [your app name] Password Single Sign-on Settings** and choose **Manually detect sign-in fields**. Follow the instructions to point out where the sign-in credentials go. 
-->

## <a name="test-your-app"></a>测试应用程序

转到 [MyApps 站点](https://myapps.microsoft.com)并选择刚配置的应用。 使用该应用的凭据（或者已设置的具有访问权限的测试帐户的凭据）登录。 一旦成功登录，即可离开应用，在返回时无需再次输入凭据。 

## <a name="next-steps"></a>后续步骤

了解如何通过其他方式实现[通过应用程序代理进行的单一登录](active-directory-application-proxy-sso-using-kcd.md)

