---
title: 使用标识身份验证信息登录-Azure AD
description: 了解如何使用安全信息中的各种身份验证方法登录。
services: active-directory
author: curtand
manager: daveba
ms.reviewer: librown
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: curtand
ms.custom: user-help
ms.openlocfilehash: 7d1d27cede6c593150d3937bb3aa0ca59394eea9
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2020
ms.locfileid: "77062160"
---
# <a name="sign-in-using-two-step-verification-or-security-info"></a>使用双重验证或安全信息登录

设置双重验证或安全信息后，可以使用指定的验证方法登录到帐户。

> [!Note]
> 如果仍在使用双重验证体验，则需要按照为[双重验证设置我的帐户一](multi-factor-authentication-end-user-first-time.md)文中的说明设置验证方法。
>
> 如果你的管理员已启用安全信息体验，你将需要使用以下分步文章来设置验证方法：<ul><li>[设置安全信息以使用身份验证应用](security-info-setup-auth-app.md)</li><li>[设置安全信息以使用短信](security-info-setup-text-msg.md)</li><li>[设置安全信息以使用电话呼叫](security-info-setup-phone-number.md)</li><li>[设置安全信息以使用安全密钥](security-info-setup-security-key.md)</li></ul>

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

1. 注册安全密钥后，从 Windows 10 锁定屏幕中选择 "安全密钥" 映像。

2. 将安全密钥插入设备的 USB 端口，并使用安全密钥 PIN 登录 Windows。

    ![Windows 10 锁屏界面上的安全密钥登录](./media/security-info/security-info-windows-10-lock-screen-security-key.png)

## <a name="sign-in-using-a-security-key-and-the-microsoft-edge-browser"></a>使用安全密钥和 Microsoft Edge 浏览器登录

1. 注册安全密钥后，请打开 Microsoft Edge 浏览器。

2. 系统提示登录时，将安全密钥插入设备的 USB 端口，并使用安全密钥 PIN 登录 Windows。

    ![使用 Microsoft Edge 浏览器的安全密钥登录](./media/security-info/security-info-edge-security-key.png)

    >[!NOTE]
    >有关使用 Microsoft Authenticator 应用登录的信息，请参阅文章[使用 Microsoft Authenticator 应用登录到帐户](user-help-auth-app-sign-in.md)。

## <a name="sign-in-using-another-verification-method"></a>使用其他验证方法登录

如果由于某种原因而无法使用主登录方法，则可以使用另一个以前设置的验证方法。

1. 正常登录到你的帐户，然后选择 "**双重验证**" 页上的 "**另一种方式登录**" 链接。

    ![更改登录验证方法](media/security-info/two-factor-auth-signin-another-way.png)

    >[!Note]
    >如果看不到 "**以其他方式登录**" 链接，则表示你尚未设置任何其他验证方法，因此你必须与管理员联系，以帮助登录到你的帐户。 管理员帮助你登录后，请确保添加其他验证方法。 有关添加验证方法的详细信息，请参阅[管理双重验证设置](multi-factor-authentication-end-user-manage-settings.md)一文。
    >
    >如果你看到 "**以其他方式登录**" 链接，但仍看不到任何其他验证方法，则必须与管理员联系，以帮助登录到你的帐户。

2. 选择其他验证方法，继续使用双重验证过程。

3. 返回帐户后，可更新验证方法（如有必要）。 有关添加或更改方法的详细信息，请参阅[管理双重验证设置](multi-factor-authentication-end-user-manage-settings.md)一文。

## <a name="next-steps"></a>后续步骤

- 在[安全信息（预览版）概述](user-help-security-info-overview.md)一文中了解安全信息。

- 在[双重验证概述](user-help-two-step-verification-overview.md)一文中了解双重验证。

- 如果丢失或忘记密码，请从[密码重置门户](https://passwordreset.microsoftonline.com/)重置密码

- 在[无法登录 Microsoft 帐户](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)一文中获取登录问题的故障排除提示和帮助。
