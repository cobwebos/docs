---
title: 邀请内部用户加入 B2B 协作 - Azure AD
description: 如果您有合作伙伴、分销商、供应商、供应商和其他来宾的内部用户帐户，则可以通过邀请他们使用自己的外部凭据或登录来更改为 Azure AD B2B 协作。 使用 PowerShell 或 Microsoft 图形邀请 API。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/12/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 783fc0fa6f6c4e6c918fa3ff5fe0b53a71fa0178
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680172"
---
# <a name="invite-internal-users-to-b2b-collaboration"></a>邀请内部用户加入 B2B 协作

|     |
| --- |
| 邀请内部用户使用 B2B 协作是 Azure 活动目录的公共预览功能。 有关预览的详细信息，请参阅 Microsoft [Azure 预览的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 |
|     |

在 Azure AD B2B 协作可用之前，组织可以通过为其设置内部凭据与分销商、供应商、供应商和其他来宾用户协作。 如果您有这样的内部来宾用户，则可以邀请他们使用 B2B 协作，以便利用 Azure AD B2B 权益。 您的 B2B 来宾用户将能够使用自己的身份和凭据登录，您无需维护密码或管理帐户生命周期。

向现有内部帐户发送邀请允许您保留该用户的对象 ID、UPN、组成员身份和应用分配。 您无需手动删除和重新邀请用户或重新分配资源。 要邀请用户，您将使用邀请 API 传递内部用户对象和来宾用户的电子邮件地址以及邀请。 当用户接受邀请时，B2B 服务将现有内部用户对象更改为 B2B 用户。 今后，用户必须使用 B2B 凭据登录到云资源服务。 他们仍可以使用其内部凭据访问本地资源，但您可以通过重置或更改内部帐户上的密码来防止这种情况。

> [!NOTE]
> 邀请是单向的。 您可以邀请内部用户使用 B2B 协作，但在添加 B2B 凭据后无法删除这些凭据。 要将用户更改回仅内部用户，您需要删除用户对象并创建新对象。

在公共预览版中，本文中描述的邀请内部用户加入 B2B 协作的方法不能在这些情况下使用：

- 已为内部用户分配了 Exchange 许可证。
- 用户来自为目录中的直接联合设置的域。
- 内部用户是仅云帐户，其主帐户不在 Azure AD 中。

在这些情况下，如果必须将内部用户更改为 B2B 用户，则应删除内部帐户并向用户发送 B2B 协作邀请。

**本地同步用户**：对于在本地和云之间同步的用户帐户，在邀请他们使用 B2B 协作后，本地目录仍然是权限来源。 对本地帐户所做的任何更改都将同步到云帐户，包括禁用或删除该帐户。 因此，您无法阻止用户登录其本地帐户，同时通过删除本地帐户来保留其云帐户。 相反，您可以将本地帐户密码设置为随机 GUID 或其他未知值。

## <a name="how-to-invite-internal-users-to-b2b-collaboration"></a>如何邀请内部用户加入 B2B 协作

您可以使用 PowerShell 或邀请 API 向内部用户发送 B2B 邀请。 确保要用于邀请的电子邮件地址设置为内部用户对象上的外部电子邮件地址。

- 对于仅限云的用户，请使用"用户.其他邮件"中针对邀请的电子邮件地址。
- 对于本地同步用户，必须使用邀请的 User.Mail 属性中的值。
- 用户邮件属性中的域必须与用于登录的帐户匹配。 否则，某些服务（如 Teams）将无法对用户进行身份验证。

默认情况下，邀请会向用户发送一封电子邮件，让他们知道自己被邀请了，但您可以禁止此电子邮件并发送您自己的电子邮件。

> [!NOTE]
> 要发送您自己的电子邮件或其他通信，可以使用 New-AzureADMS 邀请与 -SendInviteMessage：$false 以静默方式邀请用户，然后将您自己的电子邮件发送给转换后的用户。 请参阅[Azure AD B2B 协作 API 和自定义](customize-invitation-api.md)。

## <a name="use-powershell-to-send-a-b2b-invitation"></a>使用 PowerShell 发送 B2B 邀请

使用以下命令邀请用户加入 B2B 协作：

```powershell
Uninstall-Module AzureADPreview
Install-Module AzureADPreview
$ADGraphUser = Get-AzureADUser -searchstring "<<external email>>"
$msGraphUser = New-Object Microsoft.Open.MSGraph.Model.User -ArgumentList $ADGraphUser.ObjectId
New-AzureADMSInvitation -InvitedUserEmailAddress <<external email>> -SendInvitationMessage $True -InviteRedirectUrl "http://myapps.microsoft.com" -InvitedUser $msGraphUser
```

## <a name="use-the-invitation-api-to-send-a-b2b-invitation"></a>使用邀请 API 发送 B2B 邀请

下面的示例演示如何调用邀请 API 以邀请内部用户作为 B2B 用户。

```json
POST https://graph.microsoft.com/v1.0/invitations
Authorization: Bearer eyJ0eX...
ContentType: application/json
{
    "invitedUserEmailAddress": "<<external email>>",
    "sendInvitationMessage": true,
    "invitedUserMessageInfo": {
        "messageLanguage": "en-US",
        "ccRecipients": [
            {
                "emailAddress": {
                    "name": null,
                    "address": "<<optional additional notification email>>"
                }
            }
        ],
        "customizedMessageBody": "<<custom message>>"
    },
    "inviteRedirectUrl": "https://myapps.microsoft.com?tenantId=",
    "invitedUser": {
        "id": "<<ID for the user you want to convert>>"
    }
}
```

对 API 的响应与您邀请新来宾用户加入目录时得到的响应相同。

## <a name="next-steps"></a>后续步骤

- [B2B 协作邀请兑换](redemption-experience.md)
