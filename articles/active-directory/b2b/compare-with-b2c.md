---
title: 比较 B2B 协作和 B2C - Azure Active Directory | Microsoft Docs
description: Azure Active Directory B2B 协作和 Azure AD B2C 之间的区别是什么？
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: overview
ms.date: 01/30/2019
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: ba06952b4d01e06d7925f70ee4bc26407b48e130
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/22/2019
ms.locfileid: "56670583"
---
# <a name="compare-b2b-collaboration-and-b2c-in-azure-active-directory"></a>在 Azure Active Directory 中比较 B2B 协作和 B2C

Azure Active Directory (Azure AD) B2B 协作和 Azure AD B2C 都允许在 Azure AD 中与外部用户协作。 但它们有何区别？

Azure AD B2B 面向的是希望与外部用户安全共享文件和资源，使其能够进行协作的企业。 Azure 管理员在 Azure 门户中设置 B2B，而由 Azure AD 负责企业与外部合作伙伴之间的联合身份验证。 用户使用其工作/学校帐户或任何电子邮件帐户通过简单的邀请和兑换过程登录到共享资源。
 
Azure AD B2C 主要用于创建面向客户的应用的企业和开发人员。 借助 Azure AD B2C，开发人员可将 Azure AD 作为其应用程序的全功能标识系统，而让客户使用其已建立的标识（例如 Facebook 或 Gmail）进行登录。

下表详细列出了比较结果。


B2B 协作功能 |     Azure AD B2C 独立产品/服务
-------- | --------
适用于：希望在不考虑标识提供者的情况下验证合作伙伴组织中用户的身份的组织。 | 适用于：邀请移动和 Web 应用的客户（无论是个人、机构还是组织客户）加入到 Azure AD。
支持的标识：使用工作或学校帐户的员工、使用工作或学校帐户的合作伙伴或者任何电子邮件地址。 即将支持直接联合身份验证。  | 支持的标识：具有本地应用程序帐户的客户用户（任何电子邮件地址或用户名），或者任何使用直接联合身份验证的受支持社交标识。
外部用户在员工所在的目录中进行托管（特别备注的除外）。 可采用与员工相同的方式进行管理，还可将其添加到相同组，等等  | 外部用户在应用程序目录中管理。 他们与组织的员工和合作伙伴目录（若有）分开管理。
支持单一登录 (SSO) 到所有 Azure AD 连接的应用。 例如，可允许访问 Office 365 或本地应用以及其他 SaaS 应用（例如 Salesforce 或 Workday）。  |  支持在 Azure AD B2C 租户中单一登录到客户自有应用。 不可单一登录到 Office 365 或其他 Microsoft 和非 Microsoft SaaS 应用。
合作伙伴生命周期：由主办/邀请组织管理。  | 客户生命周期：自助服务或由应用程序管理。
安全策略和符合性：由主办/邀请组织管理（例如，通过[条件访问策略](https://docs.microsoft.com/azure/active-directory/b2b/conditional-access)）。  | 安全策略和符合性：由应用程序管理。
品牌：使用主办/邀请组织的品牌。  |    品牌：由应用程序管理。 通常是带品牌的产品，组织退居幕后。
详细信息：[博客文章](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/01/azure-ad-b2b-new-updates-make-cross-business-collab-easy/)、[文档](what-is-b2b.md)  | 详细信息：[产品页](https://azure.microsoft.com/services/active-directory-b2c/)、[文档](https://docs.microsoft.com/azure/active-directory-b2c/)


### <a name="next-steps"></a>后续步骤

- [什么是 Azure AD B2B 协作？](what-is-b2b.md)
- [B2B 协作用户属性](user-properties.md)

