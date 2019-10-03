---
title: 设置双重验证方法概述 - Azure Active Directory | Microsoft Docs
description: 概述如何为双重验证设置双重验证方法。
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: overview
ms.date: 08/12/2019
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: 83bba343ac73fd0df575ae1e8a83c589a0bc15ac
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2019
ms.locfileid: "69616164"
---
# <a name="set-up-your-two-factor-verification-methods-overview"></a>设置双重验证方法概述

你的组织已启用双重验证，这意味着，现在登录工作或学校帐户需要结合用户名、密码、移动设备或手机。 你的组织启用了此额外验证，因为它依赖于两种形式的身份验证：你知道的事情和拥有的物品，比只使用密码更安全。 双重验证有助于防止恶意黑客假装你的身份，因为即使他们有你的密码，也不太可能有你的设备。

>[!Important]
>此内容适用于用户。 如果你是管理员，可以在 [Azure Active Directory 文档](https://docs.microsoft.com/azure/active-directory)中查找有关如何设置和管理 Azure Active Directory (Azure AD) 环境的详细信息。

## <a name="who-decides-if-you-use-this-feature"></a>谁决定是否要使用此功能？

根据具体的帐户类型，你的组织可能决定必须使用双重验证，有时你也可以自行决定使用此功能。

- **工作或学校帐户。** 如果你使用工作或学校帐户（例如 alain@contoso.com），则由你的组织决定是否必须使用双重验证，以及具体的验证方法是什么。 由于你的组织已决定你必须使用此功能，因此无法单独将其关闭。

- **Microsoft 个人帐户。** 可以选择为 Microsoft 个人帐户（例如 alain@outlook.com）设置双重验证。 如果无法使用双重验证和个人 Microsoft 帐户，请参阅[为你的 Microsoft 帐户打开或关闭双重验证](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off)。 因为你选择是否使用此功能，则可随时将其打开或关闭。

## <a name="access-the-additional-security-verification-page"></a>访问“其他安全性验证”页

组织打开并设置了双重验证后，你会收到一条消息，提示如何提供更多信息来帮助保护帐户安全。

![需要更多信息提示](media/multi-factor-authentication-verification-methods/multi-factor-authentication-initial-prompt.png)

### <a name="to-access-the-additional-security-verification-page"></a>访问“其他安全性验证”页

1. 从“需要更多信息”提示中选择“下一步”   。

    随即出现“其他安全性验证”页  。

2. 从“其他安全性验证”页中，必须决定要使用哪种双重验证方法来验证登录到工作或学校帐户后所说的身份  。 可以选择：

    | 联系方式 | 说明 |
    | --- | --- |
    | 移动应用 | <ul><li>**接收验证通知。** 此选项将通知推送到智能手机或平板电脑上的验证器应用。 查看通知，如果合法，则在应用中选择“身份验证”  。 公司或学校可能要求在身份验证之前，输入 PIN。</li><li>**使用验证码。** 在此模式下，验证器应用生成每隔 30 秒更新一次的验证码。 在登录屏幕中输入最新验证码。<br>Microsoft Authenticator 应用可用于 [Android](https://go.microsoft.com/fwlink/?linkid=866594) 和 [iOS](https://go.microsoft.com/fwlink/?linkid=866594)。</li></ul> |
    | 身份验证电话 | <ul><li>**电话呼叫**向提供的电话号码发起自动语音呼叫。 接听电话，并按电话键盘上的井号键 (#) 进行身份验证。</li><li>**短信**发送包含验证码的短信。 遵循短信中的提示，回复短信或在登录界面中输入提供的验证码。</li></ul> |
    | 办公电话 | 向已提供的电话号码进行自动语音呼叫。 接听电话，并按电话键盘上的井号键 (#) 进行身份验证。 |

## <a name="next-steps"></a>后续步骤

在访问“其他安全性验证”页后，必须选择并设置双重验证方法  ：

- [将移动设备设置为验证方法](multi-factor-authentication-setup-phone-number.md)

- [将办公室电话设置为验证方法](multi-factor-authentication-setup-office-phone.md)

- [将 Microsoft Authenticator 应用设置为验证方法](multi-factor-authentication-setup-auth-app.md)

## <a name="related-resources"></a>相关资源

- [管理双重验证方法设置](multi-factor-authentication-end-user-manage-settings.md)

- [管理应用密码](multi-factor-authentication-end-user-app-passwords.md)

- [使用双重验证进行登录](multi-factor-authentication-end-user-signin.md)

- [获取有关双重验证的帮助](multi-factor-authentication-end-user-troubleshoot.md) 
