---
title: 本地化字符串 ID - Azure Active Directory B2C | Microsoft Docs
description: 在 Azure Active Directory B2C 中使用自定义策略的 api.signuporsignin ID 指定内容定义的 ID。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/15/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: f75fbf286741fcc122332574332a30ad7fa23644
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2020
ms.locfileid: "92096192"
---
# <a name="localization-string-ids"></a>本地化字符串 ID

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Localization 元素使你能够在用户旅程的策略中支持多个区域设置或语言。 本文提供了可在策略中使用的本地化 ID 列表。 若要熟悉 UI 本地化，请参阅[本地化](localization.md)。

## <a name="sign-up-or-sign-in-page-elements"></a>注册或登录页面元素

以下 Id 用于 ID 为的内容定义 `api.signuporsignin` 和 [自断言技术配置文件](self-asserted-technical-profile.md)。

| ID | 默认值 |
| -- | ------------- |
| **local_intro_email** | 使用现有帐户登录 |
| **logonIdentifier_email** | 电子邮件地址 |
| **requiredField_email** | 请输入电子邮件地址 |
| **invalid_email** | 请输入有效的电子邮件地址 |
| **email_pattern** | ^[a-zA-Z0-9.!#$%&'' *+/=?^_\`{\|}~-]+@[a-zA-Z0-9-]+(?:\\.[a-zA-Z0-9-]+)* $ |
| **local_intro_username** | 使用用户名登录 |
| **logonIdentifier_username** | 用户名 |
| **requiredField_username** | 请输入用户名 |
| **password** | 密码 |
| **requiredField_password** | 请输入密码 |
| **invalid_password** | 你输入的密码格式不正确。 |
| **forgotpassword_link** | 忘记密码了? |
| **createaccount_intro** | 没有帐户？ |
| **createaccount_link** | 立即注册 |
| **divider_title** | OR |
| **cancel_message** | 用户忘记了密码 |
| **button_signin** | 登录 |
| **social_intro** | 使用社交帐户登录 |
  **remember_me** |使我保持登录。 |
| **unknown_error** | 登录时遇到问题。 请稍后重试。 |

下面的示例演示如何在注册或登录页中使用某些用户界面元素：

![注册或登录页面 UX 元素](./media/localization-string-ids/localization-susi.png)


### <a name="sign-up-or-sign-in-identity-providers"></a>注册或登录标识提供者

标识提供者的 ID 在用户旅程 ClaimsExchange 元素中配置。 若要本地化标识提供者的标题，请将 ElementType 设置为 `ClaimsProvider`，同时将 StringId 设置为 `ClaimsExchange` 的 ID。

```xml
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="MicrosoftExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccount" />
  </ClaimsExchanges>
</OrchestrationStep>
```

下面的示例将 Facebook 标识提供者本地化为阿拉伯语：

```xml
<LocalizedString ElementType="ClaimsProvider" StringId="FacebookExchange">فيس بوك</LocalizedString>
```

### <a name="sign-up-or-sign-in-error-messages"></a>注册或登录错误消息

| ID | 默认值 |
| -- | ------------- |
| **UserMessageIfInvalidPassword** | 密码不正确。 |
| **UserMessageIfClaimsPrincipalDoesNotExist** | 似乎找不到你的帐户。 |
| **UserMessageIfOldPasswordUsed** | 你可能使用的是旧密码。 |
| **DefaultMessage** | 无效的用户名或密码。 |
| **UserMessageIfUserAccountDisabled** | 帐户已锁定。 请联系支持人员解除锁定，然后重试。 |
| **UserMessageIfUserAccountLocked** | 帐户暂时锁定以防止未经授权的使用。 请稍后再试。 |
| **AADRequestsThrottled** | 目前请求过多。 请等待一段时间，然后重试。 |

### <a name="sign-up-or-sign-in-example"></a>注册或登录示例

```xml
<LocalizedResources Id="api.signuporsignin.en">
  <LocalizedStrings>
    <LocalizedString ElementType="UxElement" StringId="logonIdentifier_email">Email Address</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="requiredField_email">Please enter your email</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="logonIdentifier_username">Username</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="password">Password</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="createaccount_link">Sign up now</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="requiredField_username">Please enter your user name</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="createaccount_intro">Don't have an account?</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="forgotpassword_link">Forgot your password?</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="divider_title">OR</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="cancel_message">The user has forgotten their password</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_signin">Sign in</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="social_intro">Sign in with your social account</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="requiredField_password">Please enter your password</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="invalid_password">The password you entered is not in the expected format.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="local_intro_username">Sign in with your user name</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="local_intro_email">Sign in with your existing account</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="invalid_email">Please enter a valid email address</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="unknown_error">We are having trouble signing you in. Please try again later.</LocalizedString>
    <!-- Uncomment the remember_me only if the keep me signed in is activated. 
    <LocalizedString ElementType="UxElement" StringId="remember_me">Keep me signed in</LocalizedString> -->
    <LocalizedString ElementType="UxElement" StringId="email_pattern">^[a-zA-Z0-9.!#$%&amp;’'+/=?^_`{|}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)$</LocalizedString>
    <LocalizedString ElementType="ClaimsProvider" StringId="FacebookExchange">Facebook</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidPassword">Your password is incorrect.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalDoesNotExist">We can't seem to find your account.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfOldPasswordUsed">Looks like you used an old password.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="DefaultMessage">Invalid username or password.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountDisabled">Your account has been locked. Contact your support person to unlock it, then try again.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountLocked">Your account is temporarily locked to prevent unauthorized use. Try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="AADRequestsThrottled">There are too many requests at this moment. Please wait for some time and try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="sign-up-and-self-asserted-pages-user-interface-elements"></a>注册和自断言页用户界面元素

