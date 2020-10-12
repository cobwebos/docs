---
title: 什么是“附加验证”页？ - Azure AD
description: 如何进入“附加安全验证”页进行双重验证
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 05/28/2020
ms.author: curtand
ms.openlocfilehash: e5b07f8f7ae766d110c87a495a3e1623b815e526
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88795977"
---
# <a name="what-is-the-additional-verification-page"></a>什么是“附加验证”页？

组织要采取额外的措施来确保使用你帐户登录的是你本人。 这种额外的安全验证也称为双重验证。 它包含用户名、密码、移动设备或电话的组合。 如果要执行的操作只是针对 alain@outlook.com 等 Microsoft 帐户禁用双重验证，请按照[为 Microsoft 帐户启用或禁用双重验证](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off)中的说明操作。

<center>

![概念身份验证方法图像](../authentication/media/concept-mfa-howitworks/methods.png)</center>

双重验证比单纯使用密码更安全，因为它依赖于两种形式的身份验证：

- 你所知道的信息，如密码。
- 你所拥有的资源，如电话或携带的其他设备。

双重验证可帮助阻止恶意黑客冒充你。 即使他们有你的密码，他们也不太可能有你的设备。

>[!Important]
>如果你是管理员，正在查找有关如何为你的员工或其他用户启用双因素验证的信息，请参阅 [Azure Active Directory 身份验证文档](../authentication/index.yml)。 本文适用于尝试通过工作或学校帐户（如 alain@contoso.com）使用双重验证的用户。

## <a name="who-decides-if-you-use-this-feature"></a>谁决定是否要使用此功能？

谁决定是否使用双重验证取决于所拥有的帐户类型：

- **工作或学校帐户。** 如果使用工作或学校帐户（例如 alain@contoso.com），则由组织决定是否使用双重验证，以及具体的验证方法是什么。 由于组织已决定必须使用此功能，因此无法单独将其关闭。

- **Microsoft 个人帐户。** 可以选择为 Microsoft 个人帐户（例如 alain@outlook.com）设置双重验证。 可以根据需要随时启用或禁用该功能，方法是按照[为 Microsoft 帐户启用或禁用双重验证](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off)的简单说明操作。

    >[!Note]
    >如果在使用双重验证和其中一个个人 Microsoft 帐户时遇到其他问题，[如何对 Microsoft 帐户使用双重验证](https://support.microsoft.com/help/12408/microsoft-account-how-to-use-two-step-verification)中提供了更多建议。

## <a name="open-the-additional-security-verification-page"></a>打开“其他安全性验证”页

组织启用双重验证后，你每次登录都会收到一条消息，提示如何提供更多信息来帮助保护帐户安全。

![需要更多信息提示](media/multi-factor-authentication-verification-methods/multi-factor-authentication-initial-prompt.png)

### <a name="to-access-the-additional-security-verification-page"></a>访问“其他安全性验证”页

1. 从“需要更多信息”提示中选择“下一步” 。

    随即出现“其他安全性验证”页。

2. 在“其他安全性验证”页中，选择登录到工作或学校帐户时用于验证你身份的双重验证方法。 可以选择：

    | 联系方式 | 说明 |
    | --- | --- |
    | 移动应用 | <ul><li>**接收验证通知。** 此选项将通知推送到智能手机或平板电脑上的验证器应用。 查看通知，如果合法，则在应用中选择“身份验证”。 公司或学校可能要求在身份验证之前，输入 PIN。</li><li>**使用验证码。** 在此模式下，应用生成每隔 30 秒更新一次的验证码。 在登录屏幕中输入最新验证码。<br>Microsoft Authenticator 应用可用于 [Android](https://go.microsoft.com/fwlink/?linkid=866594) 和 [iOS](https://go.microsoft.com/fwlink/?linkid=866594)。</li></ul> |
    | 身份验证电话 | <ul><li>**电话呼叫**向提供的电话号码发起自动语音呼叫。 接听电话，并按电话键盘上的井号键 (#) 进行身份验证。</li><li>**短信**发送包含验证码的短信。 遵循短信中的提示，回复短信或在登录界面中输入提供的验证码。</li></ul> |
    | 办公电话 | 向已提供的电话号码进行自动语音呼叫。 接听电话，并按电话键盘上的井号键 (#) 进行身份验证。 |

## <a name="next-steps"></a>后续步骤

在“其他安全性验证”页上选择双重验证方法后，必须对其进行设置：

- [将移动设备设置为验证方法](multi-factor-authentication-setup-phone-number.md)

- [将办公室电话设置为验证方法](multi-factor-authentication-setup-office-phone.md)

- [将 Microsoft Authenticator 应用设置为验证方法](multi-factor-authentication-setup-auth-app.md)

## <a name="related-resources"></a>相关资源

- [使用双重验证登录](multi-factor-authentication-end-user-signin.md)

- [获取有关双重验证的帮助](multi-factor-authentication-end-user-troubleshoot.md)