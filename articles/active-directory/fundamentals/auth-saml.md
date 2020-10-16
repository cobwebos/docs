---
title: Azure Active Directory 的 SAML 身份验证
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
ms.openlocfilehash: 05b13e04db8e83a8a10c2d7fe0aea202dfa3b69c
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2020
ms.locfileid: "92114156"
---
# <a name="saml-authentication-with-azure-active-directory"></a>Azure Active Directory 的 SAML 身份验证

安全断言标记语言 (SAML) 是在标识提供者和服务提供商之间交换身份验证和授权数据的开放标准。 SAML 是一种基于 XML 的标记语言，用于安全断言，服务提供程序使用这些声明来做出访问控制决策。 

SAML 规范定义了三个角色：

* 主体，通常是用户
* 标识提供者 (IdP) 
* 服务提供商 (SP) 


## <a name="use-when"></a>何时使用

需要为企业 SAML 应用程序 (SSO) 体验提供单一登录。

尽管 SAML 地址为 SSO 的最重要用例之一是 SSO （特别是通过跨安全域扩展 SSO），但也有其他用例 (也称为配置文件) 。 

![体系结构图](./media/authentication-patterns/saml-auth.png)

## <a name="components-of-system"></a>系统组件

* **User**：从应用程序请求服务。

* **Web 浏览器**：用户与之交互的组件。

* **Web 应用**：支持 SAML 并使用 Azure AD 作为 IdP 的企业应用程序。

* **令牌**： saml 断言 (也称为 saml 令牌) ，用于携带 IdP 有关原则 (用户) 的声明集。 它包含身份验证信息、属性和授权决策声明。

* **Azure AD**：企业云 IdP，为 SAML 应用提供 SSO 和多重身份验证。 它在向依赖应用程序提供身份验证服务的同时，同步、维护和管理用户的标识信息。 

## <a name="implement-saml-authentication-with-azure-ad"></a>通过 Azure AD 实现 SAML 身份验证

* [有关使用 Azure Active Directory 集成 SaaS 应用程序的教程](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) 

* [为非库应用程序配置基于 SAML 的单一登录](https://docs.microsoft.com/azure/active-directory/manage-apps/add-non-gallery-app) 

* [Azure AD 使用 SAML 协议的方式](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-protocol-reference)
