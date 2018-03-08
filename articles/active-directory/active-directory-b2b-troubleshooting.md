---
title: "Azure Active Directory B2B 协作故障排除 | Microsoft 文档"
description: "Azure Active Directory B2B 协作的常见问题的补救措施"
services: active-directory
documentationcenter: 
author: twooley
manager: mtillman
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 05/25/2017
ms.author: twooley
ms.reviewer: sasubram
ms.openlocfilehash: 588e154d35fda539ac6ee8803ed96e6cd9a3d1df
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2018
---
# <a name="troubleshooting-azure-active-directory-b2b-collaboration"></a>Azure Active Directory B2B 协作故障排除

以下是 Azure Active Directory (Azure AD) B2B 协作的常见问题的一些补救措施。


## <a name="ive-added-an-external-user-but-do-not-see-them-in-my-global-address-book-or-in-the-people-picker"></a>我已添加外部用户，但在全局通讯簿或人员选取器中看不到这些用户

在外部用户未填充到列表中的情况下，可能需要几分钟复制对象。

## <a name="a-b2b-guest-user-is-not-showing-up-in-sharepoint-onlineonedrive-people-picker"></a>B2B 来宾用户没有显示在 SharePoint Online/OneDrive 人员选取器中 
 
默认情况下，搜索现有来宾用户的功能在 SharePoint Online (SPO) 人员选取器中处于“关闭”状态以匹配旧行为。

可使用“ShowPeoplePickerSuggestionsForGuestUsers”设置在租户和网站集级别启用此功能。 可使用 Set-SPOTenant 和 Set-SPOSite cmdlet 设置此功能，这将允许用户搜索目录中的所有现有来宾用户。 租户范围中的更改不会影响已经预配的 SPO 站点。

## <a name="invitations-have-been-disabled-for-directory"></a>已对目录禁用邀请

如果收到无权邀请用户的通知，请在“用户设置”下验证用户帐户是否有权邀请外部用户：

![](media/active-directory-b2b-troubleshooting/external-user-settings.png)

如果最近修改了这些设置或为用户分配了“来宾邀请者”角色，可能有 15-60 分钟的延迟更改才生效。

## <a name="the-user-that-i-invited-is-receiving-an-error-during-redemption"></a>我邀请的用户在兑换过程中收到错误

常见错误包括：

### <a name="invitees-admin-has-disallowed-emailverified-users-from-being-created-in-their-tenant"></a>当发生以下情况时，被邀请者的管理员禁止在其租户中创建电子邮件验证的用户

受邀用户所在组织正在使用 Azure Active Directory，但其中不存在特定用户帐户（例如，用户不存在于 AAD contoso.comAzure AD contoso.com 中）。 contoso.com 的管理员可能会设置一个策略以阻止创建用户。 用户必须向其管理员进行核实以确定是否允许外部用户。 外部用户的管理员可能需要在其域中允许电子邮件验证的用户（请参阅有关允许电子邮件验证的用户的此[文章](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)）。

![](media/active-directory-b2b-troubleshooting/allow-email-verified-users.png)

### <a name="external-user-does-not-exist-already-in-a-federated-domain"></a>外部用户尚未存在于联合域中

如果使用联合身份验证，并且用户在 Azure Active Directory 中不存在，则无法邀请该用户。

要解决此问题，外部用户的管理员必须将该用户的帐户同步到 Azure Active Directory。

## <a name="how-does--which-is-not-normally-a-valid-character-sync-with-azure-ad"></a>“\#”（这通常不是有效字符）如何与 Azure AD 进行同步？

由于受邀帐户 user@contoso.com 变为 user_contoso.com#EXT@fabrikam.onmicrosoft.com，“\#”是 Azure AD B2B 协作或外部用户的 UPN 中的保留字符。因此，不允许来自本地的 UPN 中的 \# 登录到 Azure 门户。 

## <a name="i-receive-an-error-when-adding-external-users-to-a-synchronized-group"></a>我将外部用户添加到同步组时，收到错误

外部用户只能添加到“已分配”或“安全”组，而不能分配到在本地控制的组。

## <a name="my-external-user-did-not-receive-an-email-to-redeem"></a>我的外部用户未收到用于兑换的电子邮件

被邀请者应该向其 ISP 或垃圾邮件筛选器查询，以确保允许以下地址：Invites@microsoft.com

## <a name="i-notice-that-the-custom-message-does-not-get-included-with-invitation-messages-at-times"></a>我发现邀请消息有时不包含自定义消息

为遵守隐私法规，在以下情况下，API 不会在电子邮件邀请中包含自定义邮件：

- 邀请方在邀请租户中没有电子邮件地址
- 应用服务主体发送邀请

如果此方案非常重要，则可取消 API 邀请电子邮件，并通过所选的电子邮件机制发送邮件。 请咨询所属组织的法律顾问，确保通过这种方式发送的任何电子邮件均符合隐私法规。

## <a name="next-steps"></a>后续步骤

在 Azure AD B2B 协作网站上浏览我们的其他文章：

* [什么是 Azure AD B2B 协作？](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Azure Active Directory 管理员如何添加 B2B 协作用户？](active-directory-b2b-admin-add-users.md)
* [信息工作者如何添加 B2B 协作用户？](active-directory-b2b-iw-add-users.md)
* [B2B 协作邀请电子邮件的元素](active-directory-b2b-invitation-email.md)
* [B2B 协作邀请兑换](active-directory-b2b-redemption-experience.md)
* [Azure AD B2B 协作授权](active-directory-b2b-licensing.md)
* [Azure Active Directory B2B 协作常见问题 (FAQ)](active-directory-b2b-faq.md)
* [Azure Active Directory B2B 协作 API 和自定义](active-directory-b2b-api.md)
* [适用于 B2B 协作用户的多重身份验证](active-directory-b2b-mfa-instructions.md)
* [在没有邀请的情况下添加 B2B 协作用户](active-directory-b2b-add-user-without-invite.md)
* [有关 Azure Active Directory 中应用程序管理的文章索引](active-directory-apps-index.md)
