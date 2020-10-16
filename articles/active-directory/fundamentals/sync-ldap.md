---
title: 与 Azure Active Directory 的 LDAP 同步
description: 实现此同步模式的体系结构指南
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
ms.openlocfilehash: a14149551c0fb33906ab7d1a00b387026d827911
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2020
ms.locfileid: "92114075"
---
# <a name="ldap-synchronization-with-azure-active-directory"></a>与 Azure Active Directory 的 LDAP 同步

轻型目录访问协议 (LDAP) 是在 TCP/IP 堆栈上运行的目录服务协议。 它提供用于连接到、搜索和修改 internet 目录的机制。 LDAP 目录服务基于客户端-服务器模型，其功能是启用对现有目录的访问。 许多公司依赖于本地 LDAP 服务器来存储其关键业务应用程序的用户和组。 

Azure Active Directory (Azure AD) 可以将 LDAP 同步替换为 Azure AD Connect。 Azure AD Connect 同步服务执行与同步本地环境和 Azure AD 相关的所有操作。 

## <a name="use-when"></a>何时使用

需要在本地 LDAP v3 目录和 Azure AD 之间同步标识数据。 

![体系结构图](./media/authentication-patterns/ldap-sync.png)

## <a name="components-of-system"></a>系统组件

* **用户**：访问一个应用程序，该应用程序依赖于使用 LDAP v3 目录对用户和密码进行排序。

* **Web 浏览器**：用户与之交互的组件，用于访问应用程序的外部 URL

* **Web 应用**：应用程序与 LDAP v3 目录具有依赖关系。

* **Azure AD**： Azure AD 通过 Azure AD Connect 从组织的本地 LDAP 目录 (用户、组和密码) 同步标识信息。 

* **Azure AD Connect**：是一种用于将本地标识基础结构连接到 Microsoft Azure AD 的工具。 向导和引导式体验可帮助你部署和配置连接所需的先决条件和组件。 

* **自定义连接器**：通过泛型 ldap 连接器，可以将 Azure AD Connect 同步服务与 LDAP v3 服务器集成。 它位于 Azure AD Connect。

* **Active Directory**： Active Directory 是大多数 Windows Server 操作系统中包含的一项目录服务。 运行 Active Directory 目录服务的服务器称为域控制器，它们对 Windows 域中的所有用户和计算机进行身份验证和授权。

* **Ldap v3 服务器**： ldap 协议兼容的目录，用于存储用于目录服务身份验证的企业用户和密码。

## <a name="implement-ldap-synchronization-with-azure-ad"></a>与 Azure AD 实现 LDAP 同步

* [混合标识目录集成工具](https://docs.microsoft.com/azure/active-directory/hybrid/plan-hybrid-identity-design-considerations-tools-comparison) 

* [Azure AD Connect 安装路线图](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-roadmap) 

* [概述和创建 LDAP 连接器](https://docs.microsoft.com/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-genericldap) 

   > [!NOTE]
   > LDAP 连接器是一种高级配置，需要对 Forefront Identity Manager 和/或 Microsoft Identity Manager 有一定的熟悉。 如果在生产环境中使用，我们建议有关此配置的问题应 [顶级支持](https://support.microsoft.com/premier) 或 Microsoft 合作伙伴网络。

 
