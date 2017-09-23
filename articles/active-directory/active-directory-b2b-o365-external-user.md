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
ms.date: 05/24/2017
ms.author: sasubram
ms.translationtype: Human Translation
ms.sourcegitcommit: ef74361c7a15b0eb7dad1f6ee03f8df707a7c05e
ms.openlocfilehash: cad0ce8f745f3d6ca14436fd714b08c60de0e459
ms.contentlocale: zh-cn
ms.lasthandoff: 05/25/2017

---

# <a name="office-365-external-sharing-and-azure-active-directory-b2b-collaboration"></a>Office 365 外部共享与 Azure Active Directory B2B 协作

Office365 中的外部共享（OneDrive、SharePoint Online、统一组等）和 Azure Active Directory (Azure AD) B2B 协作从技术上讲是相同的操作。 包括 Office 365 组中的来宾在内的所有外部共享（OneDrive/SharePoint Online 除外）已使用 Azure AD B2B 协作邀请 API 进行共享。

OneDrive/SharePoint Online 具有单独的邀请管理器。 在 Azure AD 开发其外部共享支持之前，OneDrive/SharePoint Online 中已开始支持外部共享。 随着时间推移，OneDrive/SharePoint Online 外部共享已积累了多个功能和使用该产品的内置共享模式的数百万名用户。 但是，OneDrive/SharePoint Online 外部共享的工作方式与 Azure AD B2B 协作的工作方式之间有一些细微的差异：

- OneDrive/SharePoint Online 在用户兑换其邀请后将用户添加到目录中。 因此，在兑换之前，你在 Azure AD 门户中看不到用户。 如果另一个站点在此期间邀请了用户，将会生成一个新的邀请。 但是，使用 Azure AD B2B 协作时，在邀请时会立即添加用户，以便他们显示在任何位置。

- OneDrive/SharePoint Online 中的兑换体验看起来不同于 Azure AD B2B 协作中的体验。 在用户兑换邀请后，体验看起来相似。

- 可以从 OneDrive/SharePoint Online 共享对话框选取 Azure AD B2B 协作邀请的用户。 OneDrive/SharePoint Online 邀请的用户在他们兑换其邀请后也会显示在 Azure AD 中。

- 若要通过 Azure AD B2B 协作管理 OneDrive/SharePoint Online 中的外部共享，请将 OneDrive/SharePoint Online 外部共享设置设为“仅允许与已在目录中的外部用户共享”。 用户可以转到外部共享站点，从管理员已添加的外部协作者中进行选取。 管理员可以通过 B2B 协作邀请 API 添加外部协作者。

![OneDrive/SharePoint Online 外部共享设置](media/active-directory-b2b-o365-external-user/odsp-sharing-setting.png)

## <a name="next-steps"></a>后续步骤

在 Azure AD B2B 协作网站上浏览我们的其他文章：

* [什么是 Azure AD B2B 协作？](active-directory-b2b-what-is-azure-ad-b2b.md)
* [B2B 协作用户属性](active-directory-b2b-user-properties.md)
* [向角色添加 B2B 协作用户](active-directory-b2b-add-guest-to-role.md)
* [委托 B2B 协作邀请](active-directory-b2b-delegate-invitations.md)
* [动态组和 B2B 协作](active-directory-b2b-dynamic-groups.md)
* [B2B 协作代码和 PowerShell 示例](active-directory-b2b-code-samples.md)
* [为 B2B 协作配置 SaaS 应用](active-directory-b2b-configure-saas-apps.md)
* [B2B 协作用户令牌](active-directory-b2b-user-token.md)
* [B2B 协作用户声明映射](active-directory-b2b-claims-mapping.md)
* [B2B 协作当前限制](active-directory-b2b-current-limitations.md)

