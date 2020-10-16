---
title: Azure Active Directory 的基于密码的身份验证
description: 实现此身份验证模式的体系结构指南
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: f05bf8f5f7ec4907c2cd61ff48e3438dfa1e097a
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2020
ms.locfileid: "92114084"
---
# <a name="password-based-authentication-with-azure-active-directory"></a>Azure Active Directory 的基于密码的身份验证

基于密码的单个 Sign-On (SSO) 使用应用程序的现有身份验证过程。 启用基于密码的 SSO 时，Azure Active Directory (Azure AD) 在目录中收集、加密和安全地存储用户凭据。 当用户尝试登录时，Azure AD 向应用程序提供用户名和密码。

当应用程序使用用户名和密码而不是访问令牌和标头进行身份验证时，请选择 "基于密码的 SSO"。 基于密码的 SSO 支持具有基于 HTML 的登录页的任何基于云的应用程序。 

## <a name="use-when"></a>何时使用

需要使用预身份验证进行保护，并通过密码保管库将其提供给 web 应用。

![体系结构图](./media/authentication-patterns/password-based-sso-auth.png)


## <a name="components-of-system"></a>系统组件

* **用户**：从 "我的应用程序" 或通过直接访问站点来访问基于格式的应用程序。 

* **Web 浏览器**：用户与之交互的组件，用于访问应用程序的外部 URL。 用户通过 MyApps 扩展访问基于窗体的应用程序。 

* **MyApps 扩展**：标识已配置的基于密码的 SSO 应用程序，并向登录表单提供凭据。 在 web 浏览器上安装 MyApps 扩展。 

* **Azure AD**：对用户进行身份验证。

## <a name="implement-password-based-sso-with-azure-ad"></a>实现 Azure AD 的基于密码的 SSO

* [什么是基于密码的 SSO](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) 

* [为云应用程序配置基于密码的 SSO ](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-password-single-sign-on-non-gallery-applications)

* [用应用程序代理为本地应用程序配置基于密码的 SSO](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-password-vaulting)

 