下面是 ID 为的内容定义 `api.localaccountsignup` 或以开头的任何内容定义的 id `api.selfasserted` ，如 `api.selfasserted.profileupdate` 和 `api.localaccountpasswordreset` ，以及 [自断言技术配置文件](self-asserted-technical-profile.md)。

| ID | 默认值 |
| -- | ------------- |
| **ver_sent** | 验证码已发送到: |
| **ver_but_default** | 默认 |
| **cancel_message** | 用户已取消输入自断言信息 |
| **preloader_alt** | 请稍候 |
| **ver_but_send** | 发送验证代码 |
| **alert_yes** | 是 |
| **error_fieldIncorrect** | 一个或多个字段填写不正确。 请检查条目并重试。 |
| **year** | 年龄 |
| **verifying_blurb** | 正在处理你的信息，请稍候。 |
| **button_cancel** | 取消 |
| **ver_fail_no_retry** | 你进行不正确尝试的次数过多。 请稍后重试。 |
| **month** | 月份 |
| **ver_success_msg** | 已验证电子邮件地址。 现在可以继续。 |
| **months** | 一月、二月、三月、四月、五月、六月、七月、八月、九月、十月、十一月、十二月 |
| **ver_fail_server** | 我们无法验证你的电子邮件地址。 请输入有效的电子邮件地址，然后重试。 |
| **error_requiredFieldMissing** | 缺少必填字段。 请填写所有必填字段，然后重试。 |
| **initial_intro** | 请提供以下详细信息。 |
| **ver_but_resend** | 发送新验证码 |
| **button_continue** | 创建 |
| **error_passwordEntryMismatch** | 密码输入字段不一致。 请在这两个字段中输入相同密码，然后重试。 |
| **ver_incorrect_format** | 格式错误。 |
| **ver_but_edit** | 更改电子邮件 |
| **ver_but_verify** | 验证验证码 |
| **alert_no** | 否 |
| **ver_info_msg** | 验证码已发送到收件箱。 请将其复制到下面的输入框。 |
| day | 日期 |
| **ver_fail_throttled** | 用于验证此电子邮件地址的请求过多。 请稍等片刻，然后重试。 |
| **helplink_text** | 这是什么？ |
| **ver_fail_retry** | 验证码不正确。 请重试。 |
| **alert_title** | 取消输入详细信息 |
| **required_field** | 此信息是必需的。 |
| **alert_message** | 确实要取消输入详细信息吗? |
| **ver_intro_msg** | 验证是必需的。 请单击“发送”按钮。 |
| **ver_input** | 验证码 |

