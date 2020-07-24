---
title: Azure 多重身份验证常见问题解答 - Azure Active Directory
description: 与 Azure 多重身份验证相关的常见问题与解答。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/14/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: d6f5cabace81d53edf36ac6be0a2eb8830e6cc5f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87035071"
---
# <a name="frequently-asked-questions-about-azure-multi-factor-authentication"></a>有关 Azure 多重身份验证的常见问题

本“常见问题解答”文章解答有关 Azure 多重身份验证和使用多重身份验证服务的常见问题。 其中的问题已划分为常规服务问题、计费模式问题、用户体验问题和故障排除问题。

> [!IMPORTANT]
> 从 2019 年 7 月 1 日开始，Microsoft 将不再为新部署提供 MFA 服务器。 希望用户执行多重身份验证的新客户应使用基于云的 Azure 多重身份验证。 在 7 月 1 日之前激活了 MFA 服务器的现有客户可以像平时一样下载最新版本、将来的更新以及生成激活凭据。
>
> 以下与 Azure 多重身份验证服务器相关的信息仅适用于已运行 MFA 服务器的用户。
>
> 自 2018 年 9 月 1 日起，新客户无法再使用基于消费的许可。
> 2018年9月1日生效，可能无法再创建新的身份验证提供程序。 可以继续使用和更新现有的身份验证提供程序。 多重身份验证将继续成为 Azure AD Premium 许可证中的可用功能。

## <a name="general"></a>常规

