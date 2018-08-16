---
title: Azure AD 身份验证方法
description: Azure AD 中有哪些可用的 MFA 和 SSPR 身份验证方法
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry, michmcla
ms.openlocfilehash: 4a0fb5739812950f3a01a18acae1d797fa4f9158
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2018
ms.locfileid: "39622246"
---
# <a name="what-are-authentication-methods"></a>有哪些身份验证方法？

Azure AD 自助密码重置 (SSPR) 和多重身份验证 (MFA) 可能要求提供附加的信息（称为身份验证方法或安全信息），以便在你使用关联的功能时证明自己的身份。

管理员可在策略中定义 SSPR 和 MFA 用户能够使用哪些身份验证方法。 某些身份验证方法不一定适用于所有功能。

Microsoft 强烈建议管理员允许用户选择超过最小所需数量的身份验证方法，以防他们无法使用其中的某个方法。

|身份验证方法|使用情况|
| --- | --- |
| 密码 | MFA 和 SSPR |
| 安全提问 | 仅限 SSPR |
| 电子邮件地址 | 仅限 SSPR |
| Microsoft Authenticator 应用 | MFA 和 SSPR 公共预览版 |
| SMS | MFA 和 SSPR |
| 语音呼叫 | MFA 和 SSPR |
| 应用密码 | 仅限在某些情况下执行 MFA |

![登录屏幕上使用的身份验证方法](media/concept-authentication-methods/overview-login.png)

|     |
| --- |
| 将移动应用通知和移动应用代码用作 Azure AD 自助密码重置方法是 Azure Active Directory 的公共预览版功能。 有关预览版的详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。|
|     |

## <a name="password"></a>密码

Azure AD 密码被视为身份验证方法。 它是唯一**不可禁用**的方法。

## <a name="security-questions"></a>安全提问

**只有 Azure AD 自助密码重置**才向非管理员帐户提供安全提问。

如果使用安全提问，我们建议结合另一种方法使用这些提问。 因为某些人可能知道其他用户的问题的答案，因此这种方法可能比其他方法更不安全。

> [!NOTE]
> 安全问题以专用、安全的方式存储在目录的用户对象上，并且只能由用户在注册期间回答。 管理员无法读取或修改用户的问题或答案。
>

### <a name="predefined-questions"></a>预定义问题

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
* 最年长的兄弟姐妹的生日年份和月份是什么？ （例如 1985 年 11 月）
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

所有预定义的安全提问都已根据用户的浏览器区域设置翻译并本地化为 Office 365 的完整语言集。

### <a name="custom-security-questions"></a>自定义安全问题

自定义安全提问尚未本地化。 所有自定义问题的显示语言是在管理用户界面中输入这些问题时所用的语言，即使用户浏览器的区域设置与此不同。 如果需要本地化的问题，应使用预定义的问题。

自定义安全问题的最大长度为 200 个字符。

### <a name="security-question-requirements"></a>安全问题要求

* 答案包含的字符数下限为 3 个字符。
* 答案包含的字符数上限为 40 个字符。
* 用户不能多次回答同一问题。
* 用户不能为多个问题提供相同的答案。
* 可以使用任何字符集来定义问题和答案（包括 Unicode 字符）。
* 所定义问题的数量必须大于或等于注册所需问题数量。

## <a name="email-address"></a>电子邮件地址

电子邮件地址**只能在 Azure AD 自助密码重置中**使用。

Microsoft 建议使用无需提供用户 Azure AD 密码即可访问的电子邮件帐户。

## <a name="microsoft-authenticator-app"></a>Microsoft Authenticator 应用

Microsoft Authenticator 应用为 Azure AD 工作或学校帐户或 Microsoft 帐户提供附加的安全级别。