### <a name="sign-up-and-self-asserted-pages-error-messages"></a>注册和自断言页错误消息

| ID | 默认值 |
| -- | ------------- |
| **UserMessageIfClaimsPrincipalAlreadyExists** | 已存在具有指定 ID 的用户。 请选择其他用户。 |
| **UserMessageIfClaimNotVerified** | 未验证声明: {0} |
| **UserMessageIfIncorrectPattern** | 模式错误: {0} |
| **UserMessageIfMissingRequiredElement** | 缺少必需的元素: {0} |
| **UserMessageIfValidationError** | 验证错误: {0} |
| **UserMessageIfInvalidInput** | {0} 具有无效输入。 |
| **ServiceThrottled** | 目前请求过多。 请等待一段时间，然后重试。 |

下面的示例演示如何在注册页中使用某些用户界面元素：

![标记了其 UI 元素名称的注册页](./media/localization-string-ids/localization-sign-up.png)

下面的示例演示在用户单击“发送验证码”按钮后如何在注册页中使用某些用户界面元素：

![注册页电子邮件验证 UX 元素](./media/localization-string-ids/localization-email-verification.png)

## <a name="sign-up-and-self-asserted-pages-example"></a>注册和自断言页面示例

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email Address</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Email address that can be used to contact you.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="DisplayName">New Password</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="UserHelpText">Enter new password</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="PatternHelpText">8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="DisplayName">Confirm New Password</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="UserHelpText">Confirm new password</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="PatternHelpText">#8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="DisplayName">Display Name</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="UserHelpText">Your display name.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="DisplayName">Surname</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="UserHelpText">Your surname (also known as family name or last name).</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="DisplayName">Given Name</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="UserHelpText">Your given name (also known as first name).</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_continue">Create</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_fieldIncorrect">One or more fields are filled out incorrectly. Please check your entries and try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_passwordEntryMismatch">The password entry fields do not match. Please enter the same password in both fields and try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_requiredFieldMissing">A required field is missing. Please fill out all required fields and try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="helplink_text">What is this?</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="initial_intro">Please provide the following details.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="preloader_alt">Please wait</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="required_field">This information is required.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_but_edit">Change e-mail</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_but_resend">Send new code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_but_send">Send verification code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_but_verify">Verify code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_fail_code_expired">That code is expired. Please request a new code.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_fail_no_retry">You've made too many incorrect attempts. Please try again later.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_fail_retry">That code is incorrect. Please try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_fail_server">We are having trouble verifying your email address. Please enter a valid email address and try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_fail_throttled">There have been too many requests to verify this email address. Please wait a while, then try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_info_msg">Verification code has been sent to your inbox. Please copy it to the input box below.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_input">Verification code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_intro_msg">Verification is necessary. Please click Send button.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_success_msg">E-mail address verified. You can now continue.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="ServiceThrottled">There are too many requests at this moment. Please wait for some time and try again.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimNotVerified">Claim not verified: {0}</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">A user with the specified ID already exists. Please choose a different one.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfIncorrectPattern">Incorrect pattern for: {0}</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidInput">{0} has invalid input.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMissingRequiredElement">Missing required element: {0}</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfValidationError">Error in validation by: {0}</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="phone-factor-authentication-page-user-interface-elements"></a>电话身份验证页用户界面元素

下面是 ID 为的内容定义 `api.phonefactor` 和 [电话系数技术配置文件](phone-factor-technical-profile.md)的 id。

