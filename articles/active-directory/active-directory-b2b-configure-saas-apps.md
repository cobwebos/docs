---
title: "在 Azure Active Directory 中为 B2B 协作配置 SaaS 应用 | Microsoft 文档"
description: "有关 Azure Active Directory B2B 协作的代码和 PowerShell 示例"
services: active-directory
documentationcenter: 
author: sasubram
manager: mtillman
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 05/23/2017
ms.author: sasubram
ms.openlocfilehash: dc0bb3950f30b2ea676de406538e3a463ad21429
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="configure-saas-apps-for-b2b-collaboration"></a>为 B2B 协作配置 SaaS 应用

Azure Active Directory (Azure AD) B2B 协作适用于与 Azure AD 集成的大多数应用。 本部分逐步说明如何配置一些常用的 SaaS 应用来与 Azure AD B2B 配合使用。

在查看应用特定的说明之前，请考虑以下经验法则：

* 对于大多数应用，需要手动完成用户设置。 也就是说，还必须在应用中手动创建用户。

* 对于支持自动设置的应用（例如 Dropbox），可从这些应用创建单独的邀请。 用户必须接受每个邀请。

* 在用户属性中，为了缓解来宾用户的任何用户配置文件磁盘 (UPD) 损坏问题，请始终将“用户标识符”设置为 **user.mail**。


## <a name="dropbox-business"></a>Dropbox Business

为了让用户使用其组织帐户登录，必须手动将 Dropbox Business 配置为使用 Azure AD 作为安全断言标记语言 (SAML) 标识提供者。 如果不这样配置 Dropbox Business，它不会显示提示，或者以其他方式允许用户使用 Azure AD 登录。

1. 要将 Dropbox Business 应用添加到 Azure AD 中，请在左窗格中选择“企业应用程序”，然后单击“添加”。

  ![“企业应用程序”页上的“添加”按钮](media/active-directory-b2b-configure-saas-apps/add-dropbox.png)

2. 在“添加应用程序”窗口中的搜索框内输入 **dropbox**，并在结果列表中选择“Dropbox for Business”。

  ![在“添加应用程序”页上搜索“dropbox”](media/active-directory-b2b-configure-saas-apps/add-app-dialog.png)

3. 在“单一登录”页上的左窗格中选择“单一登录”，并在“用户标识符”框中输入 **user.mail**。 （用户标识符默认设置为“UPN”。）

  ![为应用配置单一登录](media/active-directory-b2b-configure-saas-apps/configure-app-sso.png)

4. 要下载用于 Dropbox 配置的证书，请选择“配置 DropBox”，并在列表中选择“SAML 单一登录服务 URL”。

  ![下载用于 DropBox 配置的证书](media/active-directory-b2b-configure-saas-apps/download-certificate.png)

5. 在“单一登录”页中使用登录 URL 登录到 Dropbox。

  ![Dropbox 登录页](media/active-directory-b2b-configure-saas-apps/sign-in-to-dropbox.png)

6. 在菜单中选择“管理控制台”。

  ![Dropbox 菜单中的“管理控制台”链接](media/active-directory-b2b-configure-saas-apps/dropbox-menu.png)

7. 在“身份验证”对话框中选择“更多”，上载证书，并在“登录 URL”框中输入 SAML 单一登录 URL。

  ![折叠的“身份验证”对话框中的“更多”链接](media/active-directory-b2b-configure-saas-apps/dropbox-auth-01.png)

  ![展开的“身份验证”对话框中的“登录 URL”链接](media/active-directory-b2b-configure-saas-apps/paste-single-sign-on-URL.png)

8. 要在 Azure 门户中配置自动用户设置，请在左窗格中选择“预配”，在“预配模式”框中选择“自动”，并选择“授权”。

  ![在 Azure 门户中配置自动用户预配](media/active-directory-b2b-configure-saas-apps/set-up-automatic-provisioning.png)

在 Dropbox 应用中设置来宾或成员用户后，他们会收到来自 Dropbox 的单独邀请。 若要使用 Dropbox 单一登录，受邀者必须单击邀请中的某个链接接受邀请。

## <a name="box"></a>Box
使用基于 SAML 协议的联合身份验证，可让用户使用其 Azure AD 帐户对 Box 来宾用户进行身份验证。 在此过程中，需要将元数据上传到 Box.com。

1. 从企业应用添加 Box 应用。

2. 按以下顺序来配置单一登录：

  ![配置 Box 单一登录](media/active-directory-b2b-configure-saas-apps/configure-box-sso.png)

 a. 在“登录 URL”框中，确保登录 URL 是在 Azure 门户中为 Box 适当设置的 URL。 此 URL 是 Box.com 租户的 URL。 它应该遵循 *https://.box.com* 中所述的命名约定。  
 “标识符”不适用于此应用，但仍显示为必填字段。

 b. 在“用户标识符”框中输入 **user.mail**（适用于来宾帐户 SSO）。

 c. 在“SAML 签名证书”下面，单击“创建新证书”。

 d. 要开始将 Box.com 租户配置为使用 Azure AD 作为标识提供者，请下载元数据文件并将其保存到本地驱动器。

 e. 将元数据文件转发给 Box 支持团队，他们将为你配置单一登录。

3. 为了在 Azure AD 中启用自动用户设置，请在左窗格中选择“预配”，并选择“授权”。

  ![授权 Azure AD 连接到 Box](media/active-directory-b2b-configure-saas-apps/auth-azure-ad-to-connect-to-box.png)

与 Dropbox 受邀者一样，Box 受邀者也必须从 Box 应用兑换其邀请。

## <a name="next-steps"></a>后续步骤

请参阅以下有关 Azure AD B2B 协作的文章：

* [什么是 Azure AD B2B 协作？](active-directory-b2b-what-is-azure-ad-b2b.md)
* [B2B 协作用户属性](active-directory-b2b-user-properties.md)
* [向角色添加 B2B 协作用户](active-directory-b2b-add-guest-to-role.md)
* [委托 B2B 协作邀请](active-directory-b2b-delegate-invitations.md)
* [动态组和 B2B 协作](active-directory-b2b-dynamic-groups.md)
* [B2B 协作代码和 PowerShell 示例](active-directory-b2b-code-samples.md)
* [B2B 协作用户令牌](active-directory-b2b-user-token.md)
* [B2B 协作用户声明映射](active-directory-b2b-claims-mapping.md)
* [Office 365 外部共享](active-directory-b2b-o365-external-user.md)
* [B2B 协作的当前限制](active-directory-b2b-current-limitations.md)
