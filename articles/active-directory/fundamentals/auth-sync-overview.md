---
title: Azure Active Directory 身份验证和同步协议概述
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
ms.openlocfilehash: 4d881dc3fe3e3caa1058cf97834735910b0de1d9
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2020
ms.locfileid: "92114155"
---
# <a name="azure-active-directory-integrations-with-legacy-authentication-and-synchronization-protocols"></a>Azure Active Directory 与旧身份验证和同步协议的集成

Microsoft Azure Active Directory (Azure AD) 可实现与多个身份验证和同步协议的集成。 通过身份验证集成，你可以使用 Azure AD 及其安全和管理功能，只需对使用旧身份验证方法的应用程序进行少量更改或无更改。 利用同步集成，你可以将用户和组数据同步到 Azure AD，然后将用户 Azure AD 管理功能。 某些同步模式还启用自动预配。

## <a name="authentication-patterns"></a>身份验证模式

下表显示了身份验证模式及其功能。 选择要查看的身份验证模式的名称

* 详细说明

* 何时使用

* 体系结构图

* 系统组件的说明

* 如何实现集成的链接

 

| 身份验证模式| 身份验证| 授权| 多重身份验证| 条件性访问 |
| - |- | - | - | - |
| [基于标头的身份验证](auth-header-based.md)|![选中标记](./media/authentication-patterns/check.png)| ![选中标记](./media/authentication-patterns/check.png)| ![选中标记](./media/authentication-patterns/check.png)| ![选中标记](./media/authentication-patterns/check.png) |
| [LDAP 身份验证](auth-ldap.md)| ![选中标记](./media/authentication-patterns/check.png)| | |  |
| [OAuth 2.0 身份验证](auth-oauth2.md)| ![选中标记](./media/authentication-patterns/check.png)| ![选中标记](./media/authentication-patterns/check.png)| ![选中标记](./media/authentication-patterns/check.png)| ![选中标记](./media/authentication-patterns/check.png) |
| [OIDC authentication](auth-oidc.md)| ![选中标记](./media/authentication-patterns/check.png)| ![选中标记](./media/authentication-patterns/check.png)| ![选中标记](./media/authentication-patterns/check.png)| ![选中标记](./media/authentication-patterns/check.png) |
| [基于密码的 SSO 身份验证](auth-password-based-sso.md )| ![选中标记](./media/authentication-patterns/check.png)| ![选中标记](./media/authentication-patterns/check.png)| ![选中标记](./media/authentication-patterns/check.png)| ![选中标记](./media/authentication-patterns/check.png) |
| [RADIUS 身份验证]( auth-radius.md)| ![选中标记](./media/authentication-patterns/check.png)| | ![选中标记](./media/authentication-patterns/check.png)| ![选中标记](./media/authentication-patterns/check.png) |
| [远程桌面网关服务](auth-remote-desktop-gateway.md)| ![选中标记](./media/authentication-patterns/check.png)| ![选中标记](./media/authentication-patterns/check.png)| ![选中标记](./media/authentication-patterns/check.png)| ![选中标记](./media/authentication-patterns/check.png) |
| [SAML 身份验证](auth-saml.md)| ![选中标记](./media/authentication-patterns/check.png)| ![选中标记](./media/authentication-patterns/check.png)| ![选中标记](./media/authentication-patterns/check.png)| ![选中标记](./media/authentication-patterns/check.png) |
| [Windows 身份验证-Kerberos 约束委派](auth-kcd.md)| ![选中标记](./media/authentication-patterns/check.png)| ![选中标记](./media/authentication-patterns/check.png)| ![选中标记](./media/authentication-patterns/check.png)| ![选中标记](./media/authentication-patterns/check.png) |


 
## <a name="synchronization-patterns"></a>同步模式

下表显示了同步模式及其功能。 选择要查看的模式名称

* 详细说明

* 何时使用

* 体系结构图

* 系统组件的说明

* 如何实现集成的链接



| 同步模式| 目录同步| 用户预配 |
| - | - | - |
| [目录同步](sync-directory.md)| ![选中标记](./media/authentication-patterns/check.png)|  |
| [LDAP 同步](sync-ldap.md)| ![选中标记](./media/authentication-patterns/check.png)|  |
| [SCIM 同步](sync-scim.md)| ![选中标记](./media/authentication-patterns/check.png)| ![选中标记](./media/authentication-patterns/check.png) |

