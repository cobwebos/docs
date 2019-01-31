---
title: Azure Active Directory B2B 协作授权指南 | Microsoft 文档
description: Azure Active Directory B2B 协作无需付费的 Azure AD 许可证，但你仍然可以为 B2B 来宾用户获取付费功能
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: mal
ms.openlocfilehash: e876c720e9cac4f05fd0776a3aba24976720fb15
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/26/2019
ms.locfileid: "55075833"
---
# <a name="azure-active-directory-b2b-collaboration-licensing-guidance"></a>Azure Active Directory B2B 协作授权指南

借助 Azure Active Directory (Azure AD) 企业到企业 (B2B) 协作可以邀请外部用户（或“来宾用户”）使用你的付费 Azure AD 服务。 对于分配给用户的每个付费 Azure AD 许可证，最多可以在“外部用户限额”下邀请五名来宾用户。

来宾用户是指不属于你所在的组织或其附属机构的用户。 来宾用户根据他们与你的组织之间的关系来定义，而不是根据他们登录时所用的凭据来定义。 事实上，来宾用户可以使用外部标识或者使用你的组织拥有的凭据进行登录。

以下用户不是来宾用户：
- 你的员工、现场合同工或现场代理
- 附属机构的员工、现场合同工或现场代理

B2B 来宾用户许可按照 5:1 的比例自动计算和报告。 目前，无法直接向来宾用户分配 B2B 来宾用户许可证。

在某些情况下，不会使用 1:5 的外部用户限额来报告来宾用户。 如果某个来宾用户在其自己的组织中拥有付费的 Azure AD 许可证，该用户不会消耗你的一个 B2B 来宾用户许可证。 此外，来宾用户可以使用免费的 Azure AD 功能，而不用满足附加的许可要求。 即使你没有任何付费的 Azure AD 许可证，来宾用户也有权访问免费的 Azure AD 功能。 

## <a name="examples-calculating-guest-user-licenses"></a>示例：计算来宾用户许可证数
确定有多少个来宾用户需要访问付费的 Azure AD 服务之后，请确保提供足够数量的 Azure AD 付费许可证，以按所需的 1:5 比例涵盖来宾用户。 下面是一些示例：

- 你想要邀请 100 个来宾用户使用你的 Azure AD 应用或服务，并想要向所有来宾用户分配访问管理和预配权限。 此外，你还希望其中的 50 个来宾用户执行 MFA 和条件访问。 若要涵盖这种组合，需要 10 个 Azure AD Basic 许可证和 10 个 Azure AD Premium P1 许可证。 如果你打算对来宾用户使用 Identity Protection 功能，需要按相同的 1:5 比例提供 Azure AD Premium P2 许可证才能涵盖这些来宾用户。
- 你想要邀请 60 个来宾用户，而这些用户都需要执行 MFA，因此，必须至少有 12 个 Azure AD Premium P1 许可证。 10 名员工有 Azure AD Premium P1 许可证，因此，根据 1:5 的许可比例，最多允许 50 个来宾用户。 需要额外购买两个 Premium P1 许可证才能涵盖 10 个附加的来宾用户。

## <a name="using-the-b2b-collaboration-api-to-invite-users-from-your-affiliates"></a>使用 B2B 协作 API 从附属机构邀请用户

根据定义，B2B 来宾用户是你邀请使用付费 Azure AD 应用和服务的外部用户。 即使使用了 B2B 功能，你所在公司或附属机构之一的员工、现场合同工或现场代理也不符合 B2B 协作的条件。 下面是一些示例： 
- 你想要使用外部凭据（例如社交标识）来邀请在你的组织任职的用户。 此方案不符合许可要求，且不受允许。 外部凭据不能将员工变成外部用户。  
- 你想要使用 B2B API 从你所在组织的附属机构之一邀请用户。 尽管此方案使用 B2B API 邀请用户，但不被视为 B2B 协作。 它不符合许可要求，因为附属机构的用户不是外部用户。 

在这两个方案中，更好的解决方案是使用 B2B API 将用户作为成员来邀请 (invitedUserType = Member)，并为每个用户分配一个 Azure AD 许可证。 

## <a name="next-steps"></a>后续步骤

请参阅以下有关 Azure AD B2B 协作的资源：

* [什么是 Azure AD B2B 协作？](what-is-b2b.md)
* [Azure Active Directory B2B 协作常见问题 (FAQ)](faq.md)
