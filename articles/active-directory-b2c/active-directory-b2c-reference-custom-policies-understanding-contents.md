---
title: Azure Active Directory B2C：了解初学者包的自定义策略 | Microsoft Docs
description: 有关 Azure Active Directory B2C 自定义策略的主题
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 04/25/2017
ms.author: davidmu
ms.openlocfilehash: 12f63bc42f8450f086ed9f0e8d598c9c91a0c3d4
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/05/2018
---
# <a name="understanding-the-custom-policies-of-the-azure-ad-b2c-custom-policy-starter-pack"></a>了解 Azure AD B2C 自定义策略初学者包的自定义策略

本部分列出**初学者包**随附的，让你通过继承 *B2C_1A_base_extensions 策略*创建自己策略的 B2C_1A_base 策略的所有核心元素。

因此，本部分专门侧重于已定义的声明类型、声明转换、内容定义、声明提供程序及其技术配置文件和核心用户旅程。

> [!IMPORTANT]
> 对于下面提供的信息，Microsoft 不做任何明示或暗示的保证。 在正式版发布之前、发布期间或之后，随时可能会引入更改。

自己的策略和 B2C_1A_base_extensions 策略可以重写这些定义，并可根据需要通过提供其他定义来扩展此父策略。

*B2C_1A_base 策略*的核心元素是声明类型、声明转换和内容定义。 在自己的策略以及 *B2C_1A_base_extensions 策略*中，可以方便地引用这些元素。

## <a name="claims-schemas"></a>声明架构

此声明架构划分为三个部分：

1.  第一个部分列出为了让用户旅程正常工作而最起码需要提供的声明。
2.  第二个部分列出要传递给其他声明提供程序的查询字符串参数和其他特殊参数所需的声明，尤其是用于身份验证的 login.microsoftonline.com。 **请不要修改这些声明**。
3.  最后，第三个部分列出可从用户收集的、在目录中存储的、登录期间在令牌中发送的任何其他可选声明。 可在此部分添加要从用户收集的和/或在令牌中发送的新声明类型。

> [!IMPORTANT]
> 声明架构包含针对某些声明（例如密码和用户名）的限制。 信任框架 (TF) 策略像处理其他任何声明提供程序一样处理 Azure AD，其所有限制在自定义策略中建模。 可以通过修改策略来添加更多限制，或者对具有自身限制的凭据存储使用另一个声明提供程序。

下面列出了可用的声明类型。

### <a name="claims-that-are-required-for-the-user-journeys"></a>用户旅程所需的声明

用户旅程需有以下声明才能正常工作：

| 声明类型 | 说明 |
|-------------|-------------|
| *UserId* | 用户名 |
| *signInName* | 登录名 |
| *tenantId* | 用户对象在 Azure AD B2C 中的租户标识符 (ID) |
| *objectId* | 用户对象在 Azure AD B2C 中的对象标识符 (ID) |
| *password* | 密码 |
| *newPassword* | |
| *reenterPassword* | |
| *passwordPolicies* | Azure AD B2C 用来确定密码强度、到期时间等的密码策略。 |
| *sub* | |
| *alternativeSecurityId* | |
| *identityProvider* | |
| *displayName* | |
| *strongAuthenticationPhoneNumber* | 用户的电话号码 |
| *Verified.strongAuthenticationPhoneNumber* | |
| *email* | 用于联系用户的电子邮件地址 |
| *signInNamesInfo.emailAddress* | 用户登录时可以使用的电子邮件地址 |
| *otherMails* | 可用于联系用户的电子邮件地址 |
| *userPrincipalName* | Azure AD B2C 中存储的用户名 |
| *upnUserName* | 用于创建用户主体名称的用户名 |
| *mailNickName* | Azure AD B2C 中存储的用户邮件昵称 |
| *newUser* | |
| *executed-SelfAsserted-Input* | 用于指定是否从用户收集了属性的声明 |
| *executed-PhoneFactor-Input* | 用于指定是否从用户收集了新电话号码的声明 |
| *authenticationSource* | 指定是通过社交标识提供者、login.microsoftonline.com 还是本地帐户验证了用户身份 |

### <a name="claims-required-for-query-string-parameters-and-other-special-parameters"></a>查询字符串参数和其他特殊参数所需的声明

需要使用以下声明将特殊参数（包括某些查询字符串参数）传递给其他声明提供程序：

| 声明类型 | 说明 |
|-------------|-------------|
| *nux* | 为了在 login.microsoftonline.com 中进行本地帐户身份验证而传递的特殊参数 |
| *nca* | 为了在 login.microsoftonline.com 中进行本地帐户身份验证而传递的特殊参数 |
| *prompt* | 为了在 login.microsoftonline.com 中进行本地帐户身份验证而传递的特殊参数 |
| *mkt* | 为了在 login.microsoftonline.com 中进行本地帐户身份验证而传递的特殊参数 |
| *lc* | 为了在 login.microsoftonline.com 中进行本地帐户身份验证而传递的特殊参数 |
| *grant_type* | 为了在 login.microsoftonline.com 中进行本地帐户身份验证而传递的特殊参数 |
| *scope* | 为了在 login.microsoftonline.com 中进行本地帐户身份验证而传递的特殊参数 |
| *client_id* | 为了在 login.microsoftonline.com 中进行本地帐户身份验证而传递的特殊参数 |
| *objectIdFromSession* | 默认会话管理提供程序提供的、用于指示已从 SSO 会话检索对象 ID 的参数 |
| *isActiveMFASession* | MFA 会话管理提供的、用于指示用户具有活动 MFA 会话的参数 |

