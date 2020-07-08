---
title: 身份验证方法和功能 - Azure Active Directory
description: 了解 Azure Active Directory 中可用的不同身份验证方法和功能，以帮助改进和保护登录事件
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
ms.openlocfilehash: 642f2705f54fe8f84cfde7ff039c9a723be59595
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83770953"
---
# <a name="what-authentication-and-verification-methods-are-available-in-azure-active-directory"></a>Azure Active Directory 中有哪些可用的身份验证和验证方法？

作为 Azure Active Directory (Azure AD) 中帐户的登录体验的一部分，用户可以通过不同的方式进行身份验证。 用户名和密码是用户历来提供凭据的最常见方式。 借助 Azure AD 中的新式身份验证和安全功能，可以使用其他身份验证方法作为基本密码的补充或替代方法。

Azure AD 中的用户可以选择使用以下身份验证方法之一进行身份验证：

* 传统用户名和密码
* Microsoft Authenticator 应用无密码登录
* OATH 硬件令牌或 FIDO2 安全密钥
* 基于 SMS 的无密码登录

Azure AD 中的许多帐户启用了自助服务密码重置 (SSPR) 或 Azure 多重身份验证。 这些功能包括其他验证方法，如电话呼叫或安全问题。 建议你要求用户注册多个验证方法。 当一个方法对用户不可用时，他们可以选择使用另一种方法进行身份验证。

下表概述了可用于主身份验证或辅助身份验证的方法：

