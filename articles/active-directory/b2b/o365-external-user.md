---
title: Office 365 外部共享与 Azure Active Directory B2B 协作 | Microsoft 文档
description: 讨论使用 O365 和 Azure Active Directory B2B 协作与外部合作伙伴共享资源。
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/24/2017
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: d55d587022b763a6890c098dd0a1b9bef2a3b7fb
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/17/2018
ms.locfileid: "34266972"
---
# <a name="office-365-external-sharing-and-azure-active-directory-b2b-collaboration"></a>Office 365 外部共享与 Azure Active Directory B2B 协作

Office365 中的外部共享（OneDrive、SharePoint Online、统一组等）和 Azure Active Directory (Azure AD) B2B 协作从技术上讲是相同的操作。 包括 Office 365 组中的来宾在内的所有外部共享（OneDrive/SharePoint Online 除外）已使用 Azure AD B2B 协作邀请 API 进行共享。

OneDrive/SharePoint Online 具有单独的邀请管理器。 在 Azure AD 开发其外部共享支持之前，OneDrive/SharePoint Online 中已开始支持外部共享。 随着时间推移，OneDrive/SharePoint Online 外部共享已积累了多个功能和使用该产品的内置共享模式的数百万名用户。 但是，OneDrive/SharePoint Online 外部共享的工作方式与 Azure AD B2B 协作的工作方式之间有一些细微的差异：

- OneDrive/SharePoint Online 在用户兑换其邀请后将用户添加到目录中。 因此，在兑换之前，在 Azure AD 门户中看不到用户。 如果另一个站点在此期间邀请了用户，会生成一个新的邀请。 但是，使用 Azure AD B2B 协作时，在邀请时会立即添加用户，以便他们显示在任何位置。

- OneDrive/SharePoint Online 中的兑换体验看起来不同于 Azure AD B2B 协作中的体验。 在用户兑换邀请后，体验看起来相似。

- 可以从 OneDrive/SharePoint Online 共享对话框选取 Azure AD B2B 协作邀请的用户。 OneDrive/SharePoint Online 邀请的用户在他们兑换其邀请后也会显示在 Azure AD 中。

- 若要通过 Azure AD B2B 协作管理 OneDrive/SharePoint Online 中的外部共享，请将 OneDrive/SharePoint Online 外部共享设置设为“仅允许与已在目录中的外部用户共享”。 用户可以转到外部共享站点，从管理员已添加的外部协作者中进行选取。 管理员可以通过 B2B 协作邀请 API 添加外部协作者。

![OneDrive/SharePoint Online 外部共享设置](media/o365-external-user/odsp-sharing-setting.png)

## <a name="next-steps"></a>后续步骤

* [什么是 Azure AD B2B 协作？](what-is-b2b.md)
* [将 B2B 协作用户添加到角色](add-guest-to-role.md)
* [委托 B2B 协作邀请](delegate-invitations.md)
* [动态组和 B2B 协作](use-dynamic-groups.md)