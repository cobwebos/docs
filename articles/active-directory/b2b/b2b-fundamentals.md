---
title: Azure Active Directory B2B 最佳实践和建议
description: 了解 Azure Active Directory 中企业到企业（B2B）来宾用户访问的最佳做法和建议。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2aa6bc856fc7b7de071a45f3aa11c051e36eca4f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75480107"
---
# <a name="azure-active-directory-b2b-best-practices"></a>Azure Active Directory B2B 最佳实践
本文包含 Azure Active Directory （Azure AD）中的企业到企业（B2B）协作的建议和最佳实践。

## <a name="b2b-recommendations"></a>B2B 建议
| 建议 | 注释 |
| --- | --- |
| 为了获得最佳登录体验，请与标识提供者联合 | 如果可能，请直接与标识提供者联合，以允许受邀用户登录到你的共享应用和资源，而无需创建 Microsoft 帐户（Msa）或 Azure AD 帐户。 你可以使用[google federation 功能](google-federation.md)，使 B2B 来宾用户可以使用其 Google 帐户登录。 或者，你可以使用 "[直接联合身份验证（预览版）" 功能](direct-federation.md)，设置与标识提供者（IdP）支持 SAML 2.0 或 WS 送单协议的任何组织的直接联盟。 |
| 对不能通过其他方式进行身份验证的 B2B 来宾使用电子邮件一次性密码（预览版）功能 | [电子邮件一次性密码（预览版）](one-time-passcode.md)功能在无法通过其他方式（例如 Azure AD、MICROSOFT 帐户（MSA）或 Google federation）进行身份验证时对 B2B 来宾用户进行身份验证。 当来宾用户兑换邀请或访问共享资源时，他们可以请求临时代码，该代码会发送到他们的电子邮件地址。 他们输入此代码后，可以继续登录。 |
| 在登录页中添加公司品牌 | 你可以自定义登录页面，使 B2B 来宾用户更加直观。 请参阅如何[在 "登录" 和 "访问面板" 页中添加公司品牌](../fundamentals/customize-branding.md)。 |
| 将隐私声明添加到 B2B 来宾用户兑换体验 | 你可以将组织的隐私声明 URL 添加到第一次邀请兑换过程中，以便受邀用户必须同意你的隐私条款才能继续。 请参阅[如何：在 Azure Active Directory 中添加组织的隐私信息](https://aka.ms/adprivacystatement)。 |
| 使用批量邀请（预览）功能同时邀请多个 B2B 来宾用户 | 使用 Azure 门户中的批量邀请预览功能，同时邀请多个来宾用户加入你的组织。 此功能使你可以上传 CSV 文件来创建 B2B 来宾用户并批量发送邀请。 请参阅[批量邀请 B2B 用户的教程](tutorial-bulk-invite.md)。 |
| 强制执行多重身份验证（MFA）的条件性访问策略 | 建议在要与合作伙伴 B2B 用户共享的应用上强制实施 MFA 策略。 这样，无论合作伙伴组织是否使用 MFA，都将对租户中的应用一致地强制执行 MFA。 请参阅[B2B 协作用户的条件性访问](conditional-access.md)。 |
| 如果要强制实施基于设备的条件访问策略，请使用排除列表来允许访问 B2B 用户 | 如果在组织中启用了基于设备的条件性访问策略，则 B2B 来宾用户设备将被阻止，因为它们不受组织的管理。 你可以创建包含特定合作伙伴用户的排除列表，将其从基于设备的条件访问策略中排除。 请参阅[B2B 协作用户的条件性访问](conditional-access.md)。 |
| 提供指向 B2B 来宾用户的直接链接时使用特定于租户的 URL | 作为邀请电子邮件的替代方法，你可以向来宾授予你的应用或门户的直接链接。 此直接链接必须是特定于租户的，这意味着它必须包含租户 ID 或已验证的域，以便在你的租户中对来宾进行身份验证，共享应用位于该位置。 请参阅[用户的兑换体验](redemption-experience.md)。 |
| 开发应用时，请使用 UserType 来确定来宾用户体验  | 如果要开发应用程序，并且想要为租户用户和来宾用户提供不同的体验，请使用 UserType 属性。 令牌中当前未包括 UserType 声明。 应用程序应使用图形 API 来查询目录，以便用户获取其 UserType。 |
| 仅当用户与组织之间的关系发生更改时*才*更改 UserType 属性 | 尽管可以使用 PowerShell 将用户的 UserType 属性从成员转换为来宾（反之亦然），但仅当用户与组织之间的关系发生更改时，才应更改此属性。 请参阅[B2B 来宾用户的属性](user-properties.md)。|

## <a name="next-steps"></a>后续步骤

[管理 B2B 共享](delegate-invitations.md)