| ID | 默认值 |
| -- | ------------- |
| **button_verify** | 致电我 |
| **country_code_label** | 国家/地区代码 |
| **cancel_message** | 用户已取消多重身份验证 |
| **text_button_send_second_code** | 发送新验证码 |
| **code_pattern** | \\d{6} |
| **intro_mixed** | 我们记录了以下号码。 我们能够发送短信验证码或致电来验证你的身份。 |
| **intro_mixed_p** | 我们记录了以下号码。 选择一个号码，以便我们能够通过电话或发送短信验证码来验证你的身份。 |
| **button_verify_code** | 验证验证码 |
| **requiredField_code** | 请输入收到的验证码 |
| **invalid_code** | 请输入收到的 6 位验证码 |
| **button_cancel** | 取消 |
| **local_number_input_placeholder_text** | 电话号码 |
| **button_retry** | 重试 |
| **alternative_text** | 我没有电话 |
| **intro_phone_p** | 我们记录了以下号码。 选择一个号码，以便我们能够通过电话来验证你的身份。 |
| **intro_phone** | 我们记录了以下号码。 我们将通过电话来验证你的身份。 |
| **enter_code_text_intro** | 请在下面输入验证码，或  |
| **intro_entry_phone** | 请在下面输入一个号码，以便我们能够通过电话来验证你的身份。 |
| **intro_entry_sms** | 请在下面输入一个号码，以便我们能够发送短信验证码来验证你的身份。 |
| **button_send_code** | 发送验证码 |
| **invalid_number** | 请输入有效电话号码 |
| **intro_sms** | 我们记录了以下号码。 我们将发送短信验证码来验证你的身份。 |
| **intro_entry_mixed** | 请在下面输入一个号码，以便我们能够发送短信验证码或通过电话来验证你的身份。 |
| **number_pattern** | ^\\+(?:[0-9][\\x20-]?){6,14}[0-9]$ |
| **intro_sms_p** |我们记录了以下号码。 选择一个号码，以便我们能够发送短信验证码来验证你的身份。 |
| **requiredField_countryCode** | 请选择国家/地区代码 |
| **requiredField_number** | 请输入电话号码 |
| **country_code_input_placeholder_text** |国家或地区 |
| **number_label** | 电话号码 |
| **error_tryagain** | 你提供的电话号码占线或不可用。 请检查号码并重试。 |
| **error_incorrect_code** | 你输入的验证码与我们的记录不一致。 请重试，或请求获取新验证码。 |
| **countryList** | 请参阅[国家/地区列表](#phone-factor-authentication-page-example)。 |
| **error_448** | 你提供的电话号码打不通。 |
| **error_449** | 用户已超出重试次数上限。 |
| **verification_code_input_placeholder_text** | 验证码 |

下面的示例演示如何在 MFA 注册页中使用某些用户界面元素：

![电话系数身份验证注册 UX 元素](./media/localization-string-ids/localization-mfa1.png)

下面的示例演示如何在 MFA 验证页中使用某些用户界面元素：

![手机因素身份验证验证 UX 元素](./media/localization-string-ids/localization-mfa2.png)

## <a name="phone-factor-authentication-page-example"></a>电话系数身份验证页面示例

```xml
<LocalizedResources Id="api.phonefactor.en">
  <LocalizedStrings>
    <LocalizedString ElementType="UxElement" StringId="button_verify">Call Me</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="country_code_label">Country Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="cancel_message">The user has canceled multi-factor authentication</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="text_button_send_second_code">Send a new code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="code_pattern">\d{6}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_mixed">We have the following number on record for you. We can send a code via SMS or phone to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_mixed_p">We have the following numbers on record for you. Choose a number that we can phone or send a code via SMS to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_verify_code">Verify Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="requiredField_code">Please enter the verification code you received</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="invalid_code">Please enter the 6-digit code you received</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_cancel">Cancel</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="local_number_input_placeholder_text">Phone number</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_retry">Retry</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="alternative_text">I don't have my phone</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_phone_p">We have the following numbers on record for you. Choose a number that we can phone to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_phone">We have the following number on record for you. We will phone to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="enter_code_text_intro">Enter your verification code below, or</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_entry_phone">Enter a number below that we can phone to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_entry_sms">Enter a number below that we can send a code via SMS to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_send_code">Send Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="invalid_number">Please enter a valid phone number</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_sms">We have the following number on record for you. We will send a code via SMS to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_entry_mixed">Enter a number below that we can send a code via SMS or phone to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="number_pattern">^\+(?:[0-9][\x20-]?){6,14}[0-9]$</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_sms_p">We have the following numbers on record for you. Choose a number that we can send a code via SMS to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="requiredField_countryCode">Please select your country code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="requiredField_number">Please enter your phone number</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="country_code_input_placeholder_text">Country or region</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="number_label">Phone Number</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_tryagain">The phone number you provided is busy or unavailable. Please check the number and try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_incorrect_code">The verification code you have entered does not match our records. Please try again, or request a new code.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="countryList">{"DEFAULT":"Country/Region","AF":"Afghanistan","AX":"Åland Islands","AL":"Albania","DZ":"Algeria","AS":"American Samoa","AD":"Andorra","AO":"Angola","AI":"Anguilla","AQ":"Antarctica","AG":"Antigua and Barbuda","AR":"Argentina","AM":"Armenia","AW":"Aruba","AU":"Australia","AT":"Austria","AZ":"Azerbaijan","BS":"Bahamas","BH":"Bahrain","BD":"Bangladesh","BB":"Barbados","BY":"Belarus","BE":"Belgium","BZ":"Belize","BJ":"Benin","BM":"Bermuda","BT":"Bhutan","BO":"Bolivia","BQ":"Bonaire","BA":"Bosnia and Herzegovina","BW":"Botswana","BV":"Bouvet Island","BR":"Brazil","IO":"British Indian Ocean Territory","VG":"British Virgin Islands","BN":"Brunei","BG":"Bulgaria","BF":"Burkina Faso","BI":"Burundi","CV":"Cabo Verde","KH":"Cambodia","CM":"Cameroon","CA":"Canada","KY":"Cayman Islands","CF":"Central African Republic","TD":"Chad","CL":"Chile","CN":"China","CX":"Christmas Island","CC":"Cocos (Keeling) Islands","CO":"Colombia","KM":"Comoros","CG":"Congo","CD":"Congo (DRC)","CK":"Cook Islands","CR":"Costa Rica","CI":"Côte d'Ivoire","HR":"Croatia","CU":"Cuba","CW":"Curaçao","CY":"Cyprus","CZ":"Czech Republic","DK":"Denmark","DJ":"Djibouti","DM":"Dominica","DO":"Dominican Republic","EC":"Ecuador","EG":"Egypt","SV":"El Salvador","GQ":"Equatorial Guinea","ER":"Eritrea","EE":"Estonia","ET":"Ethiopia","FK":"Falkland Islands","FO":"Faroe Islands","FJ":"Fiji","FI":"Finland","FR":"France","GF":"French Guiana","PF":"French Polynesia","TF":"French Southern Territories","GA":"Gabon","GM":"Gambia","GE":"Georgia","DE":"Germany","GH":"Ghana","GI":"Gibraltar","GR":"Greece","GL":"Greenland","GD":"Grenada","GP":"Guadeloupe","GU":"Guam","GT":"Guatemala","GG":"Guernsey","GN":"Guinea","GW":"Guinea-Bissau","GY":"Guyana","HT":"Haiti","HM":"Heard Island and McDonald Islands","HN":"Honduras","HK":"Hong Kong SAR","HU":"Hungary","IS":"Iceland","IN":"India","ID":"Indonesia","IR":"Iran","IQ":"Iraq","IE":"Ireland","IM":"Isle of Man","IL":"Israel","IT":"Italy","JM":"Jamaica","JP":"Japan","JE":"Jersey","JO":"Jordan","KZ":"Kazakhstan","KE":"Kenya","KI":"Kiribati","KR":"Korea","KW":"Kuwait","KG":"Kyrgyzstan","LA":"Laos","LV":"Latvia","LB":"Lebanon","LS":"Lesotho","LR":"Liberia","LY":"Libya","LI":"Liechtenstein","LT":"Lithuania","LU":"Luxembourg","MO":"Macao SAR","MK":"North Macedonia","MG":"Madagascar","MW":"Malawi","MY":"Malaysia","MV":"Maldives","ML":"Mali","MT":"Malta","MH":"Marshall Islands","MQ":"Martinique","MR":"Mauritania","MU":"Mauritius","YT":"Mayotte","MX":"Mexico","FM":"Micronesia","MD":"Moldova","MC":"Monaco","MN":"Mongolia","ME":"Montenegro","MS":"Montserrat","MA":"Morocco","MZ":"Mozambique","MM":"Myanmar","NA":"Namibia","NR":"Nauru","NP":"Nepal","NL":"Netherlands","NC":"New Caledonia","NZ":"New Zealand","NI":"Nicaragua","NE":"Niger","NG":"Nigeria","NU":"Niue","NF":"Norfolk Island","KP":"North Korea","MP":"Northern Mariana Islands","NO":"Norway","OM":"Oman","PK":"Pakistan","PW":"Palau","PS":"Palestinian Authority","PA":"Panama","PG":"Papua New Guinea","PY":"Paraguay","PE":"Peru","PH":"Philippines","PN":"Pitcairn Islands","PL":"Poland","PT":"Portugal","PR":"Puerto Rico","QA":"Qatar","RE":"Réunion","RO":"Romania","RU":"Russia","RW":"Rwanda","BL":"Saint Barthélemy","KN":"Saint Kitts and Nevis","LC":"Saint Lucia","MF":"Saint Martin","PM":"Saint Pierre and Miquelon","VC":"Saint Vincent and the Grenadines","WS":"Samoa","SM":"San Marino","ST":"São Tomé and Príncipe","SA":"Saudi Arabia","SN":"Senegal","RS":"Serbia","SC":"Seychelles","SL":"Sierra Leone","SG":"Singapore","SX":"Sint Maarten","SK":"Slovakia","SI":"Slovenia","SB":"Solomon Islands","SO":"Somalia","ZA":"South Africa","GS":"South Georgia and South Sandwich Islands","SS":"South Sudan","ES":"Spain","LK":"Sri Lanka","SH":"St Helena, Ascension, Tristan da Cunha","SD":"Sudan","SR":"Suriname","SJ":"Svalbard","SZ":"Swaziland","SE":"Sweden","CH":"Switzerland","SY":"Syria","TW":"Taiwan","TJ":"Tajikistan","TZ":"Tanzania","TH":"Thailand","TL":"Timor-Leste","TG":"Togo","TK":"Tokelau","TO":"Tonga","TT":"Trinidad and Tobago","TN":"Tunisia","TR":"Turkey","TM":"Turkmenistan","TC":"Turks and Caicos Islands","TV":"Tuvalu","UM":"U.S. Outlying Islands","VI":"U.S. Virgin Islands","UG":"Uganda","UA":"Ukraine","AE":"United Arab Emirates","GB":"United Kingdom","US":"United States","UY":"Uruguay","UZ":"Uzbekistan","VU":"Vanuatu","VA":"Vatican City","VE":"Venezuela","VN":"Vietnam","WF":"Wallis and Futuna","YE":"Yemen","ZM":"Zambia","ZW":"Zimbabwe"}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_448">The phone number you provided is unreachable.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_449">User has exceeded the number of retry attempts.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_code_input_placeholder_text">Verification code</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="strongAuthenticationPhoneNumber" StringId="DisplayName">Phone Number</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>

```

## <a name="verification-display-control-user-interface-elements"></a>验证显示控件用户界面元素

以下是具有[页面布局版本](page-layout.md)2.1.0 或更高版本的[验证显示控件](display-control-verification.md)的 id。

| ID | 默认值 |
| -- | ------------- |
|intro_msg| 验证是必需的。 请单击“发送”按钮。|
|success_send_code_msg | 验证码已发送到收件箱。 请将其复制到下面的输入框。|
|failure_send_code_msg | 我们无法验证你的电子邮件地址。 请输入有效的电子邮件地址，然后重试。|
|success_verify_code_msg | 已验证电子邮件地址。 现在可以继续。|
|failure_verify_code_msg | 我们无法验证你的电子邮件地址。 请重试。|
|but_send_code | 发送验证代码|
|but_verify_code | 验证验证码|
|but_send_new_code | 发送新验证码|
|but_change_claims | 更改电子邮件|

### <a name="verification-display-control-example"></a>验证显示控制示例

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
   <!-- Display control UI elements-->
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="intro_msg">Verification is necessary. Please click Send button.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="success_send_code_msg">Verification code has been sent to your inbox. Please copy it to the input box below.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="failure_send_code_msg">We are having trouble verifying your email address. Please enter a valid email address and try again.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="success_verify_code_msg">E-mail address verified. You can now continue.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="failure_verify_code_msg">We are having trouble verifying your email address. Please try again.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_send_code">Send verification code</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_verify_code">Verify code</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_send_new_code">Send new code</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_change_claims">Change e-mail</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="verification-display-control-user-interface-elements-deprecated"></a>验证显示控件 (弃用的用户界面元素) 

以下是具有[页面布局版本](page-layout.md)2.0.0 的[验证显示控件](display-control-verification.md)的 id。

| ID | 默认值 |
| -- | ------------- |
|verification_control_but_change_claims |更改 |
|verification_control_fail_send_code |无法发送代码，请稍后重试。 |
|verification_control_fail_verify_code |无法验证代码，请稍后重试。 |
|verification_control_but_send_code |发送验证码 |
|verification_control_but_send_new_code |发送新代码 |
|verification_control_but_verify_code |验证验证码 |
|verification_control_code_sent| 已发送验证码。 请将其复制到下面的输入框。 |

### <a name="verification-display-control-example-deprecated"></a> (弃用) 验证显示控制示例

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="UxElement" StringId="verification_control_but_change_claims">Change</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_fail_send_code">Failed to send the code, please try again later.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_fail_verify_code">Failed to verify the code, please try again later.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_but_send_code">Send Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_but_send_new_code">Send New Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_but_verify_code">Verify Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_code_sent">Verification code has been sent. Please copy it to the input box below.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="restful-service-error-messages"></a>Restful 服务错误消息

以下是 [Restful 服务技术配置文件](restful-technical-profile.md)错误消息的 ID：

| ID | 默认值 |
| -- | ------------- |
|DefaultUserMessageIfRequestFailed | 未能建立与 Restful 服务终结点的连接。 Restful 服务 URL：{0} |
|UserMessageIfCircuitOpen | {0}Restful 服务 URL：{1} |
|UserMessageIfDnsResolutionFailed | 未能解析 Restful 服务终结点的主机名。 Restful 服务 URL：{0} |
|UserMessageIfRequestTimeout | 未能在 {0} 秒的超时限制内建立与 Restful 服务终结点的连接。 Restful 服务 URL：{1} |


### <a name="restful-service-example"></a>Restful 服务示例

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="DefaultUserMessageIfRequestFailed">Failed to establish connection to restful service end point.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfCircuitOpen">Unable to connect to the restful service end point.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfDnsResolutionFailed">Failed to resolve the hostname of the restful service endpoint.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfRequestTimeout">Failed to establish connection to restful service end point within timeout limit.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="azure-mfa-error-messages"></a>Azure MFA 错误消息

以下是 [Azure MFA 技术配置文件](multi-factor-auth-technical-profile.md)错误消息的 ID：

| ID | 默认值 |
| -- | ------------- |
|UserMessageIfCouldntSendSms | 无法向手机发送短信，请尝试另一个电话号码。 |
|UserMessageIfInvalidFormat | 电话号码格式无效，请更正，然后重试。|
|UserMessageIfMaxAllowedCodeRetryReached | 输入错误代码的次数过多，请稍后重试。|
|UserMessageIfServerError | 无法使用 MFA 服务，请稍后重试。|
|UserMessageIfThrottled | 请求被阻止，请稍后重试。|
|UserMessageIfWrongCodeEntered|输入的代码错误，请重试。|

### <a name="azure-mfa-example"></a>Azure MFA 示例

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfCouldntSendSms">Cannot Send SMS to the phone, please try another phone number.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidFormat">Your phone number is not in a valid format, please correct it and try again.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMaxAllowedCodeRetryReached">Wrong code entered too many times, please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfServerError">Cannot use MFA service, please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfThrottled">Your request has been throttled, please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfWrongCodeEntered">Wrong code entered, please try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="azure-ad-sspr"></a>Azure AD SSPR

以下是 [Azure AD SSPR 技术配置文件](aad-sspr-technical-profile.md)错误消息的 ID：

| ID | 默认值 |
| -- | ------------- |
|UserMessageIfChallengeExpired | 代码已过期。|
|UserMessageIfInternalError | 电子邮件服务遇到内部错误，请稍后重试。|
|UserMessageIfThrottled | 你发送的请求过多，请稍后重试。|
|UserMessageIfVerificationFailedNoRetry | 已超过最大的验证尝试次数。|
|UserMessageIfVerificationFailedRetryAllowed | 验证失败，请重试。|


### <a name="azure-ad-sspr-example"></a>Azure AD SSPR 示例

```XML
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInternalError">We are having trouble verifying your email address. Please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfThrottled">There have been too many requests to verify this email address. Please wait a while, then try again.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfChallengeExpired">That code is expired. Please request a new code.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfVerificationFailedNoRetry">You've made too many incorrect attempts. Please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfVerificationFailedRetryAllowed">That code is incorrect. Please try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="one-time-password-error-messages"></a>一次性密码错误消息

以下是[一次性密码技术配置文件](one-time-password-technical-profile.md)错误消息的 ID

| ID | 默认值 |
| -- | ------------- |
|UserMessageIfMaxRetryAttempted |一次性密码提供的验证已超过最大尝试次数 |
|UserMessageIfSessionDoesNotExist |一次性密码验证会话已过期 |
|UserMessageIfSessionConflict |一次性密码验证会话存在冲突 |
|UserMessageIfInvalidCode |所提供的用于验证的一次性密码不正确 |
|UserMessageIfVerificationFailedRetryAllowed |验证码不正确。 请重试。 | 

### <a name="one-time-password-example"></a>一次性密码示例

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfSessionDoesNotExist">You have exceeded the maximum time allowed.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMaxRetryAttempted">You have exceeded the number of retries allowed.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidCode">You have entered the wrong code.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfSessionConflict">Cannot verify the code, please try again later.</LocalizedString>
   <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfVerificationFailedRetryAllowed">That code is incorrect. Please try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="claims-transformations-error-messages"></a>声明转换错误消息

以下是声明转换错误消息的 ID：

| ID | 声明转换 | 默认值 |
| -- | ------------- |------------- |
|UserMessageIfClaimsTransformationBooleanValueIsNotEqual |[AssertBooleanClaimIsEqualToValue](boolean-transformations.md#assertbooleanclaimisequaltovalue) | 声明类型“inputClaim”的布尔声明值比较失败。| 
|DateTimeGreaterThan |[AssertDateTimeIsGreaterThan](date-transformations.md#assertdatetimeisgreaterthan) | 声明值比较失败：提供的左操作数大于右操作数。|
|UserMessageIfClaimsTransformationStringsAreNotEqual |[AssertStringClaimsAreEqual](string-transformations.md#assertstringclaimsareequal) | 使用 StringComparison“OrdinalIgnoreCase”的声明值比较失败。|

### <a name="claims-transformations-example"></a>声明转换示例

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsTransformationBooleanValueIsNotEqual">Your email address hasn't been verified.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="DateTimeGreaterThan">Expiration date must be greater that the current date.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsTransformationStringsAreNotEqual">The email entry fields do not match. Please enter the same email address in both fields and try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="next-steps"></a>后续步骤

有关本地化示例，请参阅以下文章：

- [在 Azure Active Directory B2C 中使用自定义策略进行语言自定义](custom-policy-localization.md)
- [在 Azure Active Directory B2C 中使用用户流进行语言自定义](user-flow-language-customization.md)
