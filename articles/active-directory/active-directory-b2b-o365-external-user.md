---
title: "Office 365 外部共享与 Azure Active Directory B2B 协作 | Microsoft 文档"
description: "Azure Active Directory B2B 协作的声明映射参考"
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
ms.date: 02/06/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: d3f68340592d9032999ecb5cc16ac1fedcce4c33
ms.openlocfilehash: 716d19be09085db91c17d7ed76422e05d164d137


---

# <a name="office-365-external-sharing-and-azure-active-directory-b2b-collaboration"></a>Office 365 外部共享与 Azure Active Directory B2B 协作

Office365 中的外部共享（OneDrive、SharePoint Online、统一组等）和使用 Azure Active Directory (Azure AD) B2B 协作从技术上讲是相同的操作。 包括**统一组中的来宾**在内的所有外部共享（OneDrive/SharePoint Online 除外）已在使用 Azure AD B2B 协作邀请 API 进行共享。

OneDrive/SharePoint Online (ODSP) 具有单独的邀请管理器，因为对外部共享的 ODSP 支持在其作为 Azure AD 结构的一部分受到内在支持之前已开始。 随着时间推移，ODSP 外部共享已具有多个功能和使用该产品的内置共享模式的数百万名用户。 我们正在使用 ODSP 加入到 Azure AD B2B 邀请 API（在本文档中有提及），以统一所有端到端优点，并将其积累到 Azure AD 正在进行的有关体验的所有创新。 同时，ODSP 外部共享的工作原理与 Azure AD B2B 的工作原理之间有一些细微的差异：

1. ODSP 在用户兑换其邀请后，将用户添加到目录中。 因此，在用户兑换之前，你不会在 Azure AD 门户中实际看到该用户。 如果同时从另一个站点邀请用户，将会生成一个新的邀请。 但是，使用 B2B 协作时，我们在邀请时将立即添加用户，因此该用户会显示在所有位置中。

2. ODSP 中的兑换体验看起来与 B2B 协作不同。

3. 但是，在用户兑换邀请后，它们看起来一样。

4. B2B 协作邀请的用户可以从 ODSP 共享对话框选取。 ODSP 邀请的用户在兑换其邀请后还会显示在 Azure AD 中。

5. 若要以管理员控制的托管方式将 ODSP 中的外部共享与 B2B 协作一起使用，请将 ODSP 外部共享设置为“仅允许与已在目录中的外部用户共享”。 用户可以转到外部共享站点，从管理员已添加的外部协作者中选取。 管理员可以通过 B2B 协作邀请 API 添加外部协作者。

![ODSP 外部共享设置](media/active-directory-b2b-o365-external-user/odsp-sharing-setting.png)

## <a name="next-steps"></a>后续步骤

在 Azure AD B2B 协作网站上浏览我们的其他文章：

* [什么是 Azure AD B2B 协作？](active-directory-b2b-what-is-azure-ad-b2b.md)
* [B2B 协作用户属性](active-directory-b2b-user-properties.md)
* [向角色添加 B2B 协作用户](active-directory-b2b-add-guest-to-role.md)
* [委托 B2bB 协作邀请](active-directory-b2b-delegate-invitations.md)
* [动态组和 B2B 协作](active-directory-b2b-dynamic-groups.md)
* [B2B 协作代码和 PowerShell 示例](active-directory-b2b-code-samples.md)
* [为 B2B 协作配置 SaaS 应用](active-directory-b2b-configure-saas-apps.md)
* [B2B 协作用户令牌](active-directory-b2b-user-token.md)
* [B2B 协作用户声明映射](active-directory-b2b-claims-mapping.md)
* [B2B 协作当前限制](active-directory-b2b-current-limitations.md)



<!--HONumber=Feb17_HO1-->