Microsoft Authenticator 应用适用于 [Android](https://go.microsoft.com/fwlink/?linkid=866594)、[iOS](https://go.microsoft.com/fwlink/?linkid=866594) 和 [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071)。

> [!NOTE]
> 用户在注册自助密码重置时，无法选择注册其移动应用。 用户可以在 [https://aka.ms/mfasetup](https://aka.ms/mfasetup) 中，或者在安全信息注册预览版 ([https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo)) 中注册其移动应用。
>

### <a name="notification-through-mobile-app"></a>通过移动应用发送通知

Microsoft Authenticator 应用通过将通知推送到智能手机或平板电脑，可帮助防止对帐户进行未经授权的访问，以及停止欺诈性交易。 用户将查看通知，如果信息合法，则选择“验证”。 否则，可以选择“拒绝”。

> [!WARNING]
> 对于自助密码重置，如果只需使用一种方法来执行重置，则验证码是可供用户用于确保最高级别安全性的唯一选项。
>
> 如果需要两种方法，则用户可以使用通知或验证码进行重置，此外还能使用其他任何已启用的方法。
>

### <a name="verification-code-from-mobile-app"></a>通过移动应用发送验证码

Microsoft Authenticator 应用或其他第三方应用可用作生成 OATH 验证码所需的软件令牌。 输入用户名和密码后，在登录屏幕中输入该应用提供的代码。 验证码提供了第二种形式的身份验证。

> [!WARNING]
> 对于自助密码重置，如果只需使用一种方法来执行重置，则验证码是可供用户确保最高级别的安全性的唯一选项。
>

## <a name="mobile-phone"></a>移动电话

在手机中为用户提供两个选项。

如果用户不希望在目录中公开其移动电话号码，但仍想使用该号码来重置密码，则管理员不应在目录中填充该号码。 用户应通过[密码重置注册门户](https://aka.ms/ssprsetup)填充其“身份验证电话”属性。 管理员可在用户的配置文件中看到此信息，但此信息不会发布到其他位置。

若要正常工作，电话号码必须采用“+国家/地区代码 电话号码”格式，例如，+1 4255551234。

> [!NOTE]
> 在国家/地区代码和电话号码之间需要有一个空格。
>
> 密码重置不支持电话分机。 即使采用“+1 4255551234X12345”格式，在拨出电话前也会删除分机。

### <a name="text-message"></a>短信

将一条包含验证码的短信发送到手机号码。 在登录界面中输入提供的验证码可继续操作。

### <a name="phone-call"></a>电话呼叫

向提供的电话号码发起自动语音呼叫。 接听电话，并按电话键盘上的 # 进行身份验证

## <a name="office-phone"></a>办公电话

向提供的电话号码发起自动语音呼叫。 接听电话，并按电话键盘上的 # 进行身份验证。

若要正常工作，电话号码必须采用“+国家/地区代码 电话号码”格式，例如，+1 4255551234。

办公电话属性由管理员管理。

> [!NOTE]
> 在国家/地区代码和电话号码之间需要有一个空格。
>
> 密码重置不支持电话分机。 即使采用“+1 4255551234X12345”格式，在拨出电话前也会删除分机。

## <a name="app-passwords"></a>应用密码

某些非浏览器应用不支持多重身份验证，如果为某个用户启用了多重身份验证，当此用户尝试使用非浏览器应用时，将无法进行身份验证。 应用密码允许用户继续进行身份验证

如果通过条件访问策略而不是通过每用户 MFA 强制实施多重身份验证，则无法创建应用密码。 使用条件访问策略控制访问权限的应用程序不需要应用密码。

如果组织通过 Azure AD 进行 SSO 联合验证，并且打算使用 Azure MFA，请注意以下详细信息：

* 应用密码由 Azure AD 进行验证，因此绕过了联合。 仅在设置应用密码时，才会使用联合。 对于联合 (SSO) 用户，密码存储在组织 ID 中。 如果用户离开公司，相应信息必须通过 DirSync 流向组织 ID。 帐户禁用/删除可能需要长达三个小时才能同步，从而延迟了 Azure AD 中应用密码的禁用/删除。
* 应用密码不遵循“本地客户端访问控制”设置。
* 没有为应用密码提供本地身份验证日志记录/审核功能。
* 在对客户端使用双重验证时，某些先进的体系结构设计可能需要将组织用户名和密码与应用密码结合使用，具体取决于进行身份验证的位置。 对于针对本地基础结构进行身份验证的客户端，会使用组织用户名和密码。 对于针对 Azure AD 进行身份验证的客户端，会使用应用密码。
* 默认情况下，用户无法创建应用密码。 如果需要允许用户创建应用密码，请在服务设置下选择“允许用户创建应用密码来登录非浏览器应用程序”选项。

## <a name="next-steps"></a>后续步骤

[为组织启用自助密码重置](quickstart-sspr.md)

[为组织启用 Azure 多重身份验证](howto-mfa-getstarted.md)

[针对 Azure 多重身份验证和 Azure AD 自助密码重置启用聚合注册](concept-registration-mfa-sspr-converged.md)

[最终用户身份验证方法配置文档](https://aka.ms/securityinfoguide)
