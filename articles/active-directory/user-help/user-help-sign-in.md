---
title: 使用标识身份验证信息登录 - Azure AD
description: 了解如何使用安全信息中的各种身份验证方法进行登录。
services: active-directory
author: curtand
manager: daveba
ms.reviewer: librown
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 07/18/2019
ms.author: curtand
ms.custom: user-help
ms.openlocfilehash: 42ced6632ebfa56af8fe13a02f531b9835e13df3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88799377"
---
# <a name="sign-in-using-two-step-verification-or-security-info"></a>使用双重验证或安全信息登录

设置双重验证或安全信息后，可以使用指定的验证方法登录帐户。

> [!Note]
> 如果你仍使用双重验证体验，需按照[设置帐户以便进行双重验证](multi-factor-authentication-end-user-first-time.md)一文中的说明设置验证方法。
>
> 如果管理员已启用安全信息体验，则需要使用以下分步文章设置验证方法：<ul><li>[设置安全信息以使用身份验证应用](security-info-setup-auth-app.md)</li><li>[设置安全信息以使用短信](security-info-setup-text-msg.md)</li><li>[设置安全信息以使用电话呼叫](security-info-setup-phone-number.md)</li><li>[设置安全信息以使用安全密钥](security-info-setup-security-key.md)</li></ul>

## <a name="sign-in-using-an-authenticator-app-notification-on-your-mobile-device"></a>使用验证器应用通知在移动设备上登录

1. 使用用户名和密码登录到你的帐户。

2. 从发送到移动设备的审批通知中选择“批准”。

## <a name="sign-in-using-an-authenticator-app-code-on-your-mobile-device"></a>使用验证器应用代码在移动设备上登录

1. 使用用户名和密码登录到你的帐户。

2. 打开验证器应用，然后在“输入代码”框中键入为帐户随机生成的代码。

## <a name="sign-in-using-your-phone-number"></a>使用电话号码登录

1. 使用用户名和密码登录到你的帐户。

2. 接听电话，并按照说明进行操作。

## <a name="sign-in-using-a-text-message"></a>使用短信登录

1. 使用用户名和密码登录到你的帐户。

2. 打开短信，然后将短信中的代码键入到“输入代码”框。

## <a name="sign-in-using-a-security-key-at-the-lock-screen"></a>在锁屏界面上使用安全密钥登录

1. 注册安全密钥后，从 Windows 10 锁屏界面中选择安全密钥图像。

2. 将安全密钥插入设备的 USB 端口，然后使用安全密钥 PIN 登录 Windows。

    ![在 Windows 10 锁屏界面上使用安全密钥登录](./media/security-info/security-info-windows-10-lock-screen-security-key.png)

## <a name="sign-in-using-a-security-key-and-the-microsoft-edge-browser"></a>使用安全密钥和 Microsoft Edge 浏览器登录

1. 注册安全密钥后，打开 Microsoft Edge 浏览器。

2. 系统提示登录时，将安全密钥插入设备的 USB 端口，然后使用安全密钥 PIN 登录 Windows。

    ![使用 Microsoft Edge 浏览器进行安全密钥登录](./media/security-info/security-info-edge-security-key.png)

    >[!NOTE]
    >有关使用 Microsoft Authenticator 应用登录的信息，请参阅[使用 Microsoft Authenticator 应用登录帐户](user-help-auth-app-sign-in.md)一文。

## <a name="sign-in-using-another-verification-method"></a>使用其他验证方法登录

如果由于某种原因而无法使用主登录方法，则可以使用之前设置的其他验证方法。

1. 正常登录帐户，然后选择“双重验证”页面上的“用其他方法登录”链接 。

    ![更改登录验证方法](media/security-info/two-factor-auth-signin-another-way.png)

    >[!Note]
    >如果未看到“用其他方法登录”链接，这意味着你尚未设置其他任何验证方法，因此必须联系管理员，获取登录帐户的帮助。 管理员帮助你登录后，请确保添加其他验证方法。 有关添加验证方法的详细信息，请参阅[管理双重验证设置](multi-factor-authentication-end-user-manage-settings.md)一文。
    >
    >如果可看到“用其他方法登录”链接，但看不到其他验证方法，必须联系管理员，获取登录帐户的帮助。

2. 选择其他验证方法，继续使用双重验证过程。

3. 返回帐户后，可更新验证方法（如有必要）。 有关添加或更改方法的详细信息，请参阅[管理双重验证设置](multi-factor-authentication-end-user-manage-settings.md)一文。

## <a name="next-steps"></a>后续步骤

- 在[安全信息（预览版）概述](./security-info-setup-signin.md)一文中了解安全信息。

- 在[双重验证概述](./multi-factor-authentication-end-user-first-time.md)一文中了解双重验证。

- 如果丢失或忘记密码，请从[密码重置门户](https://passwordreset.microsoftonline.com/)重置密码

- 在[无法登录 Microsoft 帐户](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)一文中获取登录问题的故障排除提示和帮助。