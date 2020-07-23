---
title: 帐户双因素身份验证的常见问题 - Azure AD
description: 双因素身份验证以及工作或学校帐户的一些更常见问题的解决方案。
services: active-directory
author: curtand
manager: daveba
ms.assetid: 8f3aef42-7f66-4656-a7cd-d25a971cb9eb
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 04/02/2020
ms.author: curtand
ms.reviewer: kexia
ms.openlocfilehash: 8d2e34bcfd180dfeb814dace2a496f3ac593c5bf
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83738602"
---
# <a name="common-problems-with-two-factor-verification-and-your-work-or-school-account"></a>双因素验证以及工作或学校帐户的常见问题

如果你的 Azure Active Directory (Azure AD) 组织启用双因素验证，你需要结合使用用户名、密码、移动设备或电话，才能登录工作或学校帐户。 它比单纯使用密码更安全，因为它依赖两种形式的身份验证：你知道的信息和你随身携带的设备。 双因素验证可有助于防止恶意黑客冒充你的身份，因为即使他们有你的密码，也不太可能有你的设备。

<center>

![身份验证方法的概念图](../authentication/media/concept-mfa-howitworks/methods.png)</center>

有一些常见的双因素验证问题，似乎发生得比我们任何一个人预期得都要频繁。 我们编写了这篇文章，以探讨最常见的问题和一些可能的修复方法。

