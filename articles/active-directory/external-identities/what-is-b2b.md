---
title: 什么是 Azure Active Directory 中的 B2B 协作？
description: Azure Active Directory B2B 协作支持来宾用户访问权限，以便安全地与外部合作伙伴共享资源和协作。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: overview
ms.date: 08/05/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: cfce0031d912b1611b6810310e56241857821579
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/07/2020
ms.locfileid: "87926716"
---
# <a name="what-is-guest-user-access-in-azure-active-directory-b2b"></a>什么是 Azure Active Directory B2B 中的来宾用户访问权限？

Azure Active Directory (Azure AD) 企业到企业 (B2B) 协作是外部标识的一项功能，使你能够邀请来宾用户同组织一起协作。 使用 B2B 协作，可以安全地将公司的应用程序和服务与来自任何其他组织的来宾用户共享，同时保持对自己公司数据的控制。 与外部合作伙伴安全放心地合作，不论其规模是大是小，甚至就算他们没有 Azure AD 或 IT 部门也无妨。 合作伙伴通过一个简单的邀请和兑换过程即可使用自己的凭据来访问公司资源。 开发人员可以使用 Azure AD 企业到企业 API 自定义邀请处理或编写自助注册门户之类的应用程序。 有关与来宾用户相关的许可和定价信息，请参阅 [Azure Active Directory 定价](https://azure.microsoft.com/pricing/details/active-directory/)。  

   > [!IMPORTANT]
   > **从 2021 年 3 月 31 日起**，Microsoft 将不再支持通过创建用于 B2B 协作方案的非托管 Azure AD 帐户和租户进行邀请兑换。 在准备期间，我们鼓励客户选择参与[电子邮件一次性密码身份验证](one-time-passcode.md)。 我们欢迎你提供有关此公共预览版功能的反馈，并且很乐意创建更多的协作方式。

## <a name="collaborate-with-any-partner-using-their-identities"></a>与使用自己标识的任何合作伙伴协作

借助 Azure AD B2B，合作伙伴可使用自己的标识管理解决方案，因此组织省去了外部管理开销。 来宾用户可使用自己的工作、学校或社交标识登录应用和服务。

- 合作伙伴使用自己的标识和凭据；Azure AD 不是必需的。
- 不需要管理外部帐户或密码。
- 不需要同步帐户或管理帐户生命周期。  

## <a name="easily-invite-guest-users-from-the-azure-ad-portal"></a>从 Azure AD 门户轻松地邀请来宾用户

管理员可以在 Azure 门户中轻松地向组织添加来宾用户。

- 在 Azure AD 中[创建新的来宾用户](b2b-quickstart-add-guest-users-portal.md)，方法类似于添加新用户。
- 将来宾用户分配到应用或组。
- 发送包含兑换链接的邀请电子邮件或发送要共享的应用的直接链接。

![显示“新建来宾用户邀请”入口页的屏幕截图](media/what-is-b2b/add-a-b2b-user-to-azure-portal.png)

- 来宾用户按照几个简单的[兑换步骤](redemption-experience.md)操作即可登录。

![显示“查看权限”页的屏幕截图](media/what-is-b2b/consentscreen.png)


## <a name="use-policies-to-securely-share-your-apps-and-services"></a>使用策略安全地共享你的应用和服务

可以使用授权策略保护企业内容。 可在以下级别强制执行多重身份验证等条件访问策略：

- 租户级别。
- 应用程序级别。
- 针对特定来宾用户，保护企业应用和数据。

![显示“条件访问”选项的屏幕截图](media/what-is-b2b/tutorial-mfa-policy-2.png)



## <a name="let-application-and-group-owners-manage-their-own-guest-users"></a>让应用程序和组所有者管理自己的来宾用户

可以委托应用程序所有者管理来宾用户，不论是否为 Microsoft 应用程序，他们都可以将来宾用户直接添加到他们想要共享的任何应用程序。

- 管理员设置自助服务应用和组管理。
- 非管理员使用其[访问面板](https://myapps.microsoft.com)将来宾用户添加到应用程序或组。

![显示来宾用户的访问面板的屏幕截图](media/what-is-b2b/access-panel-manage-app.png)

## <a name="customize-the-onboarding-experience-for-b2b-guest-users"></a>自定义 B2B 来宾用户的载入体验

使用按组织需求自定义的方法引入外部合作伙伴。

- 使用 [Azure AD 权利管理](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview)配置[管理外部用户访问权限](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-external-users#how-access-works-for-external-users)的策略。
- 使用 [B2B 协作邀请 API](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation) 自定义载入体验。

## <a name="integrate-with-identity-providers"></a>与标识提供者集成

Azure AD 支持外部标识提供者，如 Facebook、Microsoft 帐户、Google 或企业标识提供者。 可以设置与标识提供者的联合，这样外部用户就能使用现有的社交或企业帐户登录，而不用专门为应用新建一个帐户。 详细了解外部标识的标识提供者。

![显示“标识提供者”页的屏幕截图](media/what-is-b2b/identity-providers.png)


## <a name="create-a-self-service-sign-up-user-flow-preview"></a>创建自助注册用户流（预览）

使用自助注册用户流，可以为要访问应用的外部用户创建注册体验。 在注册流中，可以提供不同的社交或企业标识提供者选项，并收集用户信息。 了解[自助注册及其设置方法](self-service-sign-up-overview.md)。

还可以使用 [API 连接器](api-connectors-overview.md)将自助注册用户流与外部云系统集成。 可以与自定义审批工作流连接、执行身份验证、验证用户提供的信息等。

![显示“用户流”页的屏幕截图](media/what-is-b2b/self-service-sign-up-user-flow-overview.png)
<!--TODO: Add screenshot with API connectors -->

## <a name="next-steps"></a>后续步骤

- [Azure AD B2B 协作的许可指南](licensing-guidance.md)
- [在门户中添加 B2B 协作来宾用户](add-users-administrator.md)
- [了解邀请兑换过程](redemption-experience.md)
