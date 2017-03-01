---

title: "在 Azure Active Directory 中比较 B2B 协作和 B2C | Microsoft Docs"
description: "Azure Active Directory B2B 协作和 Azure AD B2C 之间的区别是什么？"
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 02/08/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 72e7e57185d882a5f3243e2705aed54ee8347798
ms.openlocfilehash: 5d157a936317bbf66cfc3634d4d08ea3483d137b


---

# <a name="compare-b2b-collaboration-and-b2c-in-azure-active-directory"></a>在 Azure Active Directory 中比较 B2B 协作和 B2C

Azure Active Directory (Azure AD) B2B 协作和 Azure AD B2C 都允许在 Azure AD 中与外部用户协作。 但它们有何区别？


B2B 协作功能 |     Azure AD B2C 独立产品/服务
-------- | --------
适用于：希望使用任何所选标识向其他任何组织的用户提供公司数据、资源和应用程序访问权限的组织。 | 适用于：使用任何所选标识面向客户的客户版移动和 Web 应用，其中这些客户包括个人、公民以及机构/组织客户（非员工或外部协作者）。
支持的标识：使用工作或学校帐户的员工、使用工作或学校帐户的合作伙伴或者任何电子邮件地址。 即将支持直接联合身份验证。  | 支持的标识：具有本地应用程序帐户的客户用户（任何电子邮件地址或用户名），或者任何使用直接联合身份验证的受支持社交标识。
合作伙伴用户所在的目录：来自外部组织的合作伙伴用户在员工所在的目录中进行托管（特别备注的除外）。 可采用与员工相同的方式管理这些外部用户，还可将其添加到相同组等  | 客户用户实体所在的目录：位于应用程序目录中。 与组织的员工和合作伙伴目录分开管理（若有）。
支持单一登录 (SSO) 到 Office 365 等所有连接了 Azure AD 的应用（包括本地应用），以及Salesforce、Box 和 Workday 等其他 Microsoft 和非 Microsoft SaaS 应用。  |  支持在 Azure AD B2C 租户中单一登录到客户自有应用。 不可单一登录到 Office 365 或其他 Microsoft 和非 Microsoft SaaS 应用。
合作伙伴生命周期：由主办/邀请组织管理。  | 客户生命周期：自助服务或由应用程序管理。
安全策略和符合性：由主办/邀请组织管理。  | 安全策略和符合性：由应用程序管理。
品牌：使用主办/邀请组织的品牌。  |    品牌：由应用程序管理。 通常是带品牌的产品，组织退居幕后。
详细信息：[博客文章](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/01/azure-ad-b2b-new-updates-make-cross-business-collab-easy/)、[文档](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)  | 详细信息：[产品页](https://azure.microsoft.com/en-us/services/active-directory-b2c/)、[文档](https://docs.microsoft.com/en-us/azure/active-directory-b2c/)


### <a name="next-steps"></a>后续步骤

在 Azure AD B2B 协作网站上浏览我们的其他文章：

* [什么是 Azure AD B2B 协作？](active-directory-b2b-what-is-azure-ad-b2b.md)
* [B2B 协作用户属性](active-directory-b2b-user-properties.md)
* [向角色添加 B2B 协作用户](active-directory-b2b-add-guest-to-role.md)
* [委托 B2B 协作邀请](active-directory-b2b-delegate-invitations.md)
* [动态组和 B2B 协作](active-directory-b2b-dynamic-groups.md)
* [为 B2B 协作配置 SaaS 应用](active-directory-b2b-configure-saas-apps.md)
* [B2B 协作用户令牌](active-directory-b2b-user-token.md)
* [B2B 协作用户声明映射](active-directory-b2b-claims-mapping.md)
* [Office 365 外部共享](active-directory-b2b-o365-external-user.md)
* [B2B 协作当前限制](active-directory-b2b-current-limitations.md)
* [获取对 B2B 协作的支持](active-directory-b2b-support.md)



<!--HONumber=Feb17_HO2-->