| 方法 | 主要身份验证 | 辅助身份验证 |
| --- | --- | --- |
| [密码](#password) | 是 | |
| [Microsoft Authenticator 应用](#microsoft-authenticator-app) | 是（预览版） | MFA 和 SSPR |
| [FIDO2 安全密钥（预览版）](#fido2-security-keys) | 是 | 仅 MFA |
| [OATH 软件令牌](#oath-software-tokens) | 否 | MFA |
| [OATH 硬件令牌（预览版）](#oath-hardware-tokens-preview) | 是 | MFA |
| [SMS](#phone-options) | 是（预览版） | MFA 和 SSPR |
| [语音呼叫](#phone-options) | 否 | MFA 和 SSPR |
| [安全性问题](#security-questions) | 否 | 仅 SSPR |
| [电子邮件地址](#email-address) | 否 | 仅 SSPR |
| [应用密码](#app-passwords) | 否 | 仅限在某些情况下执行 MFA |

本文概述了可在 Azure AD 中使用的这些不同的身份验证方法以及特定的限制或限制（如有）。

![登录屏幕上使用的身份验证方法](media/concept-authentication-methods/overview-login.png)

## <a name="password"></a>密码

Azure AD 密码通常是主要身份验证方法之一。 不能禁用密码身份验证方法。

即使使用了[基于 SMS 的登录](howto-authentication-sms-signin.md)方法这类不使用密码进行登录的身份验证方法，密码仍是可用的身份验证方法。

## <a name="microsoft-authenticator-app"></a>Microsoft Authenticator 应用

Authenticator 应用为 Azure AD 工作或学校帐户或 Microsoft 帐户提供附加的安全级别，并可用于 [Android](https://go.microsoft.com/fwlink/?linkid=866594)、[iOS](https://go.microsoft.com/fwlink/?linkid=866594) 和 [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6)。 使用 Microsoft Authenticator 应用，用户可以在登录期间以无密码方式进行身份验证，或在自助服务密码重置 (SSPR) 或 Azure 多重身份验证事件期间将这种方式作为附加的身份验证选项使用。

用户可能会通过移动应用收到通知，并在其中批准或拒绝，或使用 Authenticator 应用生成可在登录界面中输入的 OATH 验证码。 如果同时启用了通知和验证码，注册 Authenticator 应用的用户可以使用任一方法验证其身份。

若想在出现登录提示时使用 Authenticator 应用而不是用户名和密码的组合，请参阅[使用 Microsoft Authenticator 应用启用无密码登录（预览）](howto-authentication-passwordless-phone.md)。

> [!NOTE]
> 用户启用 SSPR 后不会得到用于注册其移动应用的选项。 而用户可以在 [https://aka.ms/mfasetup](https://aka.ms/mfasetup) 或通过 [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) 的组合的安全信息注册过程注册他们的移动应用。

### <a name="notification-through-mobile-app"></a>通过移动应用发送通知

Authenticator 应用通过将通知推送到智能手机或平板电脑，可帮助防止对帐户进行未经授权的访问，以及停止欺诈性交易。 用户将查看通知，如果信息合法，则选择“验证”。 否则，可以选择“拒绝”。

![Web 浏览器提示示例的屏幕截图，其中提示用户处理 Authenticator 应用通知以完成登录过程](media/tutorial-enable-azure-mfa/azure-multi-factor-authentication-browser-prompt.png)

> [!NOTE]
> 如果你的组织有员工在中国工作或要前往中国，则 Android 设备上的移动应用通知方法在该国家/地区不起作用。 应向这些用户提供备用身份验证方法。

### <a name="verification-code-from-mobile-app"></a>通过移动应用发送验证码

Authenticator 应用可用作生成 OATH 验证码所需的软件令牌。 输入用户名和密码后，在登录界面中输入 Authenticator 应用提供的代码。 验证码提供了第二种形式的身份验证。

用户可以具有最多 5 个 OATH 硬件令牌或验证器应用程序（如配置为可随时使用的 Microsoft Authenticator 应用）的组合。

> [!WARNING]
> 对于自助密码重置，如果只需使用一种方法来执行重置，则验证码是可供用户用于确保最高级别安全性的唯一选项。
>
> 如果需要两种方法，则用户可以使用通知或验证码进行重置，此外还能使用其他任何已启用的方法。

## <a name="fido2-security-keys"></a>FIDO2 安全密钥

FIDO（线上快速身份验证）联盟可帮助推广开放式身份验证标准，并减少将密码作为用户身份验证方式的情况。 FIDO2 是采用了 Web 身份验证 (WebAuthn) 标准的最新标准。

若要在出现登录提示时使用 FIDO2 安全密钥而不是用户名和密码的组合，请参阅[启用无密码 FIDO2 安全密钥登录（预览）](howto-authentication-passwordless-security-key.md)。

用户可以进行注册，然后在登录界面选择 FIDO2 安全密钥作为主要的身份验证方式。 这些 FIDO2 安全密钥通常是 USB 设备，但也可以使用蓝牙或 NFC。 使用处理身份验证的硬件设备，由于不使用可能被公开或猜到的密码，帐户的安全性会提高。

Azure AD 中的 FIDO2 安全密钥当前处于预览状态。 有关预览版的详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="oath-tokens"></a>OATH 令牌

OATH TOTP（基于时间的一次性密码）是一个开放标准，用于指定如何生成一次性密码 (OTP) 代码。 可以通过使用软件或硬件生成代码来实现 OATH TOTP。 Azure AD 不支持 OATH HOTP，这是另一种代码生成标准。

### <a name="oath-software-tokens"></a>OATH 软件令牌

软件 OATH 令牌通常是应用程序，如 Microsoft Authenticator 应用和其他验证器应用。 Azure AD 生成密钥或种子，密钥或种子会被输入应用中，并用于生成每个 OTP。

Authenticator 应用在设置为发送推送通知后会自动生成代码，以便用户即使在设备没有连接时，也可以有一个备份。 还可以使用通过 OATH TOTP 生成代码的第三方应用程序。

某些 OATH TOTP 硬件令牌是可编程的，这意味着它们不附带密钥或预编程的种子。 这些可编程硬件令牌可以使用从软件令牌设置流获取的密钥或种子进行设置。 客户可以从自己选择的供应商处购买这些令牌，并在供应商的设置过程中使用密钥或种子。

### <a name="oath-hardware-tokens-preview"></a>OATH 硬件令牌（预览版）

Azure AD 支持使用每 30 或 60 秒刷新一次代码的 OATH-TOTP SHA-1 令牌。 客户可以从所选的供应商处购买这些令牌。

OATH TOTP 硬件令牌通常附带一个在令牌中经过预编程的密钥或种子。 必须将这些密钥输入 Azure AD 中，如以下步骤所述。 密钥限制为 128 个字符，可能不会与所有令牌兼容。 密钥只能包含字符“a”-“z”或“A”-“Z”，以及数字“1”-“7”，并且必须采用“Base32”编码   。

也可以在软件令牌设置流中使用 Azure AD 设置可重新设定种子的可编程的 OATH TOTP 硬件令牌。

OATH 硬件令牌目前作为公共预览版的一部分受支持。 有关预览版的详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

![将 OATH 令牌上传到 MFA OATH 令牌边栏选项卡](media/concept-authentication-methods/mfa-server-oath-tokens-azure-ad.png)

获取令牌后，必须以逗号分隔值 (CSV) 文件格式将其上传，包括 UPN、序列号、密钥、时间间隔、制造商以及型号，如以下示例所示：

```csv
upn,serial number,secret key,time interval,manufacturer,model
Helga@contoso.com,1234567,1234567abcdef1234567abcdef,60,Contoso,HardwareKey
```

> [!NOTE]
> 请确保 CSV 文件中包含标题行。

正确格式化为 CSV 文件后，管理员便可以登录 Azure 门户并导航到“Azure Active Directory”>“MFA”>“OATH 令牌”，然后上传生成的 CSV 文件。

根据 CSV 文件的大小，这可能需要花费几分钟来处理。 选择“刷新”按钮可获取当前状态。 如果文件中有任何错误，则可以下载 CSV 文件，其中列出了需要解决的所有错误。 下载的 CSV 文件中的字段名称与上传的版本不同。

解决所有错误后，管理员可以对令牌选择“激活”，然后输入令牌上显示的 OTP，以此来激活每个密钥。

用户可以具有最多 5 个 OATH 硬件令牌或验证器应用程序（如配置为可随时使用的 Microsoft Authenticator 应用）的组合。

## <a name="phone-options"></a>电话选项

对于使用文本消息的直接身份验证，可以[为用户配置和启用基于 SMS 的身份验证（预览）](howto-authentication-sms-signin.md)。 基于 SMS 的登录方式对于一线工作人员来说非常适合。 使用基于 SMS 的登录，用户无需知道用户名和密码即可访问应用程序和服务。 用户的验证方式改为：输入其注册的手机号码，收到带有验证码的短信，并在登录界面中输入该验证码。

用户还可以在 Azure 多重身份验证或自助服务密码重置 (SSPR) 期间使用移动电话或办公室电话号码作为辅助的身份验证形式进行身份验证。

若要正常工作，电话号码必须采用“+国家/地区代码 电话号码”格式，例如 +1 4251234567 。

> [!NOTE]
> 在国家/地区代码和电话号码之间需要有一个空格。
>
> 密码重置不支持电话分机。 即使采用“1 4251234567X12345”格式，在拨出电话前也会删除分机。

### <a name="mobile-phone-verification"></a>手机验证

对于 Azure 多重身份验证或 SSPR，用户可以选择接收短信，其中含有要在登录界面中输入的验证码，或者选择接听电话呼叫，接听后会听到一个提示，提示其输入事先定义的 PIN 码。

如果用户不希望在目录中公开其移动电话号码，但想使用该号码来重置密码，则管理员不应在目录中填充该号码。 而是由用户通过组合的安全信息注册 ([https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo)) 来填充“身份验证电话”属性。 管理员可在用户的配置文件中看到此信息，但此信息不会发布到其他位置。

![屏幕截图显示已填充电话号码的身份验证方法的 Azure 门户](media/concept-authentication-methods/user-authentication-methods.png)

Microsoft 不保证相同号码传送的短信或基于语音的 Azure 多重身份验证提示一致。 为了用户的利益，我们在做出路线调整期间可能随时添加或删除简短代码，以提高短信传送能力。 Microsoft 不支持除美国和加拿大以外的国家/地区的简短代码。

#### <a name="text-message-verification"></a>短信验证

如果在 SSPR 或 Azure 多重身份验证期间使用短信验证方法，相应的手机号会接收到包含验证码的 SMS。 若要完成登录过程，需要在登录界面中输入提供的验证码。

#### <a name="phone-call-verification"></a>电话呼叫验证

如果在 SSPR 或 Azure 多重身份验证期间使用电话呼叫验证方法，用户注册的电话号码会接收到一个自动语音呼叫。 为了完成登录过程，系统会提示用户在其键盘上输入其 pin 号码并按 # 键。

### <a name="office-phone-verification"></a>办公电话验证

办公电话属性由 Azure AD 管理员管理，用户本身无法注册。

如果在 SSPR 或 Azure 多重身份验证期间使用电话呼叫验证方法，用户注册的电话号码会接收到一个自动语音呼叫。 为了完成登录过程，系统会提示用户在其键盘上输入其 pin 号码并按 # 键。

### <a name="troubleshooting-phone-options"></a>排查电话选项问题

如果在使用 Azure AD 的手机身份验证时出现问题，请查看以下故障排除步骤：

* 单个设备上阻止了呼叫方 ID。
   * 查看设备上配置的任何被阻拦的号码。
* 电话号码错误或国家/地区代码不正确，或混淆个人电话号码和工作电话号码。
   * 对用户对象和配置的身份验证方法进行故障排除。 确保注册了正确的电话号码。
* 输入了错误的 PIN。
   * 确认用户使用的是注册帐户时所用的正确的 PIN。
* 呼叫被转到语音信箱。
   * 确保用户的电话处于开启状态，并且所需服务在其区域中可用，或使用替代方法。
* 用户被阻止
   * 让 Azure AD 管理员在 Azure 门户中取消阻止该用户。
* 设备上未订阅 SMS。
   * 让用户更改方法或在设备上激活 SMS。
* 电信提供程序有问题，如检测不到电话输入、DTMF 音调缺失的问题、在多台设备上阻止呼叫方 ID 或在多台设备阻止 SMS。
   * Microsoft 使用多个电信提供程序来路由电话呼叫和短信以进行身份验证。 如果发生上述任何问题，请让用户尝试在 5 分钟内至少使用此方法 5 次，并在联系 Microsoft 支持人员时提供该用户的信息。

## <a name="security-questions"></a>安全提问

在登录事件期间，不会使用安全问题作为身份验证方法。 而是在自助服务密码重置 (SSPR) 过程中，可以使用安全问题来确认用户身份。 管理员帐户不能使用安全问题作为 SSPR 的验证方法。

当用户注册 SSPR 时，系统会提示他们选择要使用的身份验证方法。 如果他们选择使用安全问题，他们需要从一组提示问题中进行选择，然后提供自己的答案。

![Azure 门户的屏幕截图显示身份验证方法和安全问题的选项](media/concept-authentication-methods/security-questions-authentication-method.png)

> [!NOTE]
> 安全问题以专用、安全的方式存储在目录的用户对象上，并且只能由用户在注册期间回答。 管理员无法读取或修改用户的问题或答案。

因为某些人可能知道其他用户的问题的答案，因此这种方法可能比其他方法更不安全。 如果在 SSPR 中使用安全问题，建议在使用这些问题的同时结合另一种方法。 可以提示用户使用 Microsoft Authenticator 应用或电话身份验证在 SSPR 过程中验证其身份，并且仅当用户的手机或注册设备不在身边时才选择使用安全问题。

### <a name="predefined-questions"></a>预定义问题

以下预定义的安全问题可作为 SSPR 的验证方法使用。 所有这些安全问题都已根据用户的浏览器区域设置翻译并本地化为 Office 365 的完整语言集：

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
* 最年长的兄弟姐妹的生日年份和月份是什么？ （例如：1985 年 11 月）
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

为了增加灵活性，你可以定义自己的自定义安全问题。 自定义安全问题的最大长度为 200 个字符。

自定义安全问题不会像默认安全问题那样自动进行本地化。 所有自定义问题的显示语言是在管理用户界面中输入这些问题时所用的语言，即使用户浏览器的区域设置与此不同。 如果需要本地化的问题，应使用预定义的问题。

### <a name="security-question-requirements"></a>安全问题要求

对于默认和自定义安全问题，适用以下要求和限制：

* 答案包含的字符数下限为 3 个字符。
* 答案包含的字符数上限为 40 个字符。
* 用户不能多次回答同一问题。
* 用户不能为多个问题提供相同的答案。
* 可以使用任何字符集来定义问题和答案（包括 Unicode 字符）。
* 所定义问题的数量必须大于或等于注册所需问题数量。

## <a name="email-address"></a>电子邮件地址

电子邮件地址不能用作直接身份验证方法。 电子邮件地址仅可作为自助密码重置 (SSPR) 的验证选项提供。 如果选择在 SSPR 期间使用电子邮件地址，会向用户发送电子邮件以完成身份验证/验证过程。

在 SSPR 注册期间，用户提供要使用的电子邮件地址。 建议他们使用与其公司帐户不同的电子邮件帐户，以确保在 SSPR 期间可以访问该帐户。

## <a name="app-passwords"></a>应用密码

某些较旧的非浏览器应用不理解身份验证过程中的暂停或中断环节。 如果为某个用户启用了多重身份验证，且该用户尝试使用某个较旧的非浏览器应用，他们通常无法成功进行身份验证。 如果使用应用密码，用户能够继续使用较旧的非浏览器应用成功进行身份验证，而不会发生中断。

默认情况下，用户无法创建应用密码。 如果需要允许用户创建应用密码，请在“服务设置”下为用户的 Azure 多重身份验证属性选择“允许用户创建应用密码以登录非浏览器应用”。

![Azure 门户的屏幕截图，其中显示了允许用户使用应用密码进行多重身份验证的服务设置](media/concept-authentication-methods/app-password-authentication-method.png)

如果通过条件访问策略而不是通过每用户 MFA 强制实施 Azure 多重身份验证，则无法创建应用密码。 使用条件访问策略控制访问权限的新式应用程序不需要应用密码。

如果组织与 Azure AD 联合进行单一登录 (SSO)，并且你使用 Azure 多重身份验证，则存在以下注意事项：

* 应用密码由 Azure AD 进行验证，从而绕过了联合。 仅在设置应用密码时，才会使用联合。 对于联合 (SSO) 用户，密码存储在组织 ID 中。 如果用户离开公司，相应信息必须通过 DirSync 流向组织 ID。 帐户禁用或删除事件可能需要长达三个小时才能同步，从而延迟了 Azure AD 中应用密码的禁用/删除。
* 应用密码不遵循“本地客户端访问控制”设置。
* 没有为应用密码提供本地身份验证日志记录或审核功能。
* 使用多重身份验证时，某些先进的体系结构设计可能需要将组织用户名和密码与应用密码结合使用，具体取决于进行身份验证的位置。
    * 对于针对本地基础结构进行身份验证的客户端，会使用组织用户名和密码。
    * 对于针对 Azure AD 进行身份验证的客户端，会使用应用密码。

## <a name="next-steps"></a>后续步骤

若要开始，请参阅[自助式密码重置 (SSPR) 的教程][tutorial-sspr]和 [Azure 多重身份验证][tutorial-azure-mfa]。

要详细了解 SSPR 概念，请参阅[ Azure AD 自助式密码重置的工作原理][concept-sspr]。

要详细了解 MFA 概念，请参阅[ Azure 多重身份验证的工作原理][concept-mfa]。

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md