### <a name="additional-optional-claims-that-can-be-collected"></a>可收集的其他（可选）声明

以下声明是可从用户收集的、在目录中存储的、在令牌中发送的其他声明。 如前所述，可将其他声明添加到此列表中。

| 声明类型 | 说明 |
|-------------|-------------|
| *givenName* | 用户的名（也称为名字） |
| *surname* | 用户的姓（也称为家族名或姓氏） |
| *Extension_picture* | 用户的社交图片 |

## <a name="claim-transformations"></a>声明转换

下面列出了可用的声明转换。

| 声明转换 | 说明 |
|----------------------|-------------|
| *CreateOtherMailsFromEmail* | |
| *CreateRandomUPNUserName* | |
| *CreateUserPrincipalName* | |
| *CreateSubjectClaimFromObjectID* | |
| *CreateSubjectClaimFromAlternativeSecurityId* | |
| *CreateAlternativeSecurityId* | |

## <a name="content-definitions"></a>内容定义

本部分介绍已在 *B2C_1A_base* 策略中声明的内容定义。 可以根据需要在自己的策略以及 *B2C_1A_base_extensions* 策略中方便地引用、重写和/或扩展这些内容定义。

| 声明提供程序 | 说明 |
|-----------------|-------------|
| *Facebook* | |
| *本地帐户登录* | |
| *PhoneFactor* | |
| *Azure Active Directory* | |
| *自我断言* | |
| *本地帐户* | |
| *会话管理* | |
| *信任框架策略引擎* | |
| *技术配置文件* | |
| *令牌颁发者* | |

## <a name="technical-profiles"></a>技术配置文件

本部分描述已在 *B2C_1A_base* 策略中为每个声明提供程序声明的技术配置文件。 可以根据需要在自己的策略以及 *B2C_1A_base_extensions* 策略中方便地进一步引用、重写和/或扩展这些技术配置文件。

### <a name="technical-profiles-for-facebook"></a>Facebook 的技术配置文件

| 技术配置文件 | 说明 |
|-------------------|-------------|
| *Facebook-OAUTH* | |

### <a name="technical-profiles-for-local-account-signin"></a>用于本地帐户登录的技术配置文件

| 技术配置文件 | 说明 |
|-------------------|-------------|
| *Login-NonInteractive* | |

### <a name="technical-profiles-for-phone-factor"></a>Phone Factor 的技术配置文件

| 技术配置文件 | 说明 |
|-------------------|-------------|
| *PhoneFactor-Input* | |
| *PhoneFactor-InputOrVerify* | |
| *PhoneFactor-Verify* | |

### <a name="technical-profiles-for-azure-active-directory"></a>Azure Active Directory 的技术配置文件

| 技术配置文件 | 说明 |
|-------------------|-------------|
| *AAD-Common* | 其他 AAD-xxx 技术配置文件包含的技术配置文件 |
| *AAD-UserWriteUsingAlternativeSecurityId* | 用于社交登录的技术配置文件 |
| *AAD-UserReadUsingAlternativeSecurityId* | 用于社交登录的技术配置文件 |
| *AAD-UserReadUsingAlternativeSecurityId-NoError* | 用于社交登录的技术配置文件 |
| *AAD-UserWritePasswordUsingLogonEmail* | 本地帐户的技术配置文件 |
| *AAD-UserReadUsingEmailAddress* | 本地帐户的技术配置文件 |
| *AAD-UserWriteProfileUsingObjectId* | 用于通过 objectId 更新用户记录的技术配置文件 |
| *AAD-UserWritePhoneNumberUsingObjectId* | 用于通过 objectId 更新用户记录的技术配置文件 |
| *AAD-UserWritePasswordUsingObjectId* | 用于通过 objectId 更新用户记录的技术配置文件 |
| *AAD-UserReadUsingObjectId* | 在用户身份验证后用于读取数据的技术配置文件 |

### <a name="technical-profiles-for-self-asserted"></a>自我断言的技术配置文件

| 技术配置文件 | 说明 |
|-------------------|-------------|
| *SelfAsserted-Social* | |
| *SelfAsserted-ProfileUpdate* | |

### <a name="technical-profiles-for-local-account"></a>本地帐户的技术配置文件

| 技术配置文件 | 说明 |
|-------------------|-------------|
| *LocalAccountSignUpWithLogonEmail* | |

### <a name="technical-profiles-for-session-management"></a>用于会话管理的技术配置文件

| 技术配置文件 | 说明 |
|-------------------|-------------|
| *SM-Noop* | |
| *SM-AAD* | |
| *SM-SocialSignup* | 用于消除注册与登录之间 AAD 会话歧义的配置文件名称 |
| *SM-SocialLogin* | |
| *SM-MFA* | |

### <a name="technical-profiles-for-trustframework-policy-engine-technicalprofiles"></a>信任框架策略引擎技术配置文件的技术配置文件

目前，没有为**信任框架策略引擎技术配置文件**声明提供程序定义技术配置文件。

### <a name="technical-profiles-for-token-issuer"></a>令牌颁发者的技术配置文件

| 技术配置文件 | 说明 |
|-------------------|-------------|
| *JwtIssuer* | |

## <a name="user-journeys"></a>用户旅程

本部分描述已在 *B2C_1A_base* 策略中声明的用户旅程。 可以根据需要在自己的策略以及 *B2C_1A_base_extensions* 策略中方便地进一步引用、重写和/或扩展这些用户旅程。

| 用户旅程 | 说明 |
|--------------|-------------|
| *SignUp* | |
| *SignIn* | |
| *SignUpOrSignIn* | |
| *EditProfile* | |
| *PasswordReset* | |
