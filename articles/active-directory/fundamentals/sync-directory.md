---
title: 与 Azure Active Directory 的目录同步
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
ms.openlocfilehash: 862d17948cb09c18f9372f8b8b981e5efa6be71b
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2020
ms.locfileid: "92114077"
---
# <a name="directory-synchronization"></a>目录同步

许多组织都有包含本地和云组件的混合基础结构。 通过在本地和云目录之间同步用户标识，用户可以使用一组凭据访问资源。 

同步是指 

* 基于特定条件创建对象
* 保持对象更新
* 当不再满足条件时删除对象。 

本地预配涉及到从本地源进行预配 (例如 Active Directory) 到 Azure Active Directory (Azure AD) 。 

## <a name="use-when"></a>何时使用

需要将标识数据从本地 Active Directory 环境同步到 Azure AD。

![体系结构图](./media/authentication-patterns/dir-sync-auth.png)

## <a name="components-of-system"></a>系统组件

* **用户**：使用 Azure AD 访问应用程序。

* **Web 浏览器**：用户与之交互的组件，用于访问应用程序的外部 URL。

* **应用程序**：使用 Azure AD 进行身份验证和授权的 Web 应用。

* **Azure AD**：通过 Azure AD Connect 从组织的本地目录同步标识信息。 

* **Azure AD Connect**：用于将本地标识基础结构连接到 Microsoft Azure AD 的工具。 向导和指导体验可帮助你部署和配置连接所需的先决条件和组件，包括从 Active Directory 同步和登录到 Azure AD。 

* **Active Directory**： Active Directory 是大多数 Windows Server 操作系统中包含的一项目录服务。 运行 Active Directory 域服务 (AD DS) 的服务器称为域控制器。 它们对域中的所有用户和计算机进行身份验证和授权。

## <a name="implement-directory-synchronization-with-azure-ad"></a>实现与 Azure AD 的目录同步

* [什么是标识预配？](https://docs.microsoft.com/azure/active-directory/cloud-provisioning/what-is-provisioning) 

* [混合标识目录集成工具](https://docs.microsoft.com/azure/active-directory/hybrid/plan-hybrid-identity-design-considerations-tools-comparison) 

* [Azure AD Connect 安装路线图](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-roadmap)
