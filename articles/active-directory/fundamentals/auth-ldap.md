---
title: LDAP 身份验证与 Azure Active Directory
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
ms.openlocfilehash: 8a70cb4754d98f4573670860c510692a7a2d134c
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2020
ms.locfileid: "92114094"
---
# <a name="ldap-authentication-with-azure-active-directory"></a>LDAP 身份验证与 Azure Active Directory

轻型目录访问协议 (LDAP) 是用于处理各种目录服务的应用程序协议。 目录服务（例如 Active Directory、 [存储用户和帐户信息](https://www.dnsstuff.com/active-directory-service-accounts)）以及安全信息（如密码）。 然后，该服务会允许该信息与网络上的其他设备共享。 企业应用程序（例如电子邮件、客户关系管理器 (Crm) ）和人力资源 (HR) 软件可以使用 LDAP 来验证、访问和查找信息。 

Azure Active Directory (Azure AD) 通过 Azure AD (AD DS) 域服务来支持此模式。 它通过将其本地 LDAP 资源移到云中，使采用云优先策略的组织可以现代化环境。 直接权益如下： 

* 与 Azure AD 集成。 用户和组的添加，或其对象的属性更改将自动从 Azure AD 租户同步到 AD DS。 对本地 Active Directory 中的对象所做的更改将同步到 Azure AD，然后 AD DS。

* 简化操作。 减少手动保留和修补本地基础结构的需要。 

* 可靠。 你获取托管的、高度可用的服务 

## <a name="use-when"></a>何时使用

应用程序或服务需要使用 LDAP 身份验证。

![体系结构示意图](./media/authentication-patterns/ldap-auth.png)

## <a name="components-of-system"></a>系统组件

* **用户**：通过浏览器访问与 LDAP 相关的应用程序。

* **Web 浏览器**：用户与之交互的接口，用于访问应用程序的外部 URL。

* **虚拟网络**： Azure 中的专用网络，旧应用程序可使用该网络使用 LDAP 服务。 

* **旧版应用**程序：需要在 Azure 中的虚拟网络中部署 LDAP 的应用程序或服务器工作负荷，或者可以通过网络路由查看 AD DS 实例 ip 的可见性。 

* **Azure AD**：通过 Azure AD Connect 从组织的本地目录同步标识信息。

* **Azure AD 域服务 (AD DS) **：从 Azure AD 执行单向同步，以提供对一组集中用户、组和凭据的访问。 AD DS 实例将分配给一个虚拟网络。 Azure 中连接到分配给 AD DS 的虚拟网络的应用程序、服务和 Vm 可使用常见的 AD DS 功能，如 LDAP、域加入、组策略、Kerberos 和 NTLM 身份验证。
   > [!NOTE]
   >  在组织无法同步密码哈希或用户使用智能卡登录的环境中，我们建议你在 AD DS 中使用资源林。 

* **Azure AD Connect**：用于将本地标识信息同步到 Microsoft Azure AD 的工具。 部署向导和引导式体验可帮助你配置连接所需的先决条件和组件，包括从 Active Directory 同步和登录到 Azure AD。 

* **Active Directory**：存储 [本地标识信息（如用户和帐户信息](https://www.dnsstuff.com/active-directory-service-accounts)）以及安全信息（如密码）的目录服务。

## <a name="implement-ldap-authentication-with-azure-ad"></a>通过 Azure AD 实现 LDAP 身份验证

* [创建和配置 Azure AD DS 实例](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance) 

* [为 Azure AD DS 实例配置虚拟网络](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-configure-networking) 

* [为 Azure AD DS 托管域配置安全 LDAP](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-configure-ldaps) 

* [在 Azure AD DS 中创建对本地域的出站林信任](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-forest-trust)

 