>[!Important]
>如果你是管理员，可以在 [Azure AD 文档](https://docs.microsoft.com/azure/active-directory)中详细了解如何创建和管理 Azure AD 环境。
>
>另外，本文内容也只适用于解决工作或学校帐户出现的问题，即由组织提供给你的帐户（例如 alain@contoso.com）。 如果在结合使用双因素验证和个人 Microsoft 帐户（即你为自己创建的帐户，例如 danielle@outlook.com）时遇到问题，请参阅[为你的 Microsoft 帐户启用或禁用双因素验证](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off)。

## <a name="i-dont-have-my-mobile-device-with-me"></a>我没有随身携带移动设备

这种情况确实会发生。 你把移动设备落在家里了，所以现在无法使用电话来验证你的身份。 如果你之前添加了另一种帐户登录方法（如使用办公电话），现在应该能够使用这种方法了。 如果你从未添加过附加验证方法，你将不得不联系组织的支持人员，让其帮助你重新登录帐户。

### <a name="to-sign-in-to-your-work-or-school-account-using-another-verification-method"></a>使用另一种验证方法登录你的工作或学校帐户的具体步骤

1. 正常登录帐户，但选择“双因素验证”页上的“使用另一种方式登录”链接。

    ![更改登录验证方法](./media/multi-factor-authentication-end-user-troubleshoot/two-factor-auth-signin-another-way.png)

    如果没有看到“用其他方法登录”链接，这表示尚未设置其他验证方法。 必须联系管理员，获取登录帐户的帮助。

2. 选择备用验证方法，然后继续进行双因素验证流程。

## <a name="i-lost-my-mobile-device-or-it-was-stolen"></a>我的移动设备丢失或被盗

如果你的移动设备丢失或被盗，可以使用另一种方法登录，也可以让组织的支持人员清除你的设置。 如果你的电话丢失或被盗，强烈建议你告知组织的支持人员，这样可以对你的帐户进行相应更新。 在你的设置清除后，系统会在你下次登录时提示[注册双因素验证](multi-factor-authentication-end-user-first-time.md)。

## <a name="im-not-getting-the-verification-code-sent-to-my-mobile-device"></a>我没有收到发送到我的移动设备的验证码

没有收到验证码是一个常见问题，这通常与移动设备及其设置有关。 可以尝试下面可能的解决方法：

尝试此操作 | 指导信息
--------- | ------------
重启移动设备 | 有时，设备只是需要刷新一下。 重启设备会结束当前正在运行且可能会导致问题的所有后台进程或服务，同时还会刷新设备的核心组件，同时重启它们，以防它们在某个时间点发生故障。
验证安全信息是否正确无误 | 确保你的安全验证方法信息是准确的，特别是你的电话号码。 如果你输入了错误的电话号码，那么所有警报都会发送到这一错误号码。 幸运的是，这名用户无法使用警报执行任何操作，但这也不能帮助你登录帐户。 若要确保你的信息正确无误，请参阅[管理双因素验证方法设置](multi-factor-authentication-end-user-manage-settings.md)一文中的说明。
验证是否已启用通知 | 确保你的移动设备已启用通知，并且你已选择通知方法，以便于电话呼叫、身份验证应用和消息应用（对于短信）能够将可见的警报通知发送到你的移动设备。
确保设备有信号且已连接 Internet | 确保你的移动设备能够接听电话和接收短信。 让朋友给你打电话和发短信，以确保你都能收到。 如果收不到，请先进行检查，以确保移动设备是打开的。 如果设备是打开的，但你仍收不到电话或短信，这很可能是你的网络出了问题，你需要联系提供商。 如果你经常遇到与信号相关的问题，建议你在移动设备上安装并使用 [Microsoft Authenticator 应用](user-help-auth-app-download-install.md)。 此 Authenticator 应用可以生成用于登录的随机安全码，而不需要任何手机信号或 Internet 连接。
禁用“请勿打扰” | 确保没有在移动设备上启用“请勿打扰”功能。 在此功能启用后，就会禁止在移动设备上发出通知来提醒你。 若要了解如何禁用此功能，请参阅移动设备的手册。
取消阻止电话号码 | 在美国，Microsoft 语音呼叫的号码如下：+1 (866) 539 4191、+1 (855) 330 8653 和 +1 (877) 668 6536。
检查电池相关设置 | 此操作表面上看起来有点奇怪，但如果你已设置电池优化来阻止不太常用的应用在后台保持活跃，你的通知系统很可能已经受到了影响。 若要尝试修复此问题，请为身份验证应用和消息应用禁用电池优化，然后再次尝试登录你的帐户。
禁用第三方安全应用 | 如果你的设备安装有某种应用来最大限度地减少来自未知呼叫方的短信或电话呼叫，则可能会阻止接收验证码。 请尝试在电话上禁用任何第三方安全应用，然后请求发送另一个验证码。

## <a name="im-not-getting-prompted-for-my-second-verification-information"></a>我没有看到提供第二个验证信息的提示

如果你使用用户名和密码登录了工作或学校帐户，但没有看到提供附加安全验证信息的提示，可能是因为你还没有设置设备。 必须将移动设备设置为使用特定的附加安全验证方法。 若要确保你已打开移动设备，且设备可以使用你设置的验证方法，请参阅[更改双因素验证方法设置](multi-factor-authentication-end-user-manage-settings.md)一文。 如果你知道自己还没有设置设备或帐户，可以立即按照[为我的帐户设置双重验证](multi-factor-authentication-end-user-first-time.md)一文中的步骤操作。

## <a name="i-got-a-new-phone-number-and-i-want-to-add-it"></a>我有了一个新的电话号码，并且想要添加它

如果你有了一个新的电话号码，则需要更新安全验证方法详细信息，以便验证提示能够发送到正确的位置。 若要更新验证方法，请按照[更改双因素验证方法设置](multi-factor-authentication-end-user-manage-settings.md#add-or-change-your-phone-number)一文中的“添加或更改你的电话号码”部分所述步骤操作。

## <a name="i-got-a-new-mobile-device-and-i-want-to-add-it"></a>我有了一台新的移动设备，并且想要添加它

如果你有了一台新的移动设备，则需要将它设置为使用双因素验证。 这是一个多步骤的解决方案：

1. 按照[为我的帐户设置双重验证](multi-factor-authentication-end-user-first-time.md)一文中的步骤操作，将设备设置为使用工作或学校帐户。

1. 在“附加安全验证”页中，更新你的帐户和设备信息，同时删除旧设备，并添加新设备。 有关详细信息，请参阅[更改双因素验证方法设置](multi-factor-authentication-end-user-manage-settings.md)一文。

可选步骤：

- 按照[下载和安装 Microsoft Authenticator 应用](user-help-auth-app-download-install.md)一文中的步骤操作，在移动设备上下载、安装并设置 Microsoft Authenticator 应用。

- 按照[管理双因素验证方法设置](multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device)一文中的“在受信任的设备上启用双因素验证提示”部分中的步骤操作，为受信任的设备启用双因素验证。

## <a name="im-having-problems-signing-in-on-my-mobile-device-while-traveling"></a>旅行时，我在移动设备上登录时遇到问题

你可能会发现，当你身处国外时，使用与移动设备相关的验证方法（比如短信）会更加困难。 你的移动设备还可能会导致产生漫游费用。 对于这种情况，建议使用带有连接到 Wi-Fi 热点的选项的 Microsoft Authenticator 应用。 若要详细了解如何在移动设备上下载、安装并设置 Microsoft Authenticator 应用，请参阅[下载和安装 Microsoft Authenticator 应用](user-help-auth-app-download-install.md)一文。

## <a name="i-cant-get-my-app-passwords-to-work"></a>我的应用密码不起作用

对于不支持双因素验证的旧桌面应用，应用密码可以替代普通密码。 首先，请确保输入的密码正确。 如果这样做无法修复此问题，请按照[管理双因素验证的应用密码](multi-factor-authentication-end-user-app-passwords.md#create-and-delete-app-passwords-from-the-additional-security-verification-page)一文中“使用‘我的应用’门户创建和删除应用密码”部分的步骤操作，尝试为应用新建一个应用密码。

## <a name="i-cant-turn-two-factor-verification-off"></a>我无法禁用双因素验证

如果你是将双因素验证用于工作或学校帐户（例如 alain@contoso.com），那么这很可能意味着你的组织已决定必须使用这一附加的安全功能。 由于你的组织已决定你必须使用此功能，因此无法单独将其关闭。 不过，如果你是将双因素验证用于个人帐户（如 alain@outlook.com），则可以启用和禁用此功能。 有关如何控制个人帐户的双因素验证的说明，请参阅[为你的 Microsoft 帐户启用或禁用双因素验证](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off)。

如果无法禁用双因素验证，也可能是因为在组织级别应用了安全默认值。 若要详细了解安全默认值，请参阅[什么是安全默认值？](../fundamentals/concept-fundamentals-security-defaults.md)

## <a name="i-didnt-find-an-answer-to-my-problem"></a>我找不到问题的解答

如果已尝试这些步骤，但仍遇到问题，请联系你组织的支持人员来获取帮助。

## <a name="related-articles"></a>相关文章

- [管理双重验证方法设置](multi-factor-authentication-end-user-manage-settings.md)

- [为我的帐户设置双重验证](multi-factor-authentication-end-user-first-time.md)

- [Microsoft Authenticator 应用常见问题解答](user-help-auth-app-faq.md)
