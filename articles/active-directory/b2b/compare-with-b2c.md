---
title: 在 Azure Active Directory 中比较 B2B 协作和 B2C | Microsoft Docs
description: Azure Active Directory B2B 协作和 Azure AD B2C 之间的区别是什么？
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 03/15/2017
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 19e8e9d22938fa8a18299d67aa77824aaae3f6da
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/17/2018
---
# <a name="compare-b2b-collaboration-and-b2c-in-azure-active-directory"></a>在 Azure Active Directory 中比较 B2B 协作和 B2C

Azure Active Directory (Azure AD) B2B 协作和 Azure AD B2C 都允许在 Azure AD 中与外部用户协作。 但它们有何区别？


B2B 协作功能 |     Azure AD B2C 独立产品/服务
-------- | --------
适用于：希望在不考虑标识提供者的情况下验证合作伙伴组织中用户的身份的组织。 | 适用于：邀请移动和 Web 应用的客户（无论是个人、机构还是组织客户）加入到 Azure AD。
支持的标识：使用工作或学校帐户的员工、使用工作或学校帐户的合作伙伴或者任何电子邮件地址。 即将支持直接联合身份验证。  | 支持的标识：具有本地应用程序帐户的客户用户（任何电子邮件地址或用户名），或者任何使用直接联合身份验证的受支持社交标识。
合作伙伴用户所在的目录：来自外部组织的合作伙伴用户在员工所在的目录中进行托管（特别备注的除外）。 可采用与员工相同的方式进行管理，还可将其添加到相同组等  | 客户用户实体所在的目录：位于应用程序目录中。 与组织的员工和合作伙伴目录（若有）分开管理。
支持单一登录 (SSO) 到所有 Azure AD 连接的应用。 例如，可允许访问 Office 365 或本地应用以及其他 SaaS 应用（例如 Salesforce 或 Workday）。  |  支持在 Azure AD B2C 租户中单一登录到客户自有应用。 不可单一登录到 Office 365 或其他 Microsoft 和非 Microsoft SaaS 应用。
合作伙伴生命周期：由主办/邀请组织管理。  | 客户生命周期：自助服务或由应用程序管理。
安全策略和符合性：由主办/邀请组织管理。  | 安全策略和符合性：由应用程序管理。
品牌：使用主办/邀请组织的品牌。  |    品牌：由应用程序管理。 通常是带品牌的产品，组织退居幕后。
详细信息：[博客文章](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/01/azure-ad-b2b-new-updates-make-cross-business-collab-easy/)、[文档](what-is-b2b.md)  | 详细信息：[产品页](https://azure.microsoft.com/services/active-directory-b2c/)、[文档](https://docs.microsoft.com/azure/active-directory-b2c/)


### <a name="next-steps"></a>后续步骤

- [什么是 Azure AD B2B 协作？](what-is-b2b.md)
- [B2B 协作用户属性](user-properties.md)

