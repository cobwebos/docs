---
title: B2B 协作故障排除-Azure Active Directory |Microsoft Docs
description: Azure Active Directory B2B 协作的常见问题的补救措施
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: troubleshooting
ms.date: 11/12/2019
tags: active-directory
ms.author: mimart
author: v-miegge
manager: dcscontentpm
ms.reviewer: mal
ms.custom:
- it-pro
- seo-update-azuread-jan"
ms.collection: M365-identity-device-management
ms.openlocfilehash: d970bbc5619bc25a1cf5d6abbdcf41fb4a0ae196
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2020
ms.locfileid: "77195515"
---
# <a name="troubleshooting-azure-active-directory-b2b-collaboration"></a>Azure Active Directory B2B 协作故障排除

以下是 Azure Active Directory (Azure AD) B2B 协作的常见问题的一些补救措施。

## <a name="ive-added-an-external-user-but-do-not-see-them-in-my-global-address-book-or-in-the-people-picker"></a>我已添加外部用户，但在全局通讯簿或人员选取器中看不到这些用户

在外部用户未填充到列表中的情况下，可能需要几分钟复制对象。

## <a name="a-b2b-guest-user-is-not-showing-up-in-sharepoint-onlineonedrive-people-picker"></a>B2B 来宾用户没有显示在 SharePoint Online/OneDrive 人员选取器中

默认情况下，搜索现有来宾用户的功能在 SharePoint Online (SPO) 人员选取器中处于“关闭”状态以匹配旧行为。

可使用“ShowPeoplePickerSuggestionsForGuestUsers”设置在租户和网站集级别启用此功能。 可使用 Set-SPOTenant 和 Set-SPOSite cmdlet 设置此功能，这将允许用户搜索目录中的所有现有来宾用户。 租户范围中的更改不会影响已经预配的 SPO 站点。

## <a name="invitations-have-been-disabled-for-directory"></a>已对目录禁用邀请

如果通知你无权邀请用户，请验证你的用户帐户是否有权邀请外部用户 Azure Active Directory > 用户设置 > 外部用户 > 管理外部协作设置：

![显示外部用户设置的屏幕截图](media/troubleshoot/external-user-settings.png)

如果最近修改了这些设置或为用户分配了“来宾邀请者”角色，可能有 15-60 分钟的延迟更改才生效。

## <a name="the-user-that-i-invited-is-receiving-an-error-during-redemption"></a>我邀请的用户在兑换过程中收到错误

常见错误包括：

### <a name="invitees-admin-has-disallowed-emailverified-users-from-being-created-in-their-tenant"></a>当发生以下情况时，被邀请者的管理员禁止在其租户中创建电子邮件验证的用户

受邀用户所在组织正在使用 Azure Active Directory，但其中不存在特定用户帐户（例如，用户不存在于 AAD contoso.comAzure AD contoso.com 中）。 contoso.com 的管理员可能会设置一个策略以阻止创建用户。 用户必须向其管理员进行核实以确定是否允许外部用户。 外部用户的管理员可能需要在其域中允许电子邮件验证的用户（请参阅有关允许电子邮件验证的用户的此[文章](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)）。

![指出租户不允许电子邮件验证的用户的错误](media/troubleshoot/allow-email-verified-users.png)

### <a name="external-user-does-not-exist-already-in-a-federated-domain"></a>外部用户尚未存在于联合域中

如果使用联合身份验证，并且用户在 Azure Active Directory 中不存在，则无法邀请该用户。

要解决此问题，外部用户的管理员必须将该用户的帐户同步到 Azure Active Directory。

## <a name="how-does--which-is-not-normally-a-valid-character-sync-with-azure-ad"></a>“\#”（这通常不是有效字符）如何与 Azure AD 进行同步？

由于受邀帐户 \# 变为 user_contoso.com#EXT#user@contoso.com，“@fabrikam.onmicrosoft.com”是 Azure AD B2B 协作或外部用户的 UPN 中的保留字符。 因此，不允许来自本地的 UPN 中的 \# 登录到 Azure 门户。 

