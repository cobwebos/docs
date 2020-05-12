---
title: 身份验证方法和功能-Azure Active Directory
description: 了解 Azure Active Directory 中提供的不同身份验证方法和功能，以帮助改进和安全登录事件
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.collection: M365-identity-device-management
ms.custom: contperfq4
ms.openlocfilehash: 3947bf0dcad598bf52a742c790a2f99538d6facb
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83116356"
---
# <a name="what-authentication-and-verification-methods-are-available-in-azure-active-directory"></a>Azure Active Directory 中提供了哪些身份验证和验证方法？

作为 Azure Active Directory （Azure AD）中帐户的登录体验的一部分，用户可以通过不同的方式对其自身进行身份验证。 用户名和密码是用户过去提供凭据的最常见方法。 通过 Azure AD 中的新式身份验证和安全功能，可补充基本密码或将其替换为其他身份验证方法。

Azure AD 中的用户可以选择使用以下身份验证方法之一进行身份验证：

* 传统用户名和密码
* Microsoft Authenticator 应用无密码登录
* OATH 硬件令牌或 FIDO2 安全密钥
* 基于 SMS 的无密码登录

Azure AD 中的许多帐户都启用了自助密码重置（SSPR）或 Azure 多重身份验证。 这些功能包括其他验证方法（如电话呼叫或安全问题）。 建议你要求用户注册多个验证方法。 当某个方法不可用于用户时，他们可以选择使用其他方法进行身份验证。

下表列出了可用于不同方案的身份验证或验证方法：

