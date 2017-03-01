---
title: "将 Azure Active Directory B2B 协作用户添加到角色 | Microsoft 文档"
description: "Azure Active Directory B2B 协作用户属性是可配置的"
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
ms.date: 02/03/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: d3f68340592d9032999ecb5cc16ac1fedcce4c33
ms.openlocfilehash: 9ba46fe5ce6f06a69b057ebcb4af072689ac8ce9


---

# <a name="adding-an-azure-active-directory-b2b-collaboration-user-to-a-role"></a>将 Azure Active Directory B2B 协作用户添加到角色

虽然 Azure Active Directory (Azure AD) B2B 协作用户已作为来宾用户添加到目录，但默认情况下目录中的来宾权限受到限制，你的企业可能需要某些来宾用户填充你的组织中的更多权限角色。 若要支持此功能，可以根据组织需要将来宾用户添加到所需的任何角色。

## <a name="default-role"></a>默认角色

![默认角色](media/active-directory-b2b-add-guest-to-role/default-role.png)

## <a name="global-administrator-role"></a>全局管理员角色

![全局管理员角色](media/active-directory-b2b-add-guest-to-role/global-admin-role.png)

## <a name="limited-administrator-role"></a>受限制的管理员角色

![受限制的管理员角色](media/active-directory-b2b-add-guest-to-role/limited-admin-role.png)

## <a name="auditing-and-reporting"></a>审核和报告
使用来宾用户，现在可以获得与成员用户同等的审核功能。 下面是刚受邀的 Sam Oogle 的邀请和兑换历史记录的示例：

![审核日志](media/active-directory-b2b-add-guest-to-role/audit-log.png)

用户可以深入了解上述每个事件，以获取详细信息。 例如，让我们看一下验收详细信息。

![活动详细信息](media/active-directory-b2b-add-guest-to-role/activity-details.png)

还可以从 Azure AD 导出这些日志，并使用所选报表工具以获取自定义报表。

## <a name="resending-invitations"></a>重新发送邀请
现在可以转到 B2B 协作用户的配置文件页，重新向由于任何原因尚未兑换的来宾用户发送邀请：

![重新发送邀请](media/active-directory-b2b-add-guest-to-role/resend-invitation.png)

> ![注意] 这将从已登录用户重新发送邀请并邀请用户加入目录，即使原始邀请是发到特定应用或组，也是如此。

## <a name="next-steps"></a>后续步骤

在 Azure AD B2B 协作网站上浏览我们的其他文章：

* [什么是 Azure AD B2B 协作？](active-directory-b2b-what-is-azure-ad-b2b.md)
* [B2B 协作用户属性](active-directory-b2b-user-properties.md)
* [委托 B2bB 协作邀请](active-directory-b2b-delegate-invitations.md)
* [动态组和 B2B 协作](active-directory-b2b-dynamic-groups.md)
* [B2B 协作代码和 PowerShell 示例](active-directory-b2b-code-samples.md)
* [为 B2B 协作配置 SaaS 应用](active-directory-b2b-configure-saas-apps.md)
* [B2B 协作用户令牌](active-directory-b2b-user-token.md)
* [B2B 协作用户声明映射](active-directory-b2b-claims-mapping.md)
* [Office 365 外部共享](active-directory-b2b-o365-external-user.md)
* [B2B 协作当前限制](active-directory-b2b-current-limitations.md)



<!--HONumber=Feb17_HO1-->


