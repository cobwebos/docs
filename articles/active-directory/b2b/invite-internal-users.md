---
title: 邀请内部用户进行 B2B 协作-Azure AD
description: 如果有适用于合作伙伴、分销商、供应商、供应商和其他来宾的内部用户帐户，则可以通过邀请他们使用自己的外部凭据或登录名进行登录，来 Azure AD B2B 协作。 使用 PowerShell 或 Microsoft Graph 邀请 API。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 04/12/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: c03c2c55988df04cc45ef4a1d66d959513c1626d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85551370"
---
# <a name="invite-internal-users-to-b2b-collaboration"></a>邀请内部用户进行 B2B 协作

> [!NOTE]
> 邀请内部用户使用 B2B 协作是 Azure Active Directory 的公共预览功能。 有关预览版的详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

在 Azure AD B2B 协作的可用性之前，组织可以通过设置内部凭据与分销商、供应商、供应商和其他来宾用户合作。 如果你有与此类似的内部来宾用户，可以邀请他们使用 B2B 协作，以便可以利用 Azure AD B2B 权益。 B2B 来宾用户将能够使用自己的标识和凭据登录，无需维护密码或管理帐户生命周期。

通过向现有内部帐户发送邀请，你可以保留该用户的对象 ID、UPN、组成员身份和应用分配。 无需手动删除并重新邀请用户或重新分配资源。 若要邀请用户，你将使用邀请 API 同时传递内部用户对象和来宾用户的电子邮件地址以及邀请。 用户接受邀请后，B2B 服务会将现有的内部用户对象更改为 B2B 用户。 今后，用户必须使用其 B2B 凭据登录到云资源服务。 它们仍可使用其内部凭据访问本地资源，但你可以通过重置或更改内部帐户的密码来防止这种情况。

> [!NOTE]
> 邀请是单向的。 可以邀请内部用户使用 B2B 协作，但在添加 B2B 凭据后，不能将其删除。 若要将用户更改回仅限内部的用户，则需要删除用户对象并创建一个新的用户对象。

在公共预览版中，在以下情况下，不能使用本文中所述的方法来邀请内部用户使用 B2B 协作：

- 已为内部用户分配 Exchange 许可证。
- 用户来自在目录中为直接联盟设置的域。
- 内部用户是仅限云的帐户，其主帐户不在 Azure AD 中。

在这些情况下，如果必须将内部用户更改为 B2B 用户，则应删除内部帐户并向用户发送 B2B 协作邀请。

**本地同步的用户**：对于在本地与云之间同步的用户帐户，本地目录在被邀请使用 B2B 协作后仍会保留授权源。 对本地帐户所做的任何更改都将同步到云帐户，包括禁用或删除帐户。 因此，只需删除本地帐户，就不能阻止用户登录到本地帐户，同时保留其云帐户。 相反，你可以将本地帐户密码设置为随机 GUID 或其他未知值。

## <a name="how-to-invite-internal-users-to-b2b-collaboration"></a>如何将内部用户邀请到 B2B 协作

可以使用 PowerShell 或邀请 API 将 B2B 邀请发送给内部用户。 请确保要用于邀请的电子邮件地址设置为内部用户对象上的外部电子邮件地址。

- 对于仅限云的用户，请在邀请的 User.othermails 属性中使用电子邮件地址。
- 对于本地同步的用户，你必须使用该邀请的 User. Mail 属性中的值。
- 用户的 Mail 属性中的域必须与他们用来登录的帐户相匹配。 否则，某些服务（例如团队）将无法对用户进行身份验证。

默认情况下，邀请会向用户发送一封电子邮件，告知他们已被邀请，但你可以禁止发送此电子邮件并改为发送你自己的电子邮件。

> [!NOTE]
> 若要发送你自己的电子邮件或其他通信，你可以将 AzureADMSInvitation 与-SendInvitationMessage： $false 一起使用，以便无提示地邀请用户，然后将你自己的电子邮件发送到已转换的用户。 请参阅[AZURE AD B2B 协作 API 和自定义](customize-invitation-api.md)。

## <a name="use-powershell-to-send-a-b2b-invitation"></a>使用 PowerShell 发送 B2B 邀请

使用以下命令来邀请用户使用 B2B 协作：

```powershell
Uninstall-Module AzureADPreview
Install-Module AzureADPreview
$ADGraphUser = Get-AzureADUser -objectID "UPN of Internal User"
$msGraphUser = New-Object Microsoft.Open.MSGraph.Model.User -ArgumentList $ADGraphUser.ObjectId
New-AzureADMSInvitation -InvitedUserEmailAddress <<external email>> -SendInvitationMessage $True -InviteRedirectUrl "http://myapps.microsoft.com" -InvitedUser $msGraphUser
```

## <a name="use-the-invitation-api-to-send-a-b2b-invitation"></a>使用邀请 API 发送 B2B 邀请

下面的示例演示了如何调用邀请 API 来邀请内部用户作为 B2B 用户。

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

对 API 的响应与你邀请新的来宾用户访问目录时获得的响应相同。

## <a name="next-steps"></a>后续步骤

- [B2B 协作邀请兑换](redemption-experience.md)
