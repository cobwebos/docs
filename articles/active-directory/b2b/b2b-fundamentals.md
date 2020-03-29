---
title: Azure 活动目录 B2B 最佳实践和建议
description: 在 Azure 活动目录中了解企业对企业 （B2B） 来宾用户访问的最佳做法和建议。
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
ms.openlocfilehash: 54f5721ef606b6ea916f5a00031c58f5e2adeb0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050847"
---
# <a name="azure-active-directory-b2b-best-practices"></a>Azure 活动目录 B2B 最佳实践
本文包含 Azure 活动目录 （Azure AD） 中企业对企业 （B2B） 协作的建议和最佳实践。

   > [!IMPORTANT]
   > **从 2021 年 3 月 31 日起**，Microsoft 将不再支持为 B2B 协作方案创建非托管 Azure AD 帐户和租户来兑换邀请。 在准备中，我们鼓励客户选择[电子邮件一次性密码身份验证](one-time-passcode.md)。 我们欢迎您对此公共预览功能的反馈，并兴奋地创建更多协作方式。

## <a name="b2b-recommendations"></a>B2B 建议
| 建议 | 注释 |
| --- | --- |
| 为了获得最佳的登录体验，请与标识提供程序联合 | 尽可能直接与标识提供程序联合，允许受邀用户登录到共享应用和资源，而无需创建 Microsoft 帐户 （MSA） 或 Azure AD 帐户。 您可以使用 Google[联合功能](google-federation.md)允许 B2B 来宾用户使用其 Google 帐户登录。 或者，您可以使用 Direct[联合（预览）功能](direct-federation.md)与标识提供程序 （IdP） 支持 SAML 2.0 或 WS-Fed 协议的任何组织建立直接联合。 |
| 为无法通过其他方式进行身份验证的 B2B 来宾使用电子邮件一次性密码（预览）功能 | [电子邮件一次性密码（预览）](one-time-passcode.md)功能对 B2B 来宾用户进行身份验证，当他们无法通过其他方式（如 Azure AD、Microsoft 帐户 （MSA） 或 Google 联合）进行身份验证时。 当来宾用户兑换邀请或访问共享资源时，他们可以请求临时代码，该代码会发送到他们的电子邮件地址。 他们输入此代码后，可以继续登录。 |
| 在登录页中添加公司品牌 | 您可以自定义登录页面，以便 B2B 来宾用户更加直观。 了解如何[添加公司品牌以登录和访问面板页面](../fundamentals/customize-branding.md)。 |
| 将您的隐私声明添加到 B2B 来宾用户兑换体验 | 您可以将组织隐私声明的 URL 添加到首次邀请兑换流程，以便受邀用户必须同意您的隐私条款才能继续。 请参阅["如何"：在 Azure 活动目录中添加组织的隐私信息](https://aka.ms/adprivacystatement)。 |
| 使用批量邀请（预览）功能同时邀请多个 B2B 来宾用户 | 使用 Azure 门户中的批量邀请预览功能同时邀请多个来宾用户加入您的组织。 此功能允许您上传 CSV 文件以创建 B2B 来宾用户并批量发送邀请。 有关[批量邀请 B2B 用户的教程](tutorial-bulk-invite.md)，请参阅教程。 |
| 为多重身份验证 （MFA） 强制实施条件访问策略 | 我们建议在要与合作伙伴 B2B 用户共享的应用上强制实施 MFA 策略。 这样，无论合作伙伴组织是否使用 MFA，MFA 都将在租户中的应用上持续强制执行。 请参阅[B2B 协作用户的条件访问](conditional-access.md)。 |
| 如果要强制实施基于设备的条件访问策略，请使用排除列表来允许访问 B2B 用户 | 如果组织中启用了基于设备的条件访问策略，则 B2B 来宾用户设备将被阻止，因为它们不受组织管理。 您可以创建包含特定合作伙伴用户的排除列表，以将其从基于设备的条件访问策略中排除。 请参阅[B2B 协作用户的条件访问](conditional-access.md)。 |
| 提供指向 B2B 来宾用户的直接链接时，请使用特定于租户的 URL | 作为邀请电子邮件的替代方法，您可以向来宾提供指向应用或门户的直接链接。 此直接链接必须特定于租户，这意味着它必须包含租户 ID 或已验证的域，以便可以在共享应用所在的租户中对来宾进行身份验证。 请参阅[来宾用户的兑换体验](redemption-experience.md)。 |
| 开发应用时，请使用 UserType 来确定来宾用户体验  | 如果您正在开发应用程序，并且希望为租户用户和来宾用户提供不同的体验，请使用 UserType 属性。 令牌中当前未包括 UserType 声明。 应用程序应使用 Microsoft 图形 API 查询目录，以便用户获取其 UserType。 |
| 仅当用户与组织的关系发生更改时，*才*更改 UserType 属性 | 尽管可以使用 PowerShell 将用户的 UserType 属性从"成员"转换为来宾（反之亦然），但仅当用户与组织的关系发生更改时，才应更改此属性。 请参阅[B2B 来宾用户的属性](user-properties.md)。|

## <a name="next-steps"></a>后续步骤

[管理 B2B 共享](delegate-invitations.md)
