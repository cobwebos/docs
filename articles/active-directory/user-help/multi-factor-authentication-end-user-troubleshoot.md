---
title: '& 的解决方案与帐户身份验证 Azure AD'
description: 了解有关一些更常见的双因素验证问题和工作或学校帐户的潜在问题和解决方案。
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: 8f3aef42-7f66-4656-a7cd-d25a971cb9eb
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: lizross
ms.reviewer: kexia
ms.openlocfilehash: 498c150706cd62168b7c45b1bcf36ad6c3a8b63b
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705201"
---
# <a name="common-problems-and-solutions-with-two-factor-verification-and-your-work-or-school-account"></a>使用双重验证和工作或学校帐户的常见问题和解决方案

你的组织已启用双重验证，这意味着你的工作或学校帐户登录现在需要组合你的用户名、密码、移动设备或手机。 你的组织启用了此额外验证，因为它依赖于两种形式的身份验证：你知道的事情和拥有的物品，比只使用密码更安全。 双重验证有助于防止恶意黑客假装你的身份，因为即使他们有你的密码，也不太可能有你的设备。

有些常见的双因素验证问题似乎比我们所喜欢的更频繁。 我们整理了本文，希望解决最常见的问题和一些可能的修补程序。

>[!Important]
>此内容适用于用户。 如果你是管理员，可以在 [Azure Active Directory 文档](https://docs.microsoft.com/azure/active-directory)中查找有关如何设置和管理 Azure Active Directory (Azure AD) 环境的详细信息。
>
>此内容也只能用于工作或学校帐户，该帐户是由组织提供给你的（例如 alain@contoso.com）。 如果无法使用双重验证和个人 Microsoft 帐户，即你为自己设置的帐户（例如 danielle@outlook.com），请参阅[为你的 Microsoft 帐户打开或关闭双重验证](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off)。

## <a name="i-forgot-my-mobile-device-at-home"></a>我在家里忘记了我的移动设备

这种情况发生。 你将移动设备置于家里，现在你无法使用手机来验证你的身份是否为你。 如果你以前添加了另一种方法以登录到你的帐户，例如你的办公电话，你现在应该能够使用该方法。 如果你从未添加过其他验证方法，则必须与技术支持联系，让他们帮助你返回到你的帐户。

### <a name="to-sign-in-to-your-work-or-school-account-using-another-verification-method"></a>使用其他验证方法登录到你的工作或学校帐户

1. 正常登录到你的帐户，然后选择 "**双重验证**" 页上的 "**以其他方式登录**" 链接。

    ![更改登录验证方法](./media/multi-factor-authentication-end-user-troubleshoot/two-factor-auth-signin-another-way.png)

    >[!Note]
    >如果没有看到“用其他方法登录”链接，这表示尚未设置其他验证方法。 必须联系管理员，获取登录帐户的帮助。

2. 选择替代方法，并继续执行双因素验证过程。

## <a name="i-lost-my-mobile-device-or-it-was-stolen"></a>我的移动设备丢失或被盗

如果你的移动设备丢失或被盗，你可以使用不同的方法登录，或者可以请求技术支持清除你的设置。 如果你的电话丢失或被盗，我们强烈建议你咨询支持人员，以便可以对你的帐户进行适当的更新。 清除设置后，下次登录时，系统将提示你[注册双重验证](multi-factor-authentication-end-user-first-time.md)。

## <a name="im-not-getting-the-verification-code-sent-to-my-mobile-device"></a>我未收到发送到我的移动设备的验证码

如果未获取您的验证码，这是一个常见问题，通常与您的移动设备及其设置相关。 可能需要尝试以下操作：

- **重新启动你的移动设备。** 有时设备只需刷新。 重新启动设备将结束当前正在运行的所有后台进程或服务，并且可能会导致问题，还会在刷新设备的核心组件时重新启动它们，以防在某个时间点崩溃。

- **验证安全信息是否正确。** 请确保安全验证方法信息准确，尤其是电话号码。 如果你放入了错误的电话号码，则你的所有警报都将发送到不正确的数字。 幸运的是，该用户无法对警报执行任何操作，但它也不会帮助你登录到你的帐户。 若要确保信息正确，请参阅[管理双重验证方法设置](multi-factor-authentication-end-user-manage-settings.md)一文中的说明。

- **验证是否启用了通知。** 确保你的移动设备启用了通知，并且已选择允许电话呼叫、身份验证应用和消息应用（对于短信）的通知方法，将可见的警报通知发送到你的移动设备。

- **请确保具有设备信号和 Internet 连接。** 请确保您的电话呼叫和短信正在接收到您的移动设备。 让朋友呼叫你，并向你发送一条短信，确保你同时收到这两者。 如果不这样做，请先检查以确保移动设备已打开。 如果你的设备已打开，但你仍未收到呼叫或文本，则很可能是你的网络有问题，你需要与提供商联系。 如果你经常遇到与信号相关的问题，我们建议你在移动设备上安装并使用[Microsoft Authenticator 应用](user-help-auth-app-download-install.md)。 验证器应用可生成用于登录的随机安全代码，而无需任何单元信号或 Internet 连接。

- **关闭 "请勿打扰"。** 请确保你没有为移动设备启用 "**请勿打扰**" 功能。 启用此功能后，不允许通知在移动设备上发出警报。 有关如何关闭此功能的说明，请参阅移动设备的手册。

- **取消阻止电话号码**在美国中，来自 Microsoft 的语音呼叫来自以下号码： + 1 （866） 539 4191、+ 1 （855） 330 8653 和 + 1 （877） 668 6536。

- **检查与电池有关的设置。** 这种情况在表面上看起来有点奇怪，但如果您已设置电池优化，停止在后台使用不太常用的应用，则通知系统很可能会受到影响。 若要尝试解决此问题，请关闭身份验证应用和消息应用的电池优化，然后再次尝试登录到帐户。

## <a name="im-not-getting-prompted-for-my-second-verification-information"></a>我未收到第二个验证信息的提示

如果你使用用户名和密码登录到你的工作或学校帐户，但没有提示你提供额外的安全验证信息，则可能尚未设置你的设备。 你的移动设备必须专门设置为使用你的附加安全验证方法。 若要确保已打开移动设备，并且该设备可用于验证方法，请参阅[管理双因素验证方法设置](multi-factor-authentication-end-user-manage-settings.md)一文。 如果你知道尚未设置你的设备或帐户，则可以按照为[双重验证设置帐户一](multi-factor-authentication-end-user-first-time.md)文中的步骤操作来完成此操作。

## <a name="i-got-a-new-phone-number-how-do-i-change-it-for-two-factor-verification"></a>我有一个新的电话号码，如何更改它以实现双重验证？

如果你已获得新的电话号码，则需要更新安全验证方法的详细信息，以使验证提示到达正确的位置。 若要更新验证方法，请执行 "[管理双因素验证方法设置](multi-factor-authentication-end-user-manage-settings.md#add-or-change-your-phone-number)" 一文中的 "**添加或更改电话号码**" 一节中的步骤。

## <a name="i-got-a-new-mobile-device-how-do-i-add-it"></a>我有一个新的移动设备，如何添加？

如果已获得新的移动设备，则需要将其设置为使用双因素验证。 这是一个多步骤解决方案：

1. 按照为[双重验证设置帐户一](multi-factor-authentication-end-user-first-time.md)文中的步骤操作，将设备设置为使用工作或学校帐户。

2. 在 "**其他安全性验证**" 页中更新帐户和设备信息，删除旧设备并添加新设备。 有关详细信息，请参阅 "[管理双因素验证方法" 设置](multi-factor-authentication-end-user-manage-settings.md)一文。