## <a name="i-receive-an-error-when-adding-external-users-to-a-synchronized-group"></a>我将外部用户添加到同步组时，收到错误

外部用户只能添加到“已分配”或“安全”组，而不能分配到在本地控制的组。

## <a name="my-external-user-did-not-receive-an-email-to-redeem"></a>我的外部用户未收到用于兑换的电子邮件

被邀请者应该向其 ISP 或垃圾邮件筛选器查询，以确保允许以下地址：Invites@microsoft.com

## <a name="i-notice-that-the-custom-message-does-not-get-included-with-invitation-messages-at-times"></a>我发现邀请消息有时不包含自定义消息

为遵守隐私法规，在以下情况下，API 不会在电子邮件邀请中包含自定义邮件：

- 邀请方在邀请租户中没有电子邮件地址
- 应用服务主体发送邀请

如果此方案非常重要，则可取消 API 邀请电子邮件，并通过所选的电子邮件机制发送邮件。 请咨询所属组织的法律顾问，确保通过这种方式发送的任何电子邮件均符合隐私法规。

## <a name="you-receive-an-aadsts65005-error-when-you-try-to-log-in-to-an-azure-resource"></a>尝试登录到 Azure 资源时收到 "AADSTS65005" 错误

具有来宾帐户的用户无法登录，并收到以下错误消息：

    AADSTS65005: Using application 'AppName' is currently not supported for your organization contoso.com because it is in an unmanaged state. An administrator needs to claim ownership of the company by DNS validation of contoso.com before the application AppName can be provisioned.

用户具有 Azure 用户帐户，并且是已被放弃或非托管的病毒租户。 此外，租户中没有全局或公司管理员。

若要解决此问题，必须接管放弃的租户。 请参阅[在 Azure Active Directory 中以管理员身份接管非托管目录](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-admin-takeover)。 您还必须访问面向 internet 的 DNS 以获得相关的域后缀，以便提供您控制的命名空间的直接证据。 将租户返回到托管状态后，请与客户讨论是否将用户和验证的域名作为其组织的最佳选择。

## <a name="a-guest-user-with-a-just-in-time-or-viral-tenant-is-unable-to-reset-their-password"></a>具有实时或“病毒性”租户的来宾用户无法重置其密码

如果标识租户是实时 (JIT) 或病毒性租户（独立的不受管 Azure 租户），则只有来宾用户可以重置其密码。 有时，组织将[接管在员工使用其工作电子邮件地址注册服务时创建的病毒性租户的管理](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-admin-takeover)。 组织接管病毒性租户后，只有该组织中的管理员可以重置用户密码或启用 SSPR。 如果需要，作为邀请方组织，你可以从目录中删除来宾用户帐户并重新发送邀请。

## <a name="a-guest-user-is-unable-to-use-the-azuread-powershell-v1-module"></a>来宾用户无法使用 AzureAD PowerShell V1 模块

截至2019年11月18日，你的目录中的来宾用户（定义为**userType**属性等于**guest**的用户帐户）被阻止使用 AzureAD PowerShell V1 模块。 今后，用户需要是成员用户（其中**userType**等于**成员**）或使用 AzureAD PowerShell V2 模块。

## <a name="in-an-azure-us-government-tenant-i-cant-invite-a-b2b-collaboration-guest-user"></a>在 Azure 美国政府租户中，无法邀请 B2B 协作来宾用户

在 Azure 美国政府版云中，当前仅支持在 Azure 美国政府云中的租户之间进行 B2B 协作，并且两者都支持 B2B 协作。 如果你邀请不属于 Azure 美国政府云的租户中的用户，或者不支持 B2B 协作，则会出现错误。 有关详细信息和限制，请参阅[Azure Active Directory Premium P1 和 P2 变体](https://docs.microsoft.com/azure/azure-government/documentation-government-services-securityandidentity#azure-active-directory-premium-p1-and-p2)。


## <a name="next-steps"></a>后续步骤

[获取对 B2B 协作的支持](get-support.md)
