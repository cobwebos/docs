---
title: 本地化字符串 ID - Azure Active Directory B2C | Microsoft Docs
description: 在 Azure Active Directory B2C 中为自定义策略中 ID 为 api.signuporsignin 的内容定义指定 ID。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 05/02/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: aa90775df4462328ed7c39e70c8dd1989248e308
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2020
ms.locfileid: "82900515"
---
# <a name="localization-string-ids"></a>本地化字符串 ID

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Localization 元素使你能够在用户旅程的策略中支持多个区域设置或语言。 本文提供了可在策略中使用的本地化 ID 列表。 若要熟悉 UI 本地化，请参阅[本地化](localization.md)。

## <a name="sign-up-or-sign-in-page-elements"></a>注册或登录页面元素

以下 ID 用于 ID 为 `api.signuporsignin` 的内容定义。

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
| password | 密码 |
| **requiredField_password** | 请输入密码 |
| **invalid_password** | 你输入的密码格式不正确。 |
| **forgotpassword_link** | 忘记密码了? |
| **createaccount_intro** | 没有帐户？ |
| **createaccount_link** | 立即注册 |
| **divider_title** | OR |
| **cancel_message** | 用户忘记了密码 |
| **button_signin** | 登录 |
| **social_intro** | 使用社交帐户登录 |
  **remember_me** |使我保持登录状态|
| **unknown_error** | 登录时遇到问题。 请稍后重试。 |

下面的示例演示如何在注册或登录页中使用某些用户界面元素：

![注册或登录页面 UX 元素](./media/localization-string-ids/localization-susi.png)

标识提供者的 ID 在用户旅程 ClaimsExchange 元素中配置。 若要本地化标识提供者的标题，请将 ElementType 设置为 `ClaimsProvider`，同时将 StringId 设置为 `ClaimsExchange` 的 ID。

```XML
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

```XML
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

## <a name="sign-up-and-self-asserted-pages-user-interface-elements"></a>注册和自断言页用户界面元素

以下是包含 `api.localaccountsignup` ID 的内容定义或任何以 `api.selfasserted` 开头的内容定义的 ID，如 `api.selfasserted.profileupdate` 和 `api.localaccountpasswordreset`。

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
| month | 月份 |
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


## <a name="phone-factor-authentication-page-user-interface-elements"></a>电话身份验证页用户界面元素