* [Azure 多重身份验证服务器如何处理用户数据？](#how-does-azure-multi-factor-authentication-server-handle-user-data)
* [哪些短信代码用于向用户发送短信？](#what-sms-short-codes-are-used-for-sending-sms-messages-to-my-users)

### <a name="how-does-azure-multi-factor-authentication-server-handle-user-data"></a>Azure 多重身份验证服务器如何处理用户数据？

在多重身份验证服务器中，用户数据只存储在本地服务器上。 云中不会持久存储任何用户数据。 当用户执行双重验证时，多重身份验证服务器将数据发送到 Azure 多重身份验证云服务执行身份验证。 多重身份验证服务器与多重身份验证云服务之间的通信在出站端口 443 上使用安全套接字层 (SSL) 或传输层安全性 (TLS)。

当身份验证请求发送到云服务时，收集的数据用于身份验证和使用情况报告。 双重验证日志中包含以下数据字段：

* **唯一 ID**（用户名或本地多重身份验证服务器 ID）
* **名字和姓氏**（可选）
* **电子邮件地址**（可选）
* **电话号码**（用于语音通话或短信身份验证）
* **设备令牌**（用于移动应用身份验证）
* **身份验证模式**
* **身份验证结果**
* **多重身份验证服务器名称**
* **多重身份验证服务器 IP**
* **客户端 IP**（如果可用）

可以在多重身份验证服务器中配置可选字段。

验证结果（成功或拒绝）和任何拒绝原因（如果有）与身份验证数据一起存储。 可在身份验证和使用情况报告中使用这些数据。

有关详细信息，请参阅[Azure 多重身份验证的数据驻留和客户数据](concept-mfa-data-residency.md)。

### <a name="what-sms-short-codes-are-used-for-sending-sms-messages-to-my-users"></a>哪些短信简短代码用于向用户发送短信？

在美国中，我们将使用以下短信短代码：

* *97671*
* *69829*
* *51789*
* *99399*

在加拿大，我们使用以下短信短代码：

* *759731*
* *673801*

不保证基于短信或基于语音的多重身份验证提示传递相同的数字。 为了用户的利益，我们在做出路线调整期间可能随时添加或删除简短代码，以提高短信传送能力。

除美国和加拿大以外的国家或地区，我们不支持短代码。

## <a name="billing"></a>计费

大多数计费问题都可以通过参考[多重身份验证定价页](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)或[Azure 多重身份验证的文档版本和消耗计划](concept-mfa-licensing.md)来回答。

* [我的组织是否计费用于发送用于身份验证的电话呼叫和短信？](#is-my-organization-charged-for-sending-the-phone-calls-and-text-messages-that-are-used-for-authentication)
* [每用户计费模型是为所有启用的用户，还是只对执行双重验证的用户收费？](#does-the-per-user-billing-model-charge-me-for-all-enabled-users-or-just-the-ones-that-performed-two-step-verification)
* [多重身份验证如何计费？](#how-does-multi-factor-authentication-billing-work)
* [Azure 多重身份验证是否有免费版本？](#is-there-a-free-version-of-azure-multi-factor-authentication)
* [我的组织是否可以随时在每个用户和每个身份验证消耗计费模式之间切换？](#can-my-organization-switch-between-per-user-and-per-authentication-consumption-billing-models-at-any-time)
* [你的组织是否可以随时在基于消耗的计费和订阅（基于许可证的模型）之间进行切换？](#can-my-organization-switch-between-consumption-based-billing-and-subscriptions-a-license-based-model-at-any-time)
* [组织是否必须使用并同步标识才能使用 Azure 多重身份验证？](#does-my-organization-have-to-use-and-synchronize-identities-to-use-azure-multi-factor-authentication)

### <a name="is-my-organization-charged-for-sending-the-phone-calls-and-text-messages-that-are-used-for-authentication"></a>通过电话或短信进行身份验证时，我的组织是否需要付费？

不可以。通过 Azure 多重身份验证向用户发送的各个电话呼叫或短信无需支付费用。 如果使用按身份验证的 MFA 提供程序，则需为每次身份验证付费，但不需要为使用的方法付费。

用户可能需要为收到的电话或短信支付费用，取决于个人电话服务。

### <a name="does-the-per-user-billing-model-charge-me-for-all-enabled-users-or-just-the-ones-that-performed-two-step-verification"></a>按用户计费模式的收费依据是所有启用的用户，还是执行双重验证的用户？

计费依据是配置为使用多重身份验证的用户数量，无论用户当月是否执行过双重验证。

### <a name="how-does-multi-factor-authentication-billing-work"></a>多重身份验证服务如何收费？

创建按用户或按身份验证计费的 MFA 提供程序时，组织的 Azure 订阅每月根据使用情况计费一次。 此计费模式类似于 Azure 如何使用虚拟机和 Web 应用。

如果购买 Azure 多重身份验证订阅，组织仅支付每个用户每年的许可费用。 MFA 许可证和 Office 365、Azure AD Premium 或企业移动性 + 安全性捆绑包按此方式计费。

有关详细信息，请参阅[如何获取 Azure 多重身份验证](concept-mfa-licensing.md)。

### <a name="is-there-a-free-version-of-azure-multi-factor-authentication"></a>Azure 多重身份验证是否有免费版本？

可以在 Azure AD Free 层中启用安全默认设置。 使用安全默认设置，将使用 Microsoft Authenticator 应用为所有用户启用多重身份验证。 不能使用具有安全默认设置的短信或电话验证，只是 Microsoft Authenticator 应用。

有关详细信息，请参阅[什么是安全默认值？](../fundamentals/concept-fundamentals-security-defaults.md)

### <a name="can-my-organization-switch-between-per-user-and-per-authentication-consumption-billing-models-at-any-time"></a>组织是否可以随时在“按用户”和“按身份验证”使用量计费模式之间切换？

如果组织以独立服务的形式（采用基于使用量的计费模式）购买了 MFA，则可以在创建 MFA 提供程序时选择计费模式。 创建 MFA 提供程序后，无法更改计费模式。 

如果 MFA 提供程序*未*链接到 Azure AD 租户，或者将新的 MFA 提供程序链接到不同的 Azure AD 租户、用户设置和配置选项，则不会传输。 此外，需要使用通过新 MFA 提供程序生成的激活凭据，重新激活现有 Azure MFA 服务器。 重新激活 MFA 服务器并将其链接到新 MFA 提供程序不会影响电话和短信身份验证，但所有用户不再会收到移动应用通知，除非他们重新激活移动应用。

在 [Azure 多重身份验证提供程序入门](concept-mfa-authprovider.md)中了解有关 MFA 提供程序的详细信息。

### <a name="can-my-organization-switch-between-consumption-based-billing-and-subscriptions-a-license-based-model-at-any-time"></a>组织是否可以随时在使用量计费和订阅（基于许可证的模式）之间切换？

在某些情况下，是。

如果目录中包含*按用户* Azure 多重身份验证提供程序，则可以添加 MFA 许可证。 拥有许可证的用户不会计入基于使用量的按用户计费。 对于没有许可证的用户，仍可通过 MFA 提供程序启用 MFA。 如果为配置为使用多重身份验证的所有用户购买并分配许可证，可以删除 Azure 多重身份验证提供程序。 如果将来的用户数超过许可证数，始终可以创建另一个按用户的 MFA 提供程序。

如果你的目录具有*每个身份验证*Azure 多重身份验证提供程序，则只要 MFA 提供程序已链接到你的订阅，你就始终需要为每次身份验证付费。 可以将 MFA 许可证分配给用户，但仍要为每个双重验证请求付费，不管该请求是否来自拥有 MFA 许可证的用户。

### <a name="does-my-organization-have-to-use-and-synchronize-identities-to-use-azure-multi-factor-authentication"></a>组织是否必须使用并同步标识才能使用 Azure 多重身份验证？

如果组织使用基于使用量的计费模式，则 Azure Active Directory 是可选而不是必需的。 如果 MFA 提供程序未链接到 Azure AD 租户，则只能在本地部署 Azure 多重身份验证服务器。

许可模式需要 Azure Active Directory，因为在购买许可证并将其分配给目录中的用户时，许可证将添加到 Azure AD 租户。

## <a name="manage-and-support-user-accounts"></a>管理和支持用户帐户

* [如果他们的手机上未收到响应，我该怎么办？](#what-should-i-tell-my-users-to-do-if-they-dont-receive-a-response-on-their-phone)
* [如果某个用户无法进入其帐户，我该怎么办？](#what-should-i-do-if-one-of-my-users-cant-get-in-to-their-account)
* [如果某个用户丢失了使用应用密码的手机，应该怎么办？](#what-should-i-do-if-one-of-my-users-loses-a-phone-that-is-using-app-passwords)
* [如果用户无法登录到非浏览器应用，该怎么办？](#what-if-a-user-cant-sign-in-to-non-browser-apps)
* [我的用户说，有时他们不会收到短信或验证超时。](#my-users-say-that-sometimes-they-dont-receive-the-text-message-or-the-verification-times-out)
* [我是否可以更改在系统超时之前，用户必须在短信中输入验证码的时间？](#can-i-change-the-amount-of-time-my-users-have-to-enter-the-verification-code-from-a-text-message-before-the-system-times-out)
* [能否在 Azure 多重身份验证服务器中使用硬件令牌？](#can-i-use-hardware-tokens-with-azure-multi-factor-authentication-server)
* [能否使用 Azure 多重身份验证服务器保护终端服务？](#can-i-use-azure-multi-factor-authentication-server-to-secure-terminal-services)
* [我在 MFA 服务器中配置了呼叫方 ID，但我的用户仍从匿名呼叫方收到多重身份验证呼叫。](#i-configured-caller-id-in-mfa-server-but-my-users-still-receive-multi-factor-authentication-calls-from-an-anonymous-caller)
* [为什么会提示用户注册其安全信息？](#why-are-my-users-being-prompted-to-register-their-security-information)

### <a name="what-should-i-tell-my-users-to-do-if-they-dont-receive-a-response-on-their-phone"></a>如果他们的手机上未收到响应，我该怎么办？

让用户在5分钟内尝试最多5次，以获取电话呼叫或短信进行身份验证。 Microsoft 使用多个提供程序，用于进行呼叫和发送短信。 如果此方法不起作用，请打开支持案例进行进一步的故障排除。

第三方安全应用程序也可能会阻止验证代码文本消息或电话呼叫。 如果使用第三方安全应用，请尝试禁用保护，然后请求发送另一个 MFA 验证代码。

如果上述步骤不起作用，请检查是否为用户配置了多个验证方法。 再次尝试登录，但在登录页上选择其他验证方法。

有关详细信息，请参阅[最终用户故障排除指南](../user-help/multi-factor-authentication-end-user-troubleshoot.md)。

### <a name="what-should-i-do-if-one-of-my-users-cant-get-in-to-their-account"></a>如果某个用户无法进入其帐户，我该怎么做？

可以要求用户再次完成注册过程来重置其帐户。 详细了解[管理云中 Azure 多重身份验证的用户和设备设置](howto-mfa-userdevicesettings.md)。

### <a name="what-should-i-do-if-one-of-my-users-loses-a-phone-that-is-using-app-passwords"></a>如果某个用户丢失了使用应用密码的手机，我该怎么做？

为了防止未经授权的访问，请删除该用户的所有应用密码。 用户购买替代设备后，即可重新创建密码。 详细了解[管理云中 Azure 多重身份验证的用户和设备设置](howto-mfa-userdevicesettings.md)。

### <a name="what-if-a-user-cant-sign-in-to-non-browser-apps"></a>如果用户无法登录到非浏览器应用，该怎么办？

如果组织仍然使用传统客户端，并且[允许使用应用密码](howto-mfa-app-passwords.md)，则用户无法使用其用户名和密码登录到这些传统客户端。 他们需要[设置应用密码](../user-help/multi-factor-authentication-end-user-app-passwords.md)。 用户必须清除（删除）其登录信息、重新启动应用，并使用其用户名和*应用密码*而不是普通的密码登录。

如果你的组织没有旧客户端，则不应允许用户创建应用密码。

> [!NOTE]
> **适用于 Office 2013 客户端的新式验证**
>
> 只有不支持新式身份验证的应用才需要应用密码。 Office 2013 客户端支持新式身份验证协议，但需要进行配置。 对于运行三月2015或更高版本的 Office 2013 的任何客户，新式验证均可供使用。 有关详细信息，请参阅博客文章[更新的 Office 365 新式身份验证](https://www.microsoft.com/microsoft-365/blog/2015/11/19/updated-office-365-modern-authentication-public-preview/)。

### <a name="my-users-say-that-sometimes-they-dont-receive-the-text-message-or-the-verification-times-out"></a>我的用户说，有时他们收不到短信，或者验证超时。

由于存在可能影响服务可靠性的不可控因素，因此不能保证 SMS 消息的传送。 这些因素包括目标国家/地区、移动电话运营商和信号强度。

第三方安全应用程序也可能会阻止验证代码文本消息或电话呼叫。 如果使用第三方安全应用，请尝试禁用保护，然后请求发送另一个 MFA 验证代码。

如果用户在可靠地接收短信时经常遇到问题，请告诉他们使用 Microsoft Authenticator 应用或电话呼叫方法。 Microsoft Authenticator 可以通过手机网络和 Wi-fi 连接接收通知。 此外，即使设备根本没有信号，也可以生成验证码。 Microsoft Authenticator 应用可用于[Android](https://go.microsoft.com/fwlink/?Linkid=825072)、 [iOS](https://go.microsoft.com/fwlink/?Linkid=825073)和[Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6)。

### <a name="can-i-change-the-amount-of-time-my-users-have-to-enter-the-verification-code-from-a-text-message-before-the-system-times-out"></a>是否可以更改在系统超时之前，用户必须输入短信中验证代码的时限？

在某些情况下可以。

对于使用 Azure MFA 服务器 v7.0 或更高版本的单向短信，可以通过设置注册表项来配置超时设置。 在 MFA 云服务发送短信后，验证码（或一次性密码）将返回给 MFA 服务器。 默认情况下，MFA 服务器将验证码存储在内存中，保持期为 300 秒。 如果用户在 300 秒后未输入验证码，身份验证将遭拒。 若要更改默认超时设置，请按照以下步骤操作：

1. 转到 `HKLM\Software\Wow6432Node\Positive Networks\PhoneFactor`。
2. 创建一个名为*pfsvc_pendingSmsTimeoutSeconds*的**DWORD**注册表项，并设置想要 Azure MFA 服务器存储一次密码的时间（以秒为单位）。

>[!TIP]
>
> 如果有多个 MFA 服务器，只有处理原始身份验证请求的服务器才知道发送给用户的验证码。 在用户输入验证码后，必须将用于验证的身份验证请求发送到相同的服务器。 如果将验证码验证请求发送到不同的服务器，身份验证将遭拒。

如果用户未在定义的超时期内回复短信，身份验证将遭拒。

对于云中的 Azure MFA （包括 AD FS 适配器或网络策略服务器扩展）的单向短信，你无法配置超时设置。 Azure AD 存储验证码 180 秒。

### <a name="can-i-use-hardware-tokens-with-azure-multi-factor-authentication-server"></a>是否可以在 Azure 多重身份验证服务器上使用硬件令牌？

如果使用 Azure 多重身份验证服务器，则可以导入第三方开放身份验证（OATH）基于时间的一次性密码（TOTP）令牌，然后将其用于双重验证。

如果将机密密钥放在可以导入 Azure 多重身份验证服务器的 CSV 文件中，可以使用充当 OATH TOTP 令牌的 ActiveIdentity 令牌。 可将 OATH 令牌与以下服务配合使用：Active Directory 联合身份验证服务 (ADFS)、基于 Internet Information Server (IIS) 表单的身份验证和远程身份验证拨入用户服务 (RADIUS)（只要客户端系统能够接受用户输入）。

可以使用以下格式导入第三方 OATH TOTP 令牌：  

- 可移植对称密钥容器 (PSKC)
- CSV 如果文件包含序列号、Base 32 格式的密钥和时间间隔

### <a name="can-i-use-azure-multi-factor-authentication-server-to-secure-terminal-services"></a>是否可以使用 Azure 多重身份验证服务器保护终端服务？

可以，但如果使用的是 Windows Server 2012 R2 或更高版本，则只能使用远程桌面网关（RD 网关）来保护终端服务。

Windows Server 2012 R2 中的安全性更改改变了 Azure 多重身份验证服务器连接到 Windows Server 2012 和更低版本中的本地安全机构 (LSA) 安全包的方式。 对于 Windows 2012 或更低版本中的终端服务版本，可以[使用 Windows 身份验证保护应用程序](howto-mfaserver-windows.md#to-secure-an-application-with-windows-authentication-use-the-following-procedure)。 如果使用的是 Windows Server 2012 R2，则需要 RD 网关。

### <a name="i-configured-caller-id-in-mfa-server-but-my-users-still-receive-multi-factor-authentication-calls-from-an-anonymous-caller"></a>我在 MFA 服务器中配置了来电显示，但用户仍然接到匿名呼叫者的多重身份验证电话。

通过公共电话网络拨打多重身份验证电话时，有时会通过不支持来电显示的运营商路由电话。 由于此电信行为的原因，不保证调用方 ID，即使多重身份验证系统始终会发送它。

### <a name="why-are-my-users-being-prompted-to-register-their-security-information"></a>为何系统提示用户注册其安全信息？

有多种原因会导致系统提示用户注册其安全信息：

- 该用户的管理员已在 Azure AD 中为其启用 MFA，但没有为其帐户注册安全信息。
- 该用户已在 Azure AD 中启用自助密码重置。 以后如果用户忘记了密码，安全信息可帮助他们重置密码。
- 用户访问的应用程序具有条件性访问策略，要求进行 MFA，但之前尚未注册 MFA。
- 用户正在使用 Azure AD （包括 Azure AD 加入）注册设备，并且你的组织需要通过 MFA 进行设备注册，但该用户以前未注册 MFA。
- 用户正在 Windows 10 中生成 Windows Hello for Business （需要 MFA），但以前未注册 MFA。
- 组织已创建并启用一个 MFA 注册策略，该策略已应用到该用户。
- 该用户以前已注册 MFA，但选择的验证方法后来被管理员禁用。 因此，该用户必须再次完成 MFA 注册，以选择新的默认验证方法。

## <a name="errors"></a>错误

* [如果用户在使用移动应用通知时看到 "身份验证请求不适用于已激活的帐户" 错误消息，该怎么办？](#what-should-users-do-if-they-see-an-authentication-request-is-not-for-an-activated-account-error-message-when-using-mobile-app-notifications)
* [如果用户在登录到非浏览器应用程序时看到0x800434D4L 错误消息，该怎么办？](#what-should-users-do-if-they-see-a-0x800434d4l-error-message-when-signing-in-to-a-non-browser-application)

### <a name="what-should-users-do-if-they-see-an-authentication-request-is-not-for-an-activated-account-error-message-when-using-mobile-app-notifications"></a>如果用户在使用移动应用通知时看到 "身份验证请求不适用于已激活的帐户" 错误消息，该怎么办？

要求用户完成以下过程以从 Microsoft Authenticator 中删除帐户，并再次添加：

1. 中转到[Azure 门户配置文件](https://account.activedirectory.windowsazure.com/profile/)，并使用组织帐户进行登录。
2. 选择 "**其他安全性验证**"。
3. 从 Microsoft Authenticator 应用中删除现有帐户。
4. 单击 "**配置**"，然后按照说明重新配置 Microsoft Authenticator。

### <a name="what-should-users-do-if-they-see-a-0x800434d4l-error-message-when-signing-in-to-a-non-browser-application"></a>如果用户在登录非浏览器应用程序时看到 0x800434D4L 错误消息，该怎么办？

当你尝试登录到安装在本地计算机上的非浏览器应用程序时，会发生*0x800434D4L*错误，这不能用于需要双重验证的帐户。

此错误的解决方法是，使用不同的用户帐户执行管理员相关操作和非管理员操作。 稍后，可以在管理员帐户与非管理员帐户之间链接邮箱，以便能够使用非管理员帐户登录到 Outlook。 若要详细了解此解决方案，请了解如何[让管理员能够打开和查看用户邮箱的内容](https://help.outlook.com/141/gg709759.aspx?sl=1)。

## <a name="next-steps"></a>后续步骤

如果此处未解答您的问题，则可以使用以下支持选项：

* 在 [Microsoft 支持知识库](https://support.microsoft.com)中搜索常见技术问题的解决方法。
* 搜索和浏览来自社区的技术问题与解答，或在[Azure Active Directory Q&](https://docs.microsoft.com/answers/topics/azure-active-directory.html)提出自己的问题。
* 通过[Azure 多重身份验证服务器支持](https://support.microsoft.com/oas/default.aspx?prid=14947)联系 Microsoft professional。 与我们联系时，尽可能包含有关问题的更多信息将很有帮助。 可提供的信息包括看到错误的页面、特定错误代码、特定会话 ID 和看到错误的用户的 ID。
* 如果你是旧版 PhoneFactor 客户，并且你有疑问或需要重置密码方面的帮助，请使用 [phonefactorsupport@microsoft.com](mailto:phonefactorsupport@microsoft.com) 电子邮件地址来打开支持案例。
