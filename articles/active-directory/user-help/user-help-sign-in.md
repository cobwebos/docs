---
title: 使用双重验证或安全信息-Azure Active Directory 登录 |Microsoft Docs
description: 了解如何使用安全信息中的各种身份验证方法进行登录。
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: lizross
ms.custom: user-help, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60e4d9a4cb555dfbc9f05961487a8f794d19e1bf
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60472952"
---
# <a name="sign-in-using-two-step-verification-or-security-info"></a>使用双重验证或安全信息登录
设置双重验证或安全信息后，可以使用指定的身份验证方法登录到你的帐户。

> [!Note]
> 如果你仍使用双重验证体验，需按照[设置帐户以便进行双重验证](multi-factor-authentication-end-user-first-time.md)一文中的说明设置身份验证方法。
> 
> 如果管理员已启用安全信息体验，则你需要使用这些分步文章设置身份验证方法：<ul><li>[设置安全信息以使用身份验证应用](security-info-setup-auth-app.md)</li><li>[设置安全信息以使用短信](security-info-setup-text-msg.md)</li><li>[设置安全信息以使用电话呼叫](security-info-setup-phone-number.md)</li></ul>

## <a name="sign-in-using-an-authenticator-app-notification-on-your-mobile-device"></a>使用验证器应用通知在移动设备上登录

1. 使用用户名和密码登录到你的帐户。

2. 从发送到移动设备的审批通知中选择“批准”  。

## <a name="sign-in-using-an-authenticator-app-code-on-your-mobile-device"></a>使用验证器应用代码在移动设备上登录

1. 使用用户名和密码登录到你的帐户。

2. 打开验证器应用，然后在“输入代码”  框中键入为帐户随机生成的代码。

## <a name="sign-in-using-your-phone-number"></a>使用电话号码登录

1. 使用用户名和密码登录到你的帐户。

2. 接听电话，并按照说明进行操作。

## <a name="sign-in-using-a-text-message"></a>使用短信登录

1. 使用用户名和密码登录到你的帐户。

2. 打开短信，然后将短信中的代码键入到“输入代码”  框。

## <a name="sign-in-using-another-verification-method"></a>使用其他验证方法登录
如果出于某种原因您无法使用主登录方法，可以使用另一个以前设置了验证方法。

1. 通常情况下，登录到你的帐户，然后选择**在另一种方法登录**链接**双重验证**页。

    ![更改登录验证方法](media/security-info/two-factor-auth-signin-another-way.png)

    >[!Note]
    >如果没有看到**在另一种方法登录**链接，这意味着你尚未设置任何其他验证方法，但需要联系管理员请求帮助登录到你的帐户。 你的管理员可帮助你在登录后，请确保添加其他验证方法。 有关添加验证方法的详细信息，请参阅[管理双重验证设置](multi-factor-authentication-end-user-manage-settings.md)一文。
    > 
    >如果您看到**在另一种方法登录**链接，但仍看不到任何其他验证方法，将需要联系管理员请求帮助登录到你的帐户。

2. 选择其他验证方法，继续使用双重验证过程。

3. 返回帐户后，可更新验证方法（如有必要）。 有关添加或更改方法的详细信息，请参阅[管理双重验证设置](multi-factor-authentication-end-user-manage-settings.md)一文。

## <a name="next-steps"></a>后续步骤

- 在[安全信息（预览版）概述](user-help-security-info-overview.md)一文中了解安全信息。

- 在[双重验证概述](user-help-two-step-verification-overview.md)一文中了解双重验证。 

- 如果丢失或忘记密码，请从[密码重置门户](https://passwordreset.microsoftonline.com/)重置密码

- 在[无法登录 Microsoft 帐户](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)一文中获取登录问题的故障排除提示和帮助。
