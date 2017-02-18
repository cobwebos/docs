---
title: "在 Azure Active Directory 中为 B2B 协作配置 SaaS 应用 | Microsoft 文档"
description: "有关 Azure Active Directory B2B 协作的代码和 PowerShell 示例"
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 02/06/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 0c05cd490ee9125f7e5182cb502db6f4e9390094
ms.openlocfilehash: 3f41fdaa4f0ec31c9f11f2826b5cb9ccbf4db30d


---

# <a name="configure-saas-apps-for-b2b-collaboration"></a>为 B2B 协作配置 SaaS 应用

Azure Active Directory (Azure AD) B2B 协作适用于与 Azure AD 集成的大多数应用程序。 在此部分中，我们将浏览有关如何配置某些常用的 SAS 应用以用于 Azure AD B2B 的说明。
在查看特定于应用程序的说明之前，以下是一些经验法则：

* 请记住，对于大多数应用程序来说，用户预配需要手动进行（即，必须也在应用程序中手动创建用户）。

* 对于支持自动预配的应用（例如 Dropbox），将会从应用程序创建单独的邀请。 用户必须确保接受每个邀请。

* 在用户属性中，始终将用户标识符设置为 user.mail（以缓解来宾用户的已损坏 UPD 所带来的任何问题）


##<a name="dropbox-for-business"></a>DropBox for Business

对于能够使用其组织帐户登录的用户，必须手动配置 Dropbox for Business 以使用 Azure AD 作为 SAML 标识提供者。 如果尚未这样配置，Dropbox for Business 将无法提示或以其他方式允许用户使用 Azure AD 进行登录。

1. 将 DropBox for Business 应用程序添加到 Azure AD 中，如屏幕截图中所示。

  ![将 Dropbox 添加到 Azure AD](media/active-directory-b2b-configure-saas-apps/add-dropbox.png)

  ![将 Dropbox 添加到 Azure AD](media/active-directory-b2b-configure-saas-apps/add-app-dialog.png)

2. 配置应用程序。

  ![为应用配置单一登录](media/active-directory-b2b-configure-saas-apps/configure-app-sso.png)

3. 选择单一登录配置并将用户标识符更改为 user.mail（默认情况下，为其 UPN）

4. 下载要用于 DropBox 配置的证书。

  ![下载证书](media/active-directory-b2b-configure-saas-apps/download-certificate.png)

5. 使用“配置 DropBox”选项（下面的屏幕截图详细说明了该过程）

6. 获取要在配置中使用的 SAML 单一登录 URL。

7. 从 DropBox 配置页获取登录 URL。

  ![登录到 dropbox](media/active-directory-b2b-configure-saas-apps/sign-in-to-dropbox.png)

  ![Dropbox 菜单](media/active-directory-b2b-configure-saas-apps/dropbox-menu.png)

  ![折叠的 Dropbox 身份验证对话框](media/active-directory-b2b-configure-saas-apps/dropbox-auth-01.png)

  ![展开的 Dropbox 身份验证对话框](media/active-directory-b2b-configure-saas-apps/dropbox-auth-02.png)

8. 在此处上载证书并粘贴 SAML 单一登录 URL。

  ![粘贴 SAML SSO URL](media/active-directory-b2b-configure-saas-apps/paste-single-sign-on-URL.png)

9. 在 Azure 门户中配置自动用户预配。

  ![设置自动用户预配](media/active-directory-b2b-configure-saas-apps/set-up-automatic-provisioning.png)

10. 在 DropBox 应用程序中预配后，来宾/成员用户将从 DropBox 收到单独邀请。 被邀请者必须通过单击该链接接受邀请，以便在 DropBox 中使用单一登录。

## <a name="box"></a>Box
本部分概述如何让用户使用基于 SAML 协议的联合身份验证通过其在 Azure AD 中的帐户向 Box 进行来宾用户身份验证。 在此过程中，需要将元数据上传到 Box.com。

1. 从企业应用程序添加 Box

2. 配置单一登录

  ![配置 Box 单一登录](media/active-directory-b2b-configure-saas-apps/configure-box-sso.png)

3. 首先，确保在 Azure 管理门户中为 Box 正确设置了登录 URL。 这是 Box.com 租户的 URL，应遵循以下格式：https://.box.com。

4. 该标识符不适用于此应用程序，但仍显示为必填字段，只需注意这一事实。

5. user.mail 的用户标识符（以确保来宾帐户的 SSO）

6. 创建新的 SAML 证书

7. 若要开始配置 Box.com 租户以使用 Azure Active Directory 作为标识提供者，首先请下载以下元数据文件，并将其保存在本地计算机上：下载元数据文件（确保使其处于活动状态）

8. 将该元数据文件转发给 Box 支持团队。 支持团队将为你配置单一登录。

9. 为 Azure AD 自动用户预配做准备。

  ![授权 Azure AD，使其可以连接到 box](media/active-directory-b2b-configure-saas-apps/auth-azure-ad-to-connect-to-box.png)

受邀者还必须从 Box 应用程序兑换其邀请。

## <a name="next-steps"></a>后续步骤

在 Azure AD B2B 协作网站上浏览我们的其他文章：

* [什么是 Azure AD B2B 协作？](active-directory-b2b-what-is-azure-ad-b2b.md)
* [B2B 协作用户属性](active-directory-b2b-user-properties.md)
* [向角色添加 B2B 协作用户](active-directory-b2b-add-guest-to-role.md)
* [委托 B2B 协作邀请](active-directory-b2b-delegate-invitations.md)
* [动态组和 B2B 协作](active-directory-b2b-dynamic-groups.md)
* [B2B 协作代码和 PowerShell 示例](active-directory-b2b-code-samples.md)
* [B2B 协作用户令牌](active-directory-b2b-user-token.md)
* [B2B 协作用户声明映射](active-directory-b2b-claims-mapping.md)
* [Office 365 外部共享](active-directory-b2b-o365-external-user.md)
* [B2B 协作当前限制](active-directory-b2b-current-limitations.md)



<!--HONumber=Feb17_HO1-->


