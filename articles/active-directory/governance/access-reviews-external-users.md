---
title: 使用 Azure AD Identity Governance 查看和删除不再具有资源访问权限的外部用户
description: 使用访问评审来扩展合作伙伴组织成员的删除访问权限
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 09/06/2020
ms.author: barclayn
ms.openlocfilehash: 64ff2a2a7ad6f07aac959422eadec7f24b210d88
ms.sourcegitcommit: 59ea8436d7f23bee75e04a84ee6ec24702fb2e61
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2020
ms.locfileid: "89505802"
---
# <a name="use-azure-active-directory-azure-ad-identity-governance-to-review-and-remove-external-users-who-no-longer-have-resource-access"></a>使用 Azure Active Directory (Azure AD) 标识调控来查看和删除不再具有资源访问权限的外部用户

本文介绍了一些功能和方法，通过这些功能和方法，你可以查找和选择外部标识，以便你可以查看它们，并在不再需要时从 Azure AD 中将它们删除。 云使用户能够比以往更轻松地与内部或外部用户协作。 使用 Office 365，组织可以了解 (包括来宾) 的外部标识激增，因为用户可将数据、文档或数字工作区（如团队）一起工作。 组织需要权衡，实现协作并满足安全和监管要求。 其中的部分工作应包括评估和清理外部用户、受邀加入你的租户、从合作伙伴组织，以及在不再需要时将其从 Azure AD 中移除。

>[!NOTE]
>使用 Azure AD 访问评审需要有效的 Azure AD Premium P2、企业移动性 + 安全性 E5 付费版或试用版许可证。 有关详细信息，请参阅 [Azure Active Directory 版本](../fundamentals/active-directory-whatis.md)。

## <a name="why-review-users-from-external-organizations-in-your-tenant"></a>为什么要查看租户中的外部组织的用户？

在大多数组织中，最终用户会开始邀请业务合作伙伴和供应商进行协作。 需要对组织进行协作，以便为资源所有者和最终用户提供一种方法来定期评估和证明外部用户。 通常，加入新协作合作伙伴的过程是计划的，但在许多协作中没有明显的结束日期，当用户不再需要访问权限时，并不总是很明显。 同样，标识生命周期管理推动企业 Azure AD 清理和删除不再需要访问组织资源的用户。 在目录中仅保留合作伙伴和供应商的相关标识引用有助于降低员工的风险，并无意选择并授予应删除的外部用户的访问权限。 本文档将指导你完成多个选项，这些选项包括从建议的前瞻性建议到被动和清理活动，以管理外部标识。

## <a name="use-entitlement-management-to-grant-and-revoke-access"></a>使用权利管理授予和撤销访问权限