| 方法 | 登录时使用 | 在验证期间使用 |
| --- | --- | --- |
| [密码](#password) | 是 | MFA 和 SSPR |
| [Microsoft Authenticator 应用](#microsoft-authenticator-app) | 是（预览版） | MFA 和 SSPR |
| [FIDO2 安全密钥（预览版）](#fido2-security-keys) | 是 | 仅 MFA |
| [OATH 硬件令牌（预览）](#oath-hardware-tokens) | 是 | SSPR 和 MFA |
| [SMS](#phone-options) | 是（预览版） | MFA 和 SSPR |
| [语音呼叫](#phone-options) | 否 | MFA 和 SSPR |
| [安全提问](#security-questions) | 否 | 仅 SSPR |
| [电子邮件地址](#email-address) | 否 | 仅 SSPR |
| [应用密码](#app-passwords) | 否 | 仅限在某些情况下执行 MFA |

本文概述 Azure AD 和任何特定限制或限制中提供的各种不同的身份验证方法。

![登录屏幕上使用的身份验证方法](media/concept-authentication-methods/overview-login.png)

## <a name="password"></a>密码

Azure AD 密码通常是主要身份验证方法之一。 不能禁用密码身份验证方法。

即使在用户不使用其密码进行签名时使用身份验证方法（例如[基于短信的登录](howto-authentication-sms-signin.md)），也会将密码保留为可用的身份验证方法。

## <a name="microsoft-authenticator-app"></a>Microsoft Authenticator 应用

验证器应用为 Azure AD 工作或学校帐户或 Microsoft 帐户提供额外的安全级别，适用于[Android](https://go.microsoft.com/fwlink/?linkid=866594)、 [iOS](https://go.microsoft.com/fwlink/?linkid=866594)和[Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6)。 使用 Microsoft Authenticator 应用程序时，用户可以无密码的方式进行身份验证，或者在自助服务密码重置（SSPR）或 Azure 多重身份验证事件期间作为附加验证选项进行身份验证。

用户可能会通过移动应用收到通知，告知他们批准或拒绝，或使用验证器应用生成可在登录界面中输入的 OATH 验证码。 如果同时启用了通知和验证代码，则注册验证器应用的用户可以使用任一方法来验证其身份。

若要在登录提示而不是用户名和密码组合中使用验证器应用，请参阅[使用 Microsoft Authenticator 应用启用无密码登录（预览版）](howto-authentication-passwordless-phone.md)。

> [!NOTE]
> 如果用户启用了 SSPR，则不能选择注册其移动应用。 相反，用户可以在中注册其移动应用， [https://aka.ms/mfasetup](https://aka.ms/mfasetup) 或将其作为组合的安全信息注册的一部分进行注册 [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) 。

### <a name="notification-through-mobile-app"></a>通过移动应用发送通知

验证器应用可通过将通知推送到智能手机或平板电脑，帮助防止对帐户进行未经授权的访问并停止欺诈性交易。 用户查看通知，如果合法，则选择 "**验证**"。 否则，他们可以选择 "**拒绝**"。

![示例 web 浏览器提示身份验证器应用通知完成登录过程的屏幕截图](media/tutorial-enable-azure-mfa/azure-multi-factor-authentication-browser-prompt.png)

> [!NOTE]
> 如果你的组织有员工在中国工作或出差，则通过 Android 设备上的*移动应用方法发出的通知*在该国家/地区不起作用。 应为这些用户提供备用身份验证方法。

### <a name="verification-code-from-mobile-app"></a>通过移动应用发送验证码

验证器应用可用作软件令牌来生成 OATH 验证码。 输入用户名和密码后，在登录界面中输入验证器应用提供的代码。 验证码提供了第二种形式的身份验证。

用户最多可将五个 OATH 硬件令牌或验证器应用程序（例如 Microsoft Authenticator 应用）组合在一起使用。

> [!WARNING]
> 若要确保重置仅需要一个方法时的自助密码重置的最高安全级别，用户只能使用验证码。
>
> 当需要两个方法时，用户可以使用通知或验证代码以及任何其他已启用的方法重置。

## <a name="fido2-security-keys"></a>FIDO2 安全密钥

FIDO （快速标识在线）联盟有助于提升开放式身份验证标准，并将密码的用户减少为一种身份验证形式。 FIDO2 是包含 web 身份验证（WebAuthn）标准的最新标准。

若要在登录提示而不是用户名和密码组合中使用 FIDO2 安全密钥，请参阅[Enable 无密码 FIDO2 security key 登录（预览版）](howto-authentication-passwordless-security-key.md)。

用户可以在登录界面注册并选择 FIDO2 安全密钥作为身份验证的主要方式。 这些 FIDO2 的安全密钥通常是 USB 设备，但也可以使用蓝牙或 NFC。 如果硬件设备处理身份验证，则会增加帐户的安全性，因为没有可公开或被猜到的密码。

Azure AD 中的 FIDO2 安全密钥目前为预览版。 有关预览的详细信息，请参阅[Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="oath-hardware-tokens"></a>OATH 硬件令牌

OATH 是一个开放标准，用于指定如何生成一次性密码 (OTP) 代码。 Azure AD 支持使用具有30秒或60秒的 TOTP 的 OATH-1 令牌。 客户可以从其选择的供应商处购买这些令牌。

机密密钥限制为128个字符，它们可能与所有令牌都不兼容。 密钥只能*包含字符 a-z*或 a-z 以及数字*1-7* *，并且必须*在*Base32*中进行编码。

Azure AD 中的 OATH 硬件令牌目前为预览版。 有关预览的详细信息，请参阅[Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

![将 OATH 令牌上传到 MFA OATH 令牌窗口](media/concept-authentication-methods/mfa-server-oath-tokens-azure-ad.png)

获取令牌后，必须以逗号分隔值（CSV）文件格式（包括 UPN、序列号、密钥、时间间隔、制造商和型号）上传这些令牌，如以下示例中所示：

```csv
upn,serial number,secret key,time interval,manufacturer,model
Helga@contoso.com,1234567,1234567abcdef1234567abcdef,60,Contoso,HardwareKey
```

> [!NOTE]
> 请确保 CSV 文件中包含标题行。

格式化为 CSV 文件后，管理员可以登录到 Azure 门户，导航到**Azure Active Directory**  >  **安全**  >  **MFA**  >  **OATH 令牌**，并上传生成的 CSV 文件。

根据 CSV 文件的大小，这可能需要花费几分钟来处理。 选择 "**刷新**" 按钮以获取当前状态。 如果文件中有任何错误，你可以下载一个 CSV 文件，其中列出了你要解决的任何错误。 下载的 CSV 文件中的字段名称与上传的版本不同。

一旦解决了任何错误，管理员就可以通过选择 "**激活**" 标记并输入标记上显示的 OTP，来激活每个密钥。

用户最多可将五个 OATH 硬件令牌或验证器应用程序（例如 Microsoft Authenticator 应用）组合在一起使用。

## <a name="phone-options"></a>电话选项

对于使用短信的直接身份验证，你可以[为基于 SMS 的身份验证（预览版）配置和启用用户](howto-authentication-sms-signin.md)。 基于 SMS 的登录非常适合于前端工作人员。 通过基于 SMS 的登录，用户无需知道用户名和密码即可访问应用程序和服务。 用户改为输入其已注册的移动电话号码，接收一条包含验证码的短信，并在登录界面中输入该号码。

用户还可以使用移动电话或办公电话验证自己的身份，作为在 Azure 多重身份验证或自助服务密码重置（SSPR）期间使用的辅助身份验证形式。

若要正常工作，电话号码必须采用 " *+ CountryCode PhoneNumber*" 格式（例如 *+ 1 4251234567*）。

> [!NOTE]
> 在国家/地区代码和电话号码之间需要有一个空格。
>
> 密码重置不支持电话分机号码。 即使是在 *+ 1 4251234567X12345*格式中，也会在调用之前删除扩展。

### <a name="mobile-phone-verification"></a>手机验证

对于 Azure 多重身份验证或 SSPR，用户可以选择接收包含验证码的短信，使其在登录界面中输入，或接收电话呼叫并提示输入其定义的 pin 代码。

如果用户不希望在目录中显示其移动电话号码，但想要将其用于密码重置，则管理员不应在目录中填充该电话号码。 相反，用户应通过中的组合安全信息注册来填充其**身份验证电话**属性 [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) 。 管理员可在用户的配置文件中看到此信息，但此信息不会发布到其他位置。

![显示带电话号码的身份验证方法的 Azure 门户屏幕截图](media/concept-authentication-methods/user-authentication-methods.png)

Microsoft 不保证按相同的号码提供一致的短信或基于语音的 Azure 多重身份验证提示。 在我们的用户感兴趣的情况下，我们可以随时添加或删除短代码，因为我们将进行路线调整以改善 SMS 传送。 除美国和加拿大以外，Microsoft 不支持国家/地区的短代码。

#### <a name="text-message-verification"></a>短信验证

在 SSPR 或 Azure 多重身份验证过程中，通过短信验证，会向包含验证码的手机号码发送一条短信。 若要完成登录过程，请在登录界面中输入提供的验证码。

#### <a name="phone-call-verification"></a>电话呼叫验证

通过在 SSPR 或 Azure 多重身份验证期间进行电话呼叫验证，会自动对用户注册的电话号码进行语音呼叫。 若要完成登录过程，系统会提示用户输入其 pin 号，并在其键盘上输入 #。

### <a name="office-phone-verification"></a>办公室电话验证

"办公电话" 属性由 Azure AD 管理员管理，不能由用户自行注册。

通过在 SSPR 或 Azure 多重身份验证期间进行电话呼叫验证，会自动对用户注册的电话号码进行语音呼叫。 若要完成登录过程，系统会提示用户输入其 pin 号，并在其键盘上输入 #。

### <a name="troubleshooting-phone-options"></a>排查电话选项问题

如果 Azure AD 的手机身份验证有问题，请查看以下故障排除步骤：

* 单个设备上阻止的呼叫方 ID。
   * 查看在设备上配置的任何被阻止的号码。
* 错误的电话号码或国家/地区代码错误，或者个人电话号码与工作电话号码之间存在混淆。
   * 排查用户对象和配置的身份验证方法。 请确保注册了正确的电话号码。
* 输入的 PIN 错误。
   * 确认用户已使用为其帐户注册的正确 PIN。
* 呼叫转发到语音邮件。
   * 请确保用户已打开手机并且该服务在其区域中可用，或使用替代方法。
* 用户被阻止
   * 让 Azure AD 管理员在 Azure 门户中取消锁定用户。
* 设备上未订阅短信。
   * 让用户更改方法或激活设备上的 SMS。
* 错误的电信提供程序，例如未检测到任何电话输入、缺少 DTMF 音问题、在多个设备上阻止了调用方 ID 或在多个设备上阻止了短信。
   * Microsoft 使用多个电信提供商路由电话呼叫和短信以进行身份验证。 如果出现上述任何问题，请让用户在5分钟内尝试至少使用一次此方法，并在联系 Microsoft 支持部门时获得该用户的信息。

## <a name="security-questions"></a>安全提问

在登录事件期间，不会将安全问题用作身份验证方法。 相反，可以在自助服务密码重置（SSPR）过程中使用安全问题来确认您的身份。 对于 SSPR，管理员帐户不能使用安全问题作为验证方法。

当用户注册 SSPR 时，系统会提示他们选择要使用的身份验证方法。 如果他们选择使用安全问题，他们会从一组问题中进行选择，以提示你，然后提供其自己的答案。

![显示安全问题的身份验证方法和选项的 Azure 门户屏幕截图](media/concept-authentication-methods/security-questions-authentication-method.png)

> [!NOTE]
> 安全问题以专用、安全的方式存储在目录的用户对象上，并且只能由用户在注册期间回答。 管理员无法读取或修改用户的问题或答案。

因为某些人可能知道其他用户的问题的答案，因此这种方法可能比其他方法更不安全。 如果对 SSPR 使用安全问题，建议将它们与其他方法结合使用。 在 SSPR 过程中，系统可能会提示用户使用 Microsoft Authenticator 应用或手机身份验证来验证其身份，并且仅当他们没有手机或注册的设备时，才选择安全问题。

### <a name="predefined-questions"></a>预定义问题

以下预定义的安全问题可用作 SSPR 的验证方法。 所有这些安全问题都已根据用户的浏览器区域设置翻译和本地化为一整套 Office 365 语言：

* 在哪个城市遇到了第一任配偶/伴侣？
* 父母在哪个城市相识？
* 与你年龄最近的兄弟姐妹居住在哪个城市？
* 父亲在哪个城市出生？
* 第一份工作是在哪个城市？
* 母亲在哪个城市出生？
* 2000 年元旦你在哪个城市？
* 中学时你最喜欢的老师姓什么？
* 填报了志愿，但未能入学的大学名称是什么？
* 举办第一次婚宴的地名是什么？
* 父亲的中间名是什么？
* 最喜欢的食品是什么？
* 外婆的姓氏和名字是什么？
* 母亲的中间名是什么？
* 最年长的兄弟姐妹的生日年份和月份是什么？ （例如，1985年11月）
* 最年长的兄弟姐妹的中间名是什么？
* 爷爷的姓氏和名字是什么？
* 最年幼的兄弟姐妹的中间名是什么？
* 六年级时你就读哪所学校？
* 童年时最好的朋友的名字和姓氏是什么？
* 第一位伴侣的名字和姓氏是什么？
* 最喜欢的小学老师姓什么？
* 第一辆汽车或摩托车是哪个品牌和型号？
* 就读的第一所学校的校名是什么？
* 在哪家医院出生？
* 童年时第一个家庭地址的街道名称是什么？
* 童年时崇拜的人是谁？
* 最喜欢哪种填充动物玩具？
* 第一个宠物叫什么？
* 童年时的绰号是什么？
* 在中学最喜欢的运动是什么？
* 第一份工作是什么？
* 童年时的电话号码最后四位数是什么？
* 年少时，希望长大后当什么？
* 遇到过的最有名的人是谁？

### <a name="custom-security-questions"></a>自定义安全问题

为了获得更大的灵活性，你可以定义自己的自定义安全问题。 自定义安全问题的最大长度为 200 个字符。

自定义安全问题没有按默认安全问题自动进行本地化。 所有自定义问题的显示语言都与在管理用户界面中输入的相同，即使用户的浏览器区域设置不同也是如此。 如果需要本地化的问题，应使用预定义的问题。

### <a name="security-question-requirements"></a>安全问题要求

对于默认的和自定义安全问题，以下要求和限制如下：

* 答案包含的字符数下限为 3 个字符。
* 答案包含的字符数上限为 40 个字符。
* 用户不能多次回答同一问题。
* 用户不能为多个问题提供相同的答案。
* 可以使用任何字符集来定义问题和答案（包括 Unicode 字符）。
* 所定义问题的数量必须大于或等于注册所需问题数量。

## <a name="email-address"></a>电子邮件地址

电子邮件地址不能用作直接身份验证方法。 电子邮件地址仅可用作自助服务密码重置（SSPR）的验证选项。 在 SSPR 期间选择电子邮件地址后，将向用户发送一封电子邮件以完成身份验证/验证过程。

注册 SSPR 期间，用户提供要使用的电子邮件地址。 建议他们使用不同于公司帐户的电子邮件帐户，以确保他们能够在 SSPR 期间进行访问。

## <a name="app-passwords"></a>应用密码

某些较早的非浏览器应用不理解身份验证过程中的暂停或中断。 如果用户已启用多重身份验证，并尝试使用这些较旧的非浏览器应用之一，则通常无法成功进行身份验证。 应用密码可让用户在不中断的情况下继续使用较旧的非浏览器应用成功进行身份验证。

默认情况下，用户无法创建应用密码。 如果需要允许用户创建应用密码，请在 "*服务设置*" 下选择 "**允许用户创建应用密码来登录非浏览器应用**"，以用于用户的 Azure 多重身份验证属性。

![Azure 门户的屏幕截图，显示多重身份验证的服务设置以允许用户应用密码](media/concept-authentication-methods/app-password-authentication-method.png)

如果使用条件访问策略而不是通过每用户 MFA 强制执行 Azure 多重身份验证，则无法创建应用密码。 使用条件访问策略控制访问的新式应用程序不需要应用密码。

如果你的组织与 Azure AD 的单一登录（SSO）联合，并使用 Azure 多重身份验证，则需要考虑以下事项：

* 应用密码由 Azure AD 进行验证，因此绕过联合。 仅在设置应用密码时，才会使用联合。 对于联合 (SSO) 用户，密码存储在组织 ID 中。 如果用户离开公司，相应信息必须通过 DirSync 流向组织 ID。 帐户禁用或删除事件可能需要长达三个小时才能同步，这会延迟 Azure AD 中应用密码的禁用/删除。
* 应用密码不遵守本地客户端访问控制设置。
* 无本地身份验证日志记录或审核功能可用于应用密码。
* 当使用多重身份验证时，某些先进的体系结构设计可能需要将组织用户名和密码与应用密码结合使用，具体取决于身份验证的位置。
    * 对于针对本地基础结构进行身份验证的客户端，会使用组织用户名和密码。
    * 对于针对 Azure AD 进行身份验证的客户端，会使用应用密码。

## <a name="next-steps"></a>后续步骤

若要开始，请参阅[自助式密码重置 (SSPR) 的教程][tutorial-sspr]和 [Azure 多重身份验证][tutorial-azure-mfa]。

若要了解有关 SSPR 概念的详细信息，请参阅[Azure AD 自助服务密码重置的工作方式][concept-sspr]。

若要详细了解 MFA 概念，请参阅[Azure 多重身份验证的工作原理][concept-mfa]。

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md