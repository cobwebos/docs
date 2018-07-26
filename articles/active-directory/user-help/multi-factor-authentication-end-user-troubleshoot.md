---
title: 双重验证故障排除 - Azure AD | Microsoft Docs
description: 为用户提供指导，让他们了解遇到 Azure 多重身份验证和双重验证问题时应采取什么操作。
services: multi-factor-authentication
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.assetid: 8f3aef42-7f66-4656-a7cd-d25a971cb9eb
ms.service: multi-factor-authentication
ms.workload: identity
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: lizross
ms.reviewer: kexia
ms.openlocfilehash: ec79adcaf7cbef778b8c9e572ae932d5ceb47aa6
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/17/2018
ms.locfileid: "39090371"
---
# <a name="get-help-with-two-step-verification"></a>获取有关双重验证的帮助

双重验证是组织用于保护帐户的一项安全功能。 双重验证比单纯使用密码更安全，因为它依赖于两种形式的身份验证：你知道的事情和拥有的物品。 你知道的事情是你的密码，而你拥有的物品是你的手机或设备。 使用双重验证有助于阻止恶意黑客以你的身份登录，即使他们获取了你的密码，也是如此。

尽管 Microsoft 提供双重验证，但是否使用此功能由你的组织决定。 如果组织要求使用该功能，则你不能放弃使用，就好像不能放弃使用密码来保护帐户一样。

>[!Note]
>要详细了解如何对 Microsoft 个人帐户使用双重验证，请参阅[关于双重验证](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification)一文。

## <a name="why-do-i-need-to-use-another-verification-method"></a>为什么需要使用其他验证方法？

出于多种原因，你可能需要为帐户使用其他验证方法。 例如：

- **你忘记带电话或设备。** 有时，我们的手机可能会遗忘在家里，但工作时仍然需要登录。 首先，应该尝试使用不需要手机的其他登录方法。

- **你的手机丢失或你有了新的电话号码。** 如果手机丢失或你有了新的号码，可以使用其他方法登录或让管理员清除设置。 我们强烈建议你通知管理员手机丢失或被盗，这样可以对你的帐户进行相应的更新。 系统清除设置后，将在你下次登录时提示[注册双重验证](multi-factor-authentication-end-user-first-time.md)。

- **未收到身份验证短信或来电。** 未收到身份验证短信或来电可能是出于多种原因。 如果过去在手机上成功收到短信或来电，则可能是手机提供商的问题，而不是帐户出现问题。 如果经常由于信号不佳导致延迟，建议在移动设备上使用 [Microsoft Authenticator 应用](microsoft-authenticator-app-how-to.md)。 此应用可以生成随机安全代码用于登录，而无需任何手机信号或 Internet 连接。<br><br>如果要尝试接收短信，请让你的朋友向你发送一条短信作为测试，确保你可以收到，如果你收到多条短信，请确保使用最新的代码。

- **应用密码不起作用。** 应用密码会替换普通的密码，这适用于不支持双重验证的旧式桌面应用程序。 首先，请确保输入的密码正确。 如果问题未解决，尝试登录[创建新的应用密码](multi-factor-authentication-end-user-app-passwords.md)或联系管理员[，让其删除你的现有应用密码](../authentication/howto-mfa-userdevicesettings.md)，以便你可创建新密码。

## <a name="sign-in-using-another-verification-method"></a>使用其他验证方法登录

1. 正常登录帐户，选择“双重验证”页面上的“用其他方法登录”链接。

    ![更改登录验证方法](./media/multi-factor-authentication-end-user-troubleshoot/two-factor-auth-signin-another-way.png)

    >[!Note]
    >如果没有看到“用其他方法登录”链接，这表示尚未设置其他验证方法。 必须联系管理员，获取登录帐户的帮助。 登录后，请确保添加其他验证方法。 有关添加验证方法的详细信息，请参阅[管理双重验证设置](multi-factor-authentication-end-user-manage-settings.md)一文。<br><br>如果看到链接，但看不到其他验证方法，必须联系管理员，获取登录帐户的帮助。

2. 选择其他验证方法，继续使用双重验证过程。

3. 返回帐户后，可更新验证方法（如有必要）。 有关添加或更改方法的详细信息，请参阅[管理双重验证设置](multi-factor-authentication-end-user-manage-settings.md)一文。

## <a name="i-didnt-find-an-answer-to-my-problem"></a>我找不到问题的解答

如果已尝试这些步骤但仍遇到问题，请联系管理员获取更多帮助。

## <a name="related-topics"></a>相关主题

* [管理双重验证设置](multi-factor-authentication-end-user-manage-settings.md)

* [Microsoft Authenticator 应用程序常见问题](microsoft-authenticator-app-faq.md)