3. 可选。 按照[下载并安装 Microsoft Authenticator 应用](user-help-auth-app-download-install.md)一文中的步骤，在移动设备上下载、安装和安装 Microsoft Authenticator 应用。

4. 可选。 按照 "[管理双因素验证方法设置](multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device)" 一文中的 "**在受信任的设备上启用双重验证"** 一节中的步骤，为受信任的设备启用双重验证。

## <a name="im-having-problems-signing-in-on-my-mobile-device-while-traveling"></a>旅行时在移动设备上登录时遇到问题

你可能会发现，在国际位置使用与移动设备相关的验证方法（如文本消息）时，它会更难使用。 你的移动设备还可能会导致漫游费用。 对于这种情况，我们建议使用 Microsoft Authenticator 应用程序，并选择连接到 Wi-fi 热点。 有关如何在移动设备上下载、安装和设置 Microsoft Authenticator 应用的详细信息，请参阅[下载并安装 Microsoft Authenticator 应用](user-help-auth-app-download-install.md)一文。

## <a name="i-cant-get-my-app-passwords-to-work"></a>我无法使我的应用密码生效

对于不支持双重验证的旧版桌面应用程序，应用密码将替换你的普通密码。 首先，请确保输入的密码正确。 如果这不能解决此问题，请尝试使用 "[管理双步验证的应用密码](multi-factor-authentication-end-user-app-passwords.md#create-and-delete-app-passwords-from-the-additional-security-verification-page)" 一文中的 "应用程序密码" 一文中的 "**创建和删除应用程序**密码" 一节中的步骤，为应用程序创建新的应用密码。

## <a name="why-cant-i-turn-two-factor-verification-off"></a>为什么无法关闭双重验证？

如果对工作或学校帐户（例如 alain@contoso.com）使用双重验证，则很可能意味着你的组织已决定必须使用此添加的安全功能。 由于你的组织已决定你必须使用此功能，因此无法单独将其关闭。 然而，如果使用个人帐户（如 alain@outlook.com）进行双重验证，则可以打开和关闭该功能。 有关如何控制 Microsoft 个人帐户的双因素验证的说明，请参阅[打开或关闭 Microsoft 帐户的双重验证](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off)。

## <a name="i-didnt-find-an-answer-to-my-problem"></a>我找不到问题的解答

如果已尝试这些步骤但仍遇到问题，请与支持人员联系以获得帮助。

## <a name="related-articles"></a>相关文章

- [管理双重验证方法设置](multi-factor-authentication-end-user-manage-settings.md)

- [设置我的帐户进行双重验证](multi-factor-authentication-end-user-first-time.md)

- [Microsoft Authenticator 应用常见问题解答](user-help-auth-app-faq.md)
