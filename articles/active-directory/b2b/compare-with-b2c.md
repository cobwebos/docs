---
title: 比较外部标识 - Azure Active Directory | Microsoft Docs
description: Azure AD 外部标识允许组织外部人员使用自己的标识访问应用和资源。 比较外部标识的解决方案，包括 Azure Active Directory B2B 协作和 Azure AD B2C。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: overview
ms.date: 05/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9006a70ae941abb700412a7c596627939c994028
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83587504"
---
# <a name="compare-solutions-for-external-identities-in-azure-active-directory"></a>比较 Azure Active Directory 中的外部标识的解决方案

借助 Azure AD 中的外部标识，可以允许组织外部人员访问应用和资源，而让他们使用所需的任何标识进行登录。 合作伙伴、分销商、供应商和其他来宾用户可以“自带标识”。 无论他们是 Azure AD 或其他 IT 托管系统的一部分，还是具有 Google 或 Facebook 等非托管社交标识，他们都可以使用自己的凭据登录。 标识提供程序管理外部用户的标识，而你使用 Azure AD 管理对应用的访问，以便保护资源。 

## <a name="external-identities-scenarios"></a>外部标识方案

Azure AD 外部标识在用户与组织的关系上关注较少，而在个人想要登录到应用和资源的方式上关注较多。 借助此框架，Azure AD 支持各种方案，从企业对企业 (B2B) 协作到面向客户和消费者（企业对消费者或 B2C）的应用开发。

- 与外部用户共享应用（B2B 协作）。 邀请外部用户加入你自己的租户，作为可向其分配权限（用于授权）的“来宾”用户，同时允许他们使用现有凭据（用于身份验证）。 用户使用其工作帐户、学校帐户或任何电子邮件帐户通过简单的邀请和兑换过程登录到共享资源。 现在，随着自助注册用户流（预览版）可用，你还可以通过要共享的应用程序为外部用户提供登录体验。 可以配置用户流设置，以控制用户注册应用程序的方式，从而允许他们使用工作帐户、学校帐户或任何想要使用的社交标识（如 Google 或 Facebook）。  有关详细信息，请参阅 [Azure AD B2B 文档](index.yml)。

- 开发面向其他 Azure AD 租户（单租户或多租户）的应用。 为 Azure AD 开发应用程序时，可以定向到单个组织中的用户（单租户），或已具有 Azure AD 租户的任何组织的用户（称为多租户应用程序）。 这些多租户应用程序在你自己的 Azure AD 中自行注册过一次，但随后可以由任何组织中的任何 Azure AD 用户使用，不需要你做任何额外的工作。

- 为消费者和客户 (Azure AD B2C) 开发白标应用。 如果你是创建面向客户的应用的业务或开发人员，则可以使用 Azure AD B2C 扩展到消费者、客户或公民。 开发人员可将 Azure AD 作为其应用程序的全功能标识系统，而让客户使用其已建立的标识（例如 Facebook 或 Gmail）进行登录。 借助 Azure AD B2C，可以在使用你的应用程序时，完全自定义和控制客户的注册和登录方式以及管理其个人资料。 有关详细信息，请参阅 [Azure AD B2C 文档](https://docs.microsoft.com/azure/active-directory-b2c/)。

下表提供了可使用 Azure AD 外部标识实现的各种方案的详细比较。

| 多租户应用程序  | 外部用户协作 (B2B) | 面向消费者或客户 (B2C) 的应用  |
| ---- | --- | --- |
| 主要方案：企业软件即服务 (SaaS) | 主要方案：使用 Microsoft 应用程序（Office 365、Teams、...）或自己的协作软件进行协作。  | 主要方案：使用自定义开发的应用程序的事务应用程序。   |
| 适用于：希望向许多企业客户提供软件的组织。    | 适用于：希望在不考虑标识提供者的情况下验证合作伙伴组织中用户的身份的组织。    | 适用于：邀请移动和 Web 应用的客户（无论是个人、机构还是组织客户）加入到不同于自己组织的目录的 Azure AD 目录。 |
| 支持的标识：具有 Azure AD 帐户的员工。 | 支持的标识：使用工作或学校帐户的员工、使用工作或学校帐户的合作伙伴或者任何电子邮件地址。 即将支持直接联合身份验证。      | 支持的标识：具有本地应用程序帐户的客户用户（任何电子邮件地址或用户名），或者任何使用直接联合身份验证的受支持社交标识。       |
| 外部用户在其自己的目录中管理，该目录与注册应用程序所在的目录相隔离。    | 外部用户在员工所在的目录中进行托管（特别备注的除外）。 可采用与员工相同的方式进行管理，还可将其添加到相同组，等等。    | 外部用户在应用程序目录中管理。 他们与组织的员工和合作伙伴目录（若有）分开管理。  |
| 单一登录：支持 SSO 到所有 Azure AD 连接的应用。          | 单一登录：支持 SSO 到所有 Azure AD 连接的应用。 例如，可允许访问 Office 365 或本地应用以及其他 SaaS 应用（例如 Salesforce 或 Workday）。    | 单一登录：支持在 Azure AD B2C 租户中单一登录到客户自有应用。 不支持单一登录到 Office 365 或其他 Microsoft SaaS 应用。    |
| 客户生命周期：由用户的本组织管理。      | 合作伙伴生命周期：由主办/邀请组织管理。    | 客户生命周期：自助服务或由应用程序管理。      |
| 安全策略和符合性：由主办/邀请组织管理（例如，通过[条件访问策略](https://docs.microsoft.com/azure/active-directory/b2b/conditional-access)）。           | 安全策略和符合性：由主办/邀请组织管理（例如，通过[条件访问策略](https://docs.microsoft.com/azure/active-directory/b2b/conditional-access)）。 | 安全策略和符合性：由应用程序管理。        |
| 品牌：使用主办/邀请组织的品牌。   | 品牌：使用主办/邀请组织的品牌。    | 品牌：由应用程序管理。 通常是带品牌的产品，组织退居幕后。   |
| 详细信息：[管理多租户应用程序中的标识](https://docs.microsoft.com/azure/architecture/multitenant-identity/)、[操作指南](https://docs.microsoft.com/azure/active-directory/develop/howto-convert-app-to-be-multi-tenant) | 详细信息：[博客文章](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/01/azure-ad-b2b-new-updates-make-cross-business-collab-easy/)、[文档](what-is-b2b.md)                   | 详细信息：[产品页](https://azure.microsoft.com/services/active-directory-b2c/)、[文档](https://docs.microsoft.com/azure/active-directory-b2c/)       |

使用 Azure AD 外部标识保护和管理超出组织边界的客户和合作伙伴。

### <a name="next-steps"></a>后续步骤

- [什么是 Azure AD B2B 协作？](what-is-b2b.md)
- [关于 Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/overview)
