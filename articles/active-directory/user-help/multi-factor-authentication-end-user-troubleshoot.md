---
title: 帐户双重身份验证的常见问题 - Azure AD
description: 解决一些更常见的双因素验证问题和您的工作或学校帐户。
services: active-directory
author: curtand
manager: daveba
ms.assetid: 8f3aef42-7f66-4656-a7cd-d25a971cb9eb
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: curtand
ms.reviewer: kexia
ms.openlocfilehash: c28b63749cfdbcd16b94cbd3ca7dd4023f46a351
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78897728"
---
# <a name="common-problems-with-two-factor-verification-and-your-work-or-school-account"></a>双重验证和您的工作或学校帐户的常见问题

当 Azure 活动目录 （Azure AD） 组织启用双重验证时，工作或学校帐户登录需要将用户名、密码和移动设备或电话组合在一起。 它比密码更安全，它依赖于两种身份验证形式：您知道的，以及您拥有的东西。 双重验证可以帮助阻止恶意黑客假装是您，因为即使他们有您的密码，他们也没有您的设备的可能性。

<center>

![概念身份验证方法图像](../authentication/media/concept-mfa-howitworks/methods.png)</center>

有一些常见的双因素验证问题似乎比我们任何人希望更频繁地发生。 我们整理了本文，以解决最常见的问题和一些可能的解决方法。

