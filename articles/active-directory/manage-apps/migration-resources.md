---
title: 用于将应用迁移到 Azure Active Directory 的资源 | Microsoft Docs
description: 帮助你将应用程序访问和身份验证迁移到 Azure Active Directory (Azure AD) 的资源。
services: active-directory
author: barbkess
manager: daveba
ms.service: active-directory
ms.component: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 09/19/2018
ms.author: barbkess
ms.reviewer: baselden
ms.openlocfilehash: 5d1a122194d27d2f9d4f6a8c03adeaf2b65f7310
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2019
ms.locfileid: "54477985"
---
# <a name="resources-for-migrating-applications-to-azure-active-directory"></a>用于将应用程序迁移到 Azure Active Directory 的资源

帮助你将应用程序访问和身份验证迁移到 Azure Active Directory (Azure AD) 的资源。 请参与此简短调查（ https://aka.ms/AppsMigrationFeedback) 提供有关将应用迁移到 Azure AD 的体验反馈（包括迁移的阻止程序、是否需要工具/指南，以及保留本地 IDP 的原因）。 

| 资源  | 说明  |
|:-----------|:-------------|
|[将应用迁移到 Azure AD](https://aka.ms/migrateapps/whitepaper) | 本白皮书介绍了迁移的优势，并介绍了如何在四个明确概述的阶段中规划迁移：发现、分类、迁移和持续管理。 本文将介绍如何思考该进程并将项目分解为易于使用的部分。 整个文档为指向重要资源的链接，可全程为你提供帮助。 |
|[解决方案指南：将应用从 Active Directory 联合身份验证服务 (AD FS) 迁移到 Azure AD](https://aka.ms/migrateapps/adfssolutionguide) | 本解决方案指南逐步说明规划和执行迁移白皮书中更高级别描述的应用程序迁移项目的四个相同阶段。 在本指南中，你将学习如何将这些阶段应用于将应用程序从 Active Directory 联合身份验证服务 (AD FS) 移动到 Azure AD 的特定目标。|
| [工具：Active Directory 联合身份验证服务迁移就绪性脚本](https://aka.ms/migrateapps/adfstools) | 这是可以在本地 Active Directory 联合身份验证服务 (AD FS) 服务器上运行的脚本，用于确定应用迁移到 Azure AD 的就绪情况。|
| [部署计划：从 AD FS 迁移到密码哈希同步](https://aka.ms/ADFSTOPHSDPDownload) | 凭借密码哈希同步，可将用户密码的哈希从本地 Active Directory 同步到 Azure AD。 通过该操作，Azure AD 可对用户进行身份验证，而无需与本地 Active Directory 进行交互。| 
| [部署计划：从 AD FS 迁移到传递身份验证](https://aka.ms/ADFSTOPTADPDownload)|借助 Azure AD 直通身份验证，用户可使用相同的密码登录到本地应用程序和基于云的应用程序。 此功能可为你的用户提供更好的体验，因为他们只需要记住一个密码。 它还可降低 IT 支持人员成本，因为用户只需要记住一个密码就不会忘记如何登录。 当人们使用 Azure AD 登录时，此功能可直接通过本地 Active Directory 验证用户的密码。|
| [部署计划：使用 Azure AD 启用 SaaS 应用程序的单一登录](https://aka.ms/SSODPDownload) | 进行单一登录 (SSO) 时，只需使用单个用户帐户登录一次，就能访问开展业务所需的全部应用和资源。 例如，用户登录后，该用户可从 Microsoft Office 移动到 SalesForce 和 Box，而无需再次进行身份验证（例如，键入密码）。 
| [部署计划：使用应用程序代理将应用扩展到 Azure AD](https://aka.ms/AppProxyDPDownload)| 传统上，提供从员工笔记本电脑和其他设备到本地应用程序的访问权限会涉及虚拟专用网络 (VPN) 或外围安全区域 (DMZ)。 这些解决方案不仅复杂且难以确保安全性，而且设置和管理成本也很高。 使用 Azure AD 应用程序代理可更轻松地访问本地应用程序。 |
| [部署计划](../fundamentals/active-directory-deployment-plans.md) | 查找更多部署计划，以部署多种功能，如多重身份验证、条件访问、用户预配、无缝 SSO 和自助服务密码重置！ |