以下是包含 `api.phonefactor` ID 的内容定义的 ID。

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
| **countryList** | {\"默认\":\"国家/地区\",\"AF\":\"阿富汗\",\"AX\":\"奥兰群岛\",\"AL\":\"阿尔巴尼亚\",\"DZ\":\"阿尔及利亚\",\"AS\":\"美属萨摩亚\",\"AD\":\"安道尔\",\"AO\":\"安哥拉\",\"AI\":\"安圭拉\",\"AQ\":\"南极洲\",\"AG\":\"安提瓜和巴布达\",\"AR\":\"阿根廷\",\"AM\":\"亚美尼亚\",\"AW\":\"阿鲁巴\",\"AU\":\"澳大利亚\",\"AT\":\"奥地利\",\"AZ\":\"阿塞拜疆\",\"BS\":\"巴哈马\",\"BH\":\"巴林\",\"BD\":\"孟加拉国\",\"BB\":\"巴巴多斯\",\"BY\":\"白俄罗斯\",\"BE\":\"比利时\",\"BZ\":\"伯利兹\",\"BJ\":\"贝宁\",\"BM\":\"百慕大\",\"BT\":\"不丹\",\"BO\":\"玻利维亚\",\"BQ\":\"博内尔岛\",\"BA\":\"波斯尼亚和黑塞哥维那\",\"BW\":\"博茨瓦纳\",\"BV\":\"布维岛\",\"BR\":\"巴西\",\"IO\":\"英属印度洋领地\",\"VG\":\"英属维尔京群岛\",\"BN\":\"文莱\",\"BG\":\"保加利亚\",\"BF\":\"布基纳法索\",\"BI\":\"布隆迪\",\"CV\":\"佛得角\",\"KH\":\"柬埔寨\",\"CM\":\"喀麦隆\",\"CA\":\"加拿大\",\"KY\":\"开曼群岛\",\"CF\":\"中非共和国\",\"TD\":\"乍得\",\"CL\":\"智利\",\"CN\":\"中国\",\"CX\":\"圣延岛\",\"CC\":\"科科斯群岛(基林群岛)\",\"CO\":\"哥伦比亚\",\"KM\":\"科摩罗\",\"CG\":\"刚果\",\"CD\":\"刚果(金)\",\"CK\":\"库克群岛\",\"CR\":\"哥斯达黎加\",\"CI\":\"科特迪瓦\",\"HR\":\"克罗地亚\",\"CU\":\"古巴\",\"CW\":\"库拉索岛\",\"CY\":\"塞浦路斯\",\"CZ\":\"捷克共和国\",\"DK\":\"丹麦\",\"DJ\":\"吉布提\",\"DM\":\"多米尼加岛\",\"DO\":\"多米尼加共和国\",\"EC\":\"厄瓜多尔\",\"EG\":\"埃及\",\"SV\":\"萨尔瓦多\",\"GQ\":\"赤道几内亚\",\"ER\":\"厄立特里亚国\",\"EE\":\"爱沙尼亚\",\"ET\":\"埃塞俄比亚\",\"FK\":\"福克兰群岛\",\"FO\":\"法罗群岛\",\"FJ\":\"斐济\",\"FI\":\"芬兰\",\"FR\":\"法国\",\"GF\":\"法属圭亚那\",\"PF\":\"法属玻里尼西亚\",\"TF\":\"法属南半球领地\",\"GA\":\"加蓬\",\"GM\":\"冈比亚\",\"GE\":\"格鲁吉亚\",\"DE\":\"德国\",\"GH\":\"加纳\",\"GI\":\"直布罗陀\",\"GR\":\"希腊\",\"GL\":\"格陵兰岛\",\"GD\":\"格林纳达\",\"GP\":\"瓜德罗普岛\",\"GU\":\"关岛\",\"GT\":\"危地马拉\",\"GG\":\"根西岛\",\"GN\":\"几内亚\",\"GW\":\"几内亚比绍\",\"GY\":\"圭亚那\",\"HT\":\"海地\",\"HM\":\"赫德岛和麦克唐纳群岛\",\"HN\":\"洪都拉斯\",\"HK\":\"香港特别行政区\",\"HU\":\"匈牙利\",\"IS\":\"冰岛\",\"IN\":\"印度\",\"ID\":\"印度尼西亚\",\"IR\":\"伊朗\",\"IQ\":\"伊拉克\",\"IE\":\"爱尔兰\",\"IM\":\"曼岛\",\"IL\":\"以色列\",\"IT\":\"意大利\",\"JM\":\"牙买加\",\"JP\":\"日本\",\"JE\":\"泽西岛\",\"JO\":\"约旦\",\"KZ\":\"哈萨克斯坦\",\"KE\":\"肯尼亚\",\"KI\":\"基里巴斯\",\"KR\":\"韩国\",\"KW\":\"科威特\",\"KG\":\"吉尔吉斯斯坦\",\"LA\":\"老挝\",\"LV\":\"拉脱维亚\",\"LB\":\"黎巴嫩\",\"LS\":\"莱索托\",\"LR\":\"利比里亚\",\"LY\":\"利比亚\",\"LI\":\"列支敦斯登\",\"LT\":\"立陶宛\",\"LU\":\"卢森堡公国\",\"MO\":\"澳门特别行政区\",\"MK\":\"北马其顿\",\"MG\":\"马达加斯加岛\",\"MW\":\"马拉维\",\"MY\":\"马来西亚\",\"MV\":\"马尔代夫\",\"ML\":\"马里\",\"MT\":\"马尔他\",\"MH\":\"马绍尔群岛\",\"MQ\":\"马提尼克\",\"MR\":\"毛里塔尼亚\",\"MU\":\"毛里求斯\",\"YT\":\"马约特岛\",\"MX\":\"墨西哥\",\"FM\":\"密克罗尼西亚群岛\",\"MD\":\"摩尔多瓦\",\"MC\":\"摩纳哥\",\"MN\":\"蒙古\",\"ME\":\"黑山共和国\",\"MS\":\"蒙特色拉特岛\",\"MA\":\"摩洛哥\",\"MZ\":\"莫桑比克\",\"MM\":\"缅甸\",\"NA\":\"纳米比亚\",\"NR\":\"瑙鲁\",\"NP\":\"尼泊尔\",\"NL\":\"荷兰\",\"NC\":\"新喀里多尼亚\",\"NZ\":\"新西兰\",\"NI\":\"尼加拉瓜\",\"NE\":\"尼日尔\",\"NG\":\"尼日利亚\",\"NU\":\"纽埃岛\",\"NF\":\"诺福克岛\",\"KP\":\"朝鲜\",\"MP\":\"北马里亚纳群岛\",\"NO\":\"挪威\",\"OM\":\"阿曼\",\"PK\":\"巴基斯坦\",\"PW\":\"帕劳群岛\",\"PS\":\"巴勒斯坦自治政府\",\"PA\":\"巴拿马\",\"PG\":\"巴布亚新几内亚\",\"PY\":\"巴拉圭\",\"PE\":\"秘鲁\",\"PH\":\"菲律宾\",\"PN\":\"皮特凯恩群岛\",\"PL\":\"波兰\",\"PT\":\"葡萄牙\",\"PR\":\"波多黎各\",\"QA\":\"卡塔尔\",\"RE\":\"留尼汪\",\"RO\":\"罗马尼亚\",\"RU\":\"俄罗斯\",\"RW\":\"卢旺达\",\"BL\":\"圣巴托洛缪岛\",\"KN\":\"圣基茨和尼维斯\",\"LC\":\"圣卢西亚岛\",\"MF\":\"法属圣马丁\",\"PM\":\"圣皮埃尔和密克隆群岛\",\"VC\":\"圣文森特和格林纳丁斯\",\"WS\":\"萨摩亚\",\"SM\":\"圣马力诺\",\"ST\":\"圣多美和普林西比\",\"SA\":\"沙特阿拉伯\",\"SN\":\"塞内加尔\",\"RS\":\"塞尔维亚\",\"SC\":\"塞舌尔群岛\",\"SL\":\"塞拉利昂\",\"SG\":\"新加坡\",\"SX\":\"圣马丁岛\",\"SK\":\"斯洛伐克\",\"SI\":\"斯洛文尼亚\",\"SB\":\"所罗门群岛\",\"SO\":\"索马里\",\"ZA\":\"南非共和国\",\"GS\":\"南乔治亚岛和南三明治群岛\",\"SS\":\"南苏丹\",\"ES\":\"西班牙\",\"LK\":\"斯里兰卡\",\"SH\":\"圣赫勒拿岛,阿森松,特里斯坦-达库尼亚群岛\",\"SD\":\"苏丹\",\"SR\":\"苏里南\",\"SJ\":\"斯瓦尔巴特群岛\",\"SZ\":\"斯威士兰\",\"SE\":\"瑞典\",\"CH\":\"瑞士\",\"SY\":\"叙利亚共和国\",\"TW\":\"台湾\",\"TJ\":\"塔吉克斯坦\",\"TZ\":\"坦桑尼亚\",\"TH\":\"泰国\",\"TL\":\"东帝汶\",\"TG\":\"多哥\",\"TK\":\"托克劳群岛\",\"TO\":\"汤加\",\"TT\":\"特立尼达和多巴哥\",\"TN\":\"突尼斯\",\"TR\":\"土耳其\",\"TM\":\"土库曼斯坦\",\"TC\":\"特克斯和凯科斯群岛\",\"TV\":\"图瓦卢\",\"UM\":\"美属离岛\",\"VI\":\"美属维尔京群岛\",\"UG\":\"乌干达\",\"UA\":\"乌克兰\",\"AE\":\"阿拉伯联合酋长国\",\"GB\":\"大不列颠及北爱尔兰联合王国\",\"美国\":\"美国\",\"UY\":\"乌拉圭\",\"UZ\":\"乌兹别克斯坦\",\"VU\":\"瓦努阿图\",\"VA\":\"梵蒂冈城\",\"VE\":\"委内瑞拉\",\"VN\":\"越南\",\"WF\":\"瓦利斯和富图纳\",\"YE\":\"也门\",\"ZM\":\"赞比亚\",\"ZW\":\"津巴布韦\"} |
| **error_448** | 你提供的电话号码打不通。 |
| **error_449** | 用户已超出重试次数上限。 |
| **verification_code_input_placeholder_text** | 验证码 |

下面的示例演示如何在 MFA 注册页中使用某些用户界面元素：

![注册页电子邮件验证 UX 元素](./media/localization-string-ids/localization-mfa1.png)

下面的示例演示如何在 MFA 验证页中使用某些用户界面元素：

![注册页电子邮件验证 UX 元素](./media/localization-string-ids/localization-mfa2.png)

## <a name="verification-display-control-user-interface-elements"></a>验证显示控件用户界面元素

下面是[验证显示控件](display-control-verification.md)的 ID

| ID | 默认值 |
| -- | ------------- |
|verification_control_but_change_claims |更改 |
|verification_control_fail_send_code |无法发送验证码，请稍后重试。 |
|verification_control_fail_verify_code |无法验证验证码，请稍后重试。 |
|verification_control_but_send_code |发送验证码 |
|verification_control_but_send_new_code |发送新验证码 |
|verification_control_but_verify_code |验证验证码 |
|verification_control_code_sent| 已发送验证码。 请将其复制到下面的输入框。 |

### <a name="example"></a>示例

```XML
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

## <a name="azure-mfa-error-messages"></a>Azure MFA 错误消息

下面是 [Azure MFA 技术配置文件](multi-factor-auth-technical-profile.md)错误消息的 ID：

| ID | 默认值 |
| -- | ------------- |
|UserMessageIfCouldntSendSms | 无法向手机发送短信，请尝试其他电话号码。 |
|UserMessageIfInvalidFormat | 你的电话号码格式无效，请更正并重试。|
|UserMessageIfMaxAllowedCodeRetryReached | 输入错误代码次数过多，请稍后重试。|
|UserMessageIfServerError | 无法使用 MFA 服务，请稍后重试。|
|UserMessageIfThrottled | 你的请求已被限制，请稍后重试。|
|UserMessageIfWrongCodeEntered|输入的代码错误，请重试。|

### <a name="example"></a>示例

```XML
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

## <a name="one-time-password-error-messages"></a>一次性密码错误消息

下面是[一次性密码技术配置文件](one-time-password-technical-profile.md)错误消息的 ID

| ID | 默认值 |
| -- | ------------- |
|UserMessageIfMaxRetryAttempted |一次性密码提供的验证已超过最大尝试次数 |
|UserMessageIfSessionDoesNotExist |一次性密码验证会话已过期 |
|UserMessageIfSessionConflict |一次性密码验证会话存在冲突 |
|UserMessageIfInvalidCode |所提供的用于验证的一次性密码不正确 |
|UserMessageIfVerificationFailedRetryAllowed |验证码不正确。 请重试。 | 

### <a name="example"></a>示例

```XML
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfSessionDoesNotExist">You have exceed the maximum time allowed.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMaxRetryAttempted">You have exceed the number of retries allowed.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidCode">You have entered the wrong code.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfSessionConflict">Cannot verify the code, please try again later.</LocalizedString>
   <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfVerificationFailedRetryAllowed">That code is incorrect. Please try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```


## <a name="claims-transformations-error-messages"></a>声明转换错误消息

下面是声明转换错误消息的 ID：

| ID | 声明转换 | 默认值 |
| -- | ------------- |------------- |
|UserMessageIfClaimsTransformationBooleanValueIsNotEqual |[AssertBooleanClaimIsEqualToValue](boolean-transformations.md#assertbooleanclaimisequaltovalue) | 针对声明类型“inputClaim”的布尔声明值比较失败。| 
|DateTimeGreaterThan |[AssertDateTimeIsGreaterThan](date-transformations.md#assertdatetimeisgreaterthan) | 声明值比较失败：提供的左操作数大于右操作数。|
|UserMessageIfClaimsTransformationStringsAreNotEqual |[AssertStringClaimsAreEqual](string-transformations.md#assertstringclaimsareequal) | 使用 StringComparison“OrdinalIgnoreCase”进行声明值比较失败。|

### <a name="example"></a>示例

```XML
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsTransformationBooleanValueIsNotEqual">Your email address hasn't been verified.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="DateTimeGreaterThan">Expiration date must be greater that the current date.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsTransformationStringsAreNotEqual">The email entry fields do not match. Please enter the same email address in both fields and try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```