>[!Important]
>如果您是管理员，则可以在[Azure AD 文档中](https://docs.microsoft.com/azure/active-directory)找到有关如何设置和管理 Azure AD 环境的详细信息。
>
>此内容也仅用于您的工作或学校帐户，这是您的组织提供给您的帐户（例如。 alain@contoso.com 如果您在双因素验证和个人 Microsoft 帐户（例如danielle@outlook.com），您自己设置的帐户（例如），遇到问题，请参阅为 Microsoft[帐户打开或关闭双因素验证](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off)。

## <a name="i-dont-have-my-mobile-device-with-me"></a>我没有我的移动设备

它发生。 您将移动设备留在家中，现在无法使用手机验证您是谁。 如果您以前添加了另一种方法来登录到您的帐户（如办公室电话），则现在应该能够使用该方法。 如果您从未添加过其他验证方法，您必须联系组织的帮助台，并让他们帮助您重新进入您的帐户。

### <a name="to-sign-in-to-your-work-or-school-account-using-another-verification-method"></a>使用其他验证方法登录到您的工作或学校帐户

1. 登录到您的帐户，但在 **"双因素验证**"页上选择 **"以其他方式登录"** 链接。

    ![更改登录验证方法](./media/multi-factor-authentication-end-user-troubleshoot/two-factor-auth-signin-another-way.png)

    如果没有看到“用其他方法登录”链接，这表示尚未设置其他验证方法****。 必须联系管理员，获取登录帐户的帮助。

2. 选择替代验证方法，然后继续双因素验证过程。

## <a name="i-lost-my-mobile-device-or-it-was-stolen"></a>我的移动设备丢失或被盗

如果您丢失或移动设备被盗，则可以使用其他方法登录，也可以要求组织的帮助台清除设置。 我们强烈建议让组织的帮助台知道您的手机是否丢失或被盗，以便对您的帐户进行适当的更新。 清除设置后，系统将提示您下次登录时[注册双因素验证](multi-factor-authentication-end-user-first-time.md)。

## <a name="im-not-getting-the-verification-code-sent-to-my-mobile-device"></a>我没有将验证码发送到我的移动设备

未获取验证码是一个常见问题，它通常与您的移动设备及其设置有关。 一些可能尝试的事情：

尝试此操作 | 指导信息
--------- | ------------
重新启动移动设备 | 有时您的设备只需要刷新。 重新启动设备将结束当前正在运行的任何后台进程或服务，并可能导致问题，同时刷新设备的核心组件，重新启动它们以防它们在某个时候崩溃。
验证您的安全信息是否正确 | 确保您的安全验证方法信息准确无误，尤其是电话号码。 如果您输入了错误的电话号码，则所有警报都将转到该不正确的号码。 幸运的是，该用户无法对警报执行任何操作，但它也无法帮助您登录您的帐户。 要确保信息正确，请参阅[管理双因素验证方法设置](multi-factor-authentication-end-user-manage-settings.md)一文中的说明。
验证通知已打开 | 确保移动设备已打开通知，并且已选择允许电话呼叫、身份验证应用和消息应用（用于短信）的通知方法向移动设备发送可见警报通知。
确保您有设备信号和互联网连接 | 确保您的电话和短信正在发送到您的移动设备。 让朋友打电话给你，并向您发送一条短信，以确保您同时收到这两个消息。 如果没有，请先检查以确保移动设备已打开。 如果设备已打开，但您仍未收到呼叫或短信，则很可能是网络问题，您需要与提供商联系。 如果您经常遇到与信号相关的问题，我们建议您在移动设备上安装和使用 Microsoft[身份验证器应用](user-help-auth-app-download-install.md)。 身份验证器应用可以为登录生成随机安全代码，而无需任何单元信号或互联网连接。
关闭 请勿打扰 | 请确保您尚未打开移动设备的 **"请勿打扰**"功能。 启用此功能后，不允许通知在移动设备上提醒您。 有关如何关闭此功能的说明，请参阅移动设备手册。
取消阻止电话号码 | 在美国，来自 Microsoft 的语音呼叫来自以下号码：+1 （866） 539 4191、+1 （855） 330 8653 和 +1 （877） 668 6536。
检查电池相关设置 | 从表面上看，这一点似乎有点奇怪，但如果您设置了电池优化，以阻止使用较少的应用在后台保持活动状态，则通知系统很可能受到影响。 要尝试解决此问题，请关闭身份验证应用和消息应用的电池优化，然后尝试再次登录到您的帐户。

## <a name="im-not-getting-prompted-for-my-second-verification-information"></a>系统不会提示我提供第二个验证信息

如果您已使用用户名和密码登录到工作或学校帐户，但未提示有关您的其他安全验证信息，则可能尚未设置设备。 必须设置移动设备才能使用特定的附加安全验证方法。 为了确保已打开移动设备，并且可以使用验证方法，请参阅[管理双因素验证方法设置](multi-factor-authentication-end-user-manage-settings.md)一文。 如果您知道尚未设置设备或帐户，则现在可以通过按照["设置我的帐户进行两步验证](multi-factor-authentication-end-user-first-time.md)"一文中的步骤进行设置。

## <a name="i-got-a-new-phone-number-and-i-want-to-add-it"></a>我有一个新的电话号码，我想添加它

如果您获得了新电话号码，则需要更新安全验证方法详细信息，以便验证提示转到正确的位置。 要更新验证方法，请按照"**添加"或"**[更改双因素验证方法设置](multi-factor-authentication-end-user-manage-settings.md#add-or-change-your-phone-number)"一文中的电话号码部分的步骤进行操作。

## <a name="i-got-a-new-mobile-device-and-i-want-to-add-it"></a>我有一个新的移动设备，我想添加它

如果您获得了新的移动设备，则需要将其设置为使用双重验证。 这是一个多步骤解决方案：

1. 按照["设置我的帐户进行两步验证](multi-factor-authentication-end-user-first-time.md)"一文中的步骤，将设备设置为与工作或学校帐户一起工作。

1. 在 **"其他安全验证**"页中更新您的帐户和设备信息，删除旧设备并添加新设备。 有关详细信息，请参阅[管理双因素验证方法设置](multi-factor-authentication-end-user-manage-settings.md)一文。

可选步骤：

- 按照["下载并安装 Microsoft 身份验证器"应用](user-help-auth-app-download-install.md)文章中的步骤，在移动设备上下载、安装和设置 Microsoft 身份验证器应用。

- 按照"打开双因素验证[方法设置](multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device)"一节中的 **"打开双因素验证提示"** 中的步骤，为受信任的设备打开双因素验证。

## <a name="im-having-problems-signing-in-on-my-mobile-device-while-traveling"></a>旅行时，我在移动设备上登录时遇到问题

当您处于国际位置时，您可能会发现使用移动设备相关的验证方法（如短信）更加困难。 您的移动设备还可能导致您产生漫游费。 对于这种情况，我们建议您使用 Microsoft 身份验证器应用，并选择连接到 Wi-Fi 热点。 有关如何在移动设备上下载、安装和设置 Microsoft 身份验证器应用的详细信息，请参阅[下载并安装 Microsoft 身份验证器应用](user-help-auth-app-download-install.md)文章。

## <a name="i-cant-get-my-app-passwords-to-work"></a>我无法使应用密码正常工作

应用密码将替换不支持双重验证的旧桌面应用程序的正常密码。 首先，请确保输入的密码正确。 如果这不能修复它，请尝试使用["管理应用密码"的"管理应用密码"](multi-factor-authentication-end-user-app-passwords.md#create-and-delete-app-passwords-from-the-additional-security-verification-page)部分，为应用创建**和删除应用密码**，以执行两步验证文章的步骤，从而尝试为应用创建新的应用密码。

## <a name="i-cant-turn-two-factor-verification-off"></a>我无法关闭双因素验证

如果您对工作或学校帐户（例如alain@contoso.com），使用双因素验证，则很可能意味着您的组织已决定必须使用此添加的安全功能。 由于你的组织已决定你必须使用此功能，因此无法单独将其关闭。 但是，如果您对个人帐户（如alain@outlook.com）使用双重验证，则可以打开和关闭该功能。 有关如何控制个人帐户的双因素验证的说明，请参阅[为 Microsoft 帐户打开或关闭双因素验证](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off)。

## <a name="i-didnt-find-an-answer-to-my-problem"></a>我找不到问题的解答

如果您已尝试这些步骤，但仍遇到问题，请与组织的帮助台联系以获得帮助。

## <a name="related-articles"></a>相关文章

- [管理双重验证方法设置](multi-factor-authentication-end-user-manage-settings.md)

- [为帐户设置双重验证](multi-factor-authentication-end-user-first-time.md)

- [Microsoft Authenticator 应用常见问题解答](user-help-auth-app-faq.md)
