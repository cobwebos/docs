---
title: 什么是 Azure Active Directory B2B 协作？ | Microsoft Docs
description: Azure Active Directory B2B 协作可让业务合作伙伴有选择性地访问本方的企业应用程序，为跨公司合作关系提供支持。
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 04/26/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 2bc405e6356113e0423f833868c86890c0c3d5d2
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/17/2018
ms.locfileid: "34259485"
---
# <a name="what-is-azure-ad-b2b-collaboration"></a>什么是 Azure AD B2B 协作？

借助 Azure Active Directory (Azure AD) 企业到企业 (B2B) 协作功能，任何使用 Azure AD 的组织都能够安全可靠地与来自任何其他组织的用户协同工作（无论这些组织的规模大小）。 这些组织可以使用也可以不使用 Azure AD，甚至不需要 IT 部门。

使用 Azure AD 的组织可以向其合作伙伴提供文档、资源和应用程序的访问权限，同时保持对自己企业数据的完全控制。 开发人员可使用 Azure AD 企业到企业 API 编写应用程序，以更安全的方式将两个组织凝聚在一起。 此外，最终用户还可轻松地进行导航。

以下视频提供了有用的概述。
>[!VIDEO https://www.youtube.com/embed/AhwrweCBdsc]

## <a name="key-benefits-of-azure-ad-b2b-collaboration"></a>Azure AD B2B 协作的主要优势

### <a name="work-with-any-user-from-any-partner"></a>与来自任意合作伙伴的任意用户合作

- 合作伙伴使用其自己的凭据
- 合作伙伴无需使用 Azure AD
- 无需任何外部目录或进行任何复杂设置

### <a name="simple-and-secure-collaboration"></a>协作简单安全

- 提供对任何企业应用或数据的访问权限，同时应用 Azure AD 支持的高级授权策略
- 方便用户
- 应用和数据的企业级安全性

### <a name="no-management-overhead"></a>无管理开销

- 无需外部帐户或密码管理
- 无需同步或手动的帐户生命周期管理
- 无外部管理开销

## <a name="easily-add-b2b-collaboration-users"></a>轻松添加 B2B 协作用户

作为管理员，你可以在 [Azure 门户](https://portal.azure.com)中将 B2B 协作（来宾）用户轻松添加到你的组织。

![添加来宾用户](media/what-is-b2b/adding-b2b-users-admin.png)

### <a name="enable-your-collaborators-to-bring-their-own-identity"></a>允许协作者自带标识

B2B 协作者可以使用自己选择的标识登录。 如果用户没有 Microsoft 帐户或和 Azure AD 帐户，则会在提供兑换时为其无缝创建一个帐户。

### <a name="delegate-to-application-and-group-owners"></a>委托给应用程序和组所有者

作为应用程序或组所有者，你可以直接将 B2B 用户添加到所关心的任何应用程序，无论是否为 Microsoft 应用程序。 管理员可向非管理员委托添加 B2B 用户的权限。 这样，非管理员即可使用 [Azure AD 应用程序访问面板](https://myapps.microsoft.com)将 B2B 协作用户添加到应用程序或组。

![访问面板](media/what-is-b2b/access-panel.png)

![添加成员](media/what-is-b2b/add-member.png)

### <a name="authorization-policies-protect-your-corporate-content"></a>授权策略保护企业内容

可通过以下级别强制执行多重身份验证等条件访问策略：
- 租户级别
- 应用程序级别
- 针对特定用户，保护企业应用和数据

### <a name="use-apis-and-sample-code-to-easily-build-applications-to-onboard"></a>使用 API 和示例代码轻松生成要载入的应用程序

使用按组织需求自定义的方法引入外部合作伙伴。

使用 [B2B 协作邀请 API](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation)，可自定义载入体验，包括创建自助服务注册门户。 我们在 [Github](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web) 上提供自助服务门户的示例代码。

![注册门户](media/what-is-b2b/sign-up-portal.png)

通过 Azure AD B2B 协作，可以获得 Azure AD 的全部功能，以最终用户认为简单且直观的方式来保护合作伙伴关系。

## <a name="next-steps"></a>后续步骤

- [Azure Active Directory 管理员如何添加 B2B 协作用户？](add-users-administrator.md)
- [信息工作者如何添加 B2B 协作用户？](add-users-information-worker.md)
- [B2B 协作邀请兑换](redemption-experience.md)
- [Azure AD B2B 协作授权](licensing-guidance.md)
- 此外，还可与往常一样通过 [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b)（Microsoft 技术社区）与产品团队联系，获取任何反馈、讨论和建议。