使用权限管理功能可以实现 [外部标识的自动生命周期](entitlement-management-external-users.md#manage-the-lifecycle-of-external-users) ，并可访问资源。 通过建立用于通过授权管理管理访问权限的过程和过程，并通过访问包发布资源，跟踪外部用户对资源的访问，从而使问题变得不太复杂。 在 Azure AD 中通过授权 [管理访问包](entitlement-management-overview.md) 管理访问权限时，你的组织可以集中定义和管理用户的访问权限，以及来自合作伙伴组织的用户。 权利管理使用访问包的批准和分配来跟踪外部用户已请求并被分配访问的位置。 如果外部用户丢失了其所有分配，则权限管理可以从租户自动删除这些外部用户。 

## <a name="find-guests-not-invited-through-entitlement-management"></a>查找未通过授权管理邀请的来宾

当员工有权与外部用户合作时，他们可能会邀请组织外部的任何数量的用户。 查找外部合作伙伴并将其分组为公司对齐的动态组并对其进行检查可能不可行，因为可能有太多不同的公司要查看，或者没有组织的所有者或主办方。 Microsoft 提供了一个示例 PowerShell 脚本，可帮助你分析租户中的外部标识的使用情况。 此脚本将枚举外部标识并对其进行分类。 此脚本可帮助你识别并清除不再需要的外部标识。 作为脚本的输出的一部分，脚本示例支持自动创建包含已确定的无组外部合作伙伴的安全组，以便进一步分析和使用 Azure AD 访问评审。
[GitHub](https://github.com/microsoft/access-reviews-samples/tree/master/ExternalIdentityUse)上提供了该脚本。 脚本运行完毕后，将生成一个 HTML 输出文件，其中概述了以下内容的外部标识：

- 租户中不再有任何组成员身份
- 为租户中的特权角色分配了分配
- 已分配给租户中的应用程序

输出还包含每个外部标识的各个域。 

>[!NOTE]
>上面引用的脚本是一个示例脚本，用于在 Azure AD 中检查组成员身份、角色分配和应用程序分配。 外部用户可能会在 Azure AD 以外的其他应用程序中收到其他分配，如 SharePoint (直接成员身份分配) 或 Azure RBAC 或 Azure DevOps。

## <a name="review-resources-used-by-external-identities"></a>查看外部标识使用的资源

如果你具有使用团队或其他不受授权管理管理的应用程序等资源的外部标识，你可能需要定期查看对这些资源的访问权限。 Azure AD [访问评审](create-access-review.md) 使你能够查看外部标识的访问权限：允许资源所有者、外部标识本身，或信任证明的其他委托人员是否需要继续访问它。 访问评审对资源进行定位，并创建一种查看活动，该活动的作用域为只能访问资源或来宾用户的每个用户。 然后，审阅者将看到他们需要检查的用户的结果列表–所有用户（包括你的组织的员工或仅限外部标识）。

![使用组查看访问权限](media/access-reviews-external-users/group-members.png)

建立资源所有者驱动的评审区域性有助于管理外部标识的访问。 资源所有者对他们所拥有的信息的访问、可用性和安全性有责任，在大多数情况下，您可以通过这些用户来推动他们对其资源的访问决策，并且更接近于访问这些资源的用户或管理许多外部对象的主办方。

## <a name="create-access-reviews-for-external-identities"></a>为外部标识创建访问评审

如果用户不再与你的组织合作，则可以删除不再对你的租户中的任何资源具有访问权限的用户。 在阻止和删除这些外部标识之前，你可能想要与这些外部用户联系，并确保你没有忽视某个项目，或他们仍需要拥有的访问权限。 当你创建的组包含所有外部标识（因为你发现的成员无法访问租户中的任何资源）时，你可以使用访问评审来使所有外部人员都能自行证实他们是否仍需要或具有访问权限，或者在将来仍将需要访问权限。 作为检查的一部分，访问评审中的评审创建者可以使用 " **需要使用批准的原因** " 功能来要求外部用户提供持续访问的理由，通过该功能，你可以了解在租户中其仍需要访问的位置和方式。 此外，你还可以启用 " **为审阅者电子邮件设置附加内容** " 功能，以让用户知道他们在没有响应的情况下将失去访问权限，并且如果他们仍需要访问，则需要进行调整。 如果你想要继续并允许访问评审 **禁用和删除** 外部标识，则在这些标识无法响应或无法提供持续访问的有效原因后，你可以使用 "禁用" 和 "删除" 选项，如下一节中所述。

![仅将评审的范围限制为来宾用户](media/access-reviews-external-users/guest-users-only.png)

评审完成后，" **结果** " 页将显示每个外部标识提供的响应的概述。 你可以选择自动应用结果，并允许访问评审禁用和删除它们。 或者，你可以查看给定的响应，并决定是要删除用户的访问权限还是跟进，并在做出决定之前获取其他信息。 如果某些用户仍有权访问你尚未查看的资源，你可以使用该审阅作为你的发现的一部分，并丰富你的下一审查和证明周期。

## <a name="disable-and-delete-external-identities-with-azure-ad-access-reviews-preview"></a>使用 Azure AD 访问评审 (预览版禁用和删除外部标识) 

除了从资源（如组或应用程序）中删除不需要的外部标识以外，Azure AD 访问评审可能会阻止外部标识登录租户，并在30天后删除租户中的外部标识。

![完成设置后](media/access-reviews-external-users/upon-completion-settings.png)

在创建新的访问评审时，在 "完成时设置" 部分中，对于 "要对已 **拒绝用户应用的操作** "，可以定义 " **阻止用户登录30天"，然后从租户中删除用户**。
此设置（当前为预览版）可用于识别、阻止和删除 Azure AD 租户中的外部标识。 被审阅者查看和拒绝的外部标识将被阻止和删除，而不考虑其拥有的资源访问权限或组成员身份。 验证外部用户是否不再具有资源访问权限，并且可以安全地从你的租户中删除，或者如果你想要确保删除这些用户，而不考虑它们的持续访问权限，则最好使用此设置。 "禁用和删除" 功能首先阻止外部用户登录到租户并访问资源的能力。 不会在此阶段撤销资源访问权限，如果想要化外部用户，则可以重新配置其登录功能。 如果没有进一步的操作，将在30天后从目录中删除被阻止的外部标识，同时删除该帐户以及他们的访问权限。

## <a name="next-steps"></a>后续步骤

- [访问评审 - Graph API](/graph/api/resources/accessreviews-root?view=graph-rest-beta)
- [权利管理 - Graph API](/graph/api/resources/entitlementmanagement-root?view=graph-rest-beta)