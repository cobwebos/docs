---
title: Kerberos 约束委派与 Azure Active Directory
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
ms.openlocfilehash: 77f90cd7aa8d972226a8f134eaa7b3abfe7bea66
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2020
ms.locfileid: "92114144"
---
# <a name="windows-authentication---kerberos-constrained-delegation-with-azure-active-directory"></a>Windows 身份验证-Kerberos 约束委派与 Azure Active Directory

Kerberos 约束委派 (KCD) 提供资源之间的约束委派，并且基于服务主体名称。 它要求域管理员创建委托，并且限制为单一域。 基于资源的 KCD 通常用作一种为 web 应用程序提供 Kerberos 身份验证的方法，该应用程序在 Active Directory 林中的多个域中具有用户。

Azure Active Directory 应用程序代理可以提供单一登录 (SSO) 和远程访问基于 KCD 的应用程序，这些应用程序需要使用 Kerberos 票证进行访问，Kerberos 约束委派 (KCD) 。

通过向应用程序代理连接器授予在 Active Directory 中模拟用户的权限，可以启用使用集成 Windows 身份验证 (IWA) 的本地 KCD 应用程序的 SSO。 应用程序代理连接器将使用此权限代表用户发送和接收令牌。

## <a name="use-when"></a>何时使用

需要提供远程访问、使用预身份验证进行保护，并提供 SSO 到本地 IWA 应用程序。

![体系结构示意图](./media/authentication-patterns/kcd-auth.png)

## <a name="components-of-system"></a>系统组件

* **用户**：访问应用程序代理提供的旧版应用程序。

* **Web 浏览器**：用户与之交互的组件，用于访问应用程序的外部 URL。

* **Azure AD**：对用户进行身份验证。 

* **应用程序代理服务**：充当反向代理，用于将请求从用户发送到本地应用程序。 它位于 Azure AD。 应用程序代理还可以强制实施任何条件性访问策略。

* **应用程序代理连接器**：在 Windows server 上安装在本地，以提供与应用程序的连接。 返回 Azure AD 的响应。 与 Active Directory 执行 KCD 协商，模拟用户以获取应用程序的 Kerberos 令牌。

* **Active Directory**：将应用程序的 Kerberos 令牌发送到应用程序代理连接器。

* **旧版应用**程序：从应用程序代理接收用户请求的应用程序。 旧应用程序将响应返回到应用程序代理连接器。

## <a name="implement-windows-authentication-kcd-with-azure-ad"></a>通过 Azure AD (KCD) 实现 Windows 身份验证

* [使用应用程序代理通过 Kerberos 约束委派单一登录到应用程序](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-with-kcd) 

* [在 Azure Active Directory 中添加一个本地应用程序以通过应用程序代理进行远程访问](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application)

 
