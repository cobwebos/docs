---
title: 适用于 Azure MFA 的 Azure Active Directory 访问和使用情况报告
description: 介绍如何使用 Azure 多重身份验证功能 - 报告。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9cf0b848e551d3c0e7d7275af9eb2c8e970dbd9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67113402"
---
# <a name="reports-in-azure-multi-factor-authentication"></a>Azure 多重身份验证中的报告

Azure 多重身份验证提供了几个可通过 Azure 门户访问的报告，供你和你的组织使用。 下表列出了可用的报告：

| 报表 | Location | 描述 |
|:--- |:--- |:--- |
| 阻止的用户历史记录 | Azure AD > MFA 服务器 > 阻止/解除阻止用户 | 显示请求阻止或解除阻止用户的历史记录。 |
| 使用情况和欺诈警报 | Azure AD > 登录 | 提供有关总体使用情况、用户摘要和用户详细信息的信息；以及指定日期范围内提交的欺诈警报的历史记录。 |
| 本地组件的使用情况 | Azure AD > MFA 服务器 > 活动报告 | 提供有关通过 NPS 扩展、ADFS 和 MFA 服务器实现的 MFA 的总体使用情况信息。 |
| 跳过的用户历史记录 | Azure AD > MFA 服务器 > 免验证一次 | 提供请求对用户跳过多重身份验证的历史记录。 |
| 服务器状态 | Azure AD > MFA 服务器 > 服务器状态 | 显示与帐户关联的多重身份验证服务器的状态。 |

## <a name="view-mfa-reports"></a>查看 MFA 报告

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在左侧选择“Azure Active Directory”   > “MFA 服务器”  。
3. 选择要查看的报告。

   ![MFA 服务器在 Azure 门户中的服务器状态报告](./media/howto-mfa-reporting/report.png)

## <a name="azure-ad-sign-ins-report"></a>Azure AD 登录报告

通过 [Azure 门户](https://portal.azure.com)中的**登录活动报告**，可以获取确定环境运行状况所需的信息。

登录报告可以提供托管应用程序的使用和用户登录活动方面的信息，其中包括多重身份验证 (MFA) 使用方面的信息。 可以通过 MFA 数据了解 MFA 在组织中的使用情况。 可以通过它来了解以下情况：

- 登录时是否进行了 MFA？
- 用户如何完成 MFA？
- 用户无法完成 MFA 的原因是什么？
- 对多少用户进行了 MFA？
- 无法完成 MFA 质询的用户有多少？
- 最终用户遇到的常见 MFA 问题有哪些？

此数据可通过 [Azure 门户](https://portal.azure.com)和[报告 API](../reports-monitoring/concept-reporting-api.md) 获取。

![在 Azure 门户中的 azure AD 登录报告](./media/howto-mfa-reporting/sign-in-report.png)

### <a name="sign-ins-report-structure"></a>登录报告结构

可以通过 MFA 登录活动报告了解以下信息：

**需要 MFA：** 登录时是否需要 MFA。 由于每个用户 MFA、 条件性访问或由于其他原因可能需要使用 MFA。 可能的值为“是”  或“否”  。

**MFA 结果：** 有关 MFA 是通过还是未通过的详细信息：

- 如果 MFA 通过，则此列会提供 MFA 如何通过的详细信息。
   - Azure 多重身份验证
      - 已在云中完成
      - 已到期，因为在租户上配置了相关策略
      - 系统提示注册
      - 已通过（根据令牌中的声明）
      - 已通过（根据外部提供程序提供的声明）
      - 已通过（采用强身份验证）
      - 已跳过，因为执行的流为 Windows 中转站登录流
      - 已跳过，因为应用密码原因
      - 已跳过，因为位置原因
      - 已跳过，因为设备已注册
      - 已跳过，因为系统已记住该设备
      - 已成功完成
   - 已重定向到多重身份验证的外部提供程序

- 如果 MFA 未通过，则此列会提供未通过的原因。
   - Azure 多重身份验证未通过；
      - 身份验证正在进行中
      - 重复进行身份验证尝试
      - 输入错误代码次数过多
      - 身份验证无效
      - 移动应用验证码无效
      - 配置错误
      - 电话呼叫转到语音邮件
      - 电话号码格式无效
      - 服务错误
      - 无法接通用户的电话
      - 无法向设备发送移动应用通知
      - 无法发送移动应用通知
      - 用户已拒绝身份验证
      - 用户未响应移动应用通知
      - 用户没有注册任何验证方法
      - 用户输入了不正确的代码
      - 用户输入了不正确的 PIN
      - 用户挂断了电话，没有成功进行身份验证
      - 用户被阻止
      - 用户从未输入验证代码
      - 找不到用户
      - 验证代码已使用过一次

**MFA 身份验证方法：** 用户用来完成 MFA 的身份验证方法。 可能的值包括：

- 短信
- 移动应用通知
- 电话呼叫（身份验证电话）
- 移动应用验证码
- 电话呼叫（办公电话）
- 电话呼叫（其他身份验证电话）

**MFA 身份验证详细信息：** 清理版的电话号码，例如：+X XXXXXXXX64。

**条件性访问**查找有关受影响的登录尝试包括的条件性访问策略的信息：

- 策略名称
- 授权控制
- 会话控制
- 结果

## <a name="powershell-reporting-on-users-registered-for-mfa"></a>PowerShell 报告关于用户的已注册 MFA

首先，确保您拥有[MSOnline V1 PowerShell 模块](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-1.0)安装。

使用后面的 PowerShell 标识已注册 MFA 的用户。

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName```

使用后面的 PowerShell 标识未注册 MFA 的用户。

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName```

## <a name="possible-results-in-activity-reports"></a>活动报告中可能的结果

下表可以用于对多重身份验证使用下载的版本的多重身份验证活动报告进行故障排除。 它们不会直接在 Azure 门户中。

| 调用结果 | 描述 | 广泛的说明 |
| --- | --- | --- |
| SUCCESS_WITH_PIN | 输入 PIN | 用户输入 PIN。  如果身份验证成功，则他们输入正确的 PIN。  如果身份验证被拒绝，则他们输入 PIN 不正确或用户被设置为标准模式。 |
| SUCCESS_NO_PIN | 只输入 # | 如果用户被设置为 PIN 模式并且身份验证被拒绝，这意味着用户没有输入其 PIN 和只输入了 #。  如果用户设置为标准模式并且身份验证成功，这表示用户只输入了 #，而这在标准模式下是正确的做法。 |
| SUCCESS_WITH_PIN_BUT_TIMEOUT | # 条目后未按 | 未输入 #，因为用户未发送任何 DTMF 数字。  除非输入 #，该值指示项的完成，不会发送其他输入的数字。 |
|SUCCESS_NO_PIN_BUT_TIMEOUT | 无电话输入-超时 | 呼叫已应答，但没有响应。  这通常指示调用已由语音信箱接听。 |
| SUCCESS_PIN_EXPIRED | PIN 已过期且未更改 | 用户的 PIN 已过期和它们已提示用户更改，但未成功完成 PIN 的更改。 |
| SUCCESS_USED_CACHE | 使用的缓存 | 由于已配置的缓存时间范围内发生相同的用户名的上一个成功身份验证，身份验证成功而无需多重身份验证呼叫。 |
| SUCCESS_BYPASSED_AUTH | 已免身份验证 | 身份验证已成功使用为用户发起免验证一次。  有关免验证，请参阅绕过用户历史记录报告的更多详细信息。 |
| SUCCESS_USED_IP_BASED_CACHE | 使用基于 IP 的缓存 | 自上一个成功进行身份验证相同的用户名、 身份验证类型、 应用程序名称，而无需多重身份验证呼叫已成功的身份验证和 IP 配置的缓存时间范围内发生。 |
| SUCCESS_USED_APP_BASED_CACHE | 使用基于应用程序的缓存 | 自上一个成功进行身份验证相同的用户名、 身份验证类型和配置的缓存时间范围内的应用程序名称，而无需多重身份验证呼叫成功身份验证。 |
| SUCCESS_INVALID_INPUT | 电话输入无效 | 通过手机发送的响应无效。  这可能是来自传真机或调制解调器或用户可能已进入 * 作为其 PIN 的一部分。 |
| SUCCESS_USER_BLOCKED | 用户被阻止 | 用户的电话号码被阻止。  通过身份验证呼叫期间用户或管理员使用 Azure 门户，可以启动号码被阻止。 <br> 注意：  被阻止的号码还会导致欺诈警告。 |
| SUCCESS_SMS_AUTHENTICATED | 文本消息进行身份验证 | 对于双向文本消息用户正确进行了回复使用了一次性密码 (OTP) 或 OTP + PIN。 |
| SUCCESS_SMS_SENT | 发送短信 | 对于文本消息，已成功发送包含一次性密码 (OTP) 的文本消息。  用户将在要完成身份验证的应用程序中输入 OTP 或 OTP + PIN。 |
| SUCCESS_PHONE_APP_AUTHENTICATED | 移动应用进行身份验证 | 已成功通过移动应用身份验证的用户。 |
| SUCCESS_OATH_CODE_PENDING | OATH 代码待处理 | 用户已提示你输入其 OATH 代码，但未做出答复。 |
| SUCCESS_OATH_CODE_VERIFIED | 已验证 OATH 代码 | 用户输入有效的 OATH 代码出现提示时。 |
| SUCCESS_FALLBACK_OATH_CODE_VERIFIED | 已验证回退 OATH 代码 | 用户已拒绝使用其主要多重身份验证方法进行身份验证，然后提供一个有效的 OATH 代码进行回退。 |
| SUCCESS_FALLBACK_SECURITY_QUESTIONS_ANSWERED | 已回答回退安全问题 | 用户被拒绝使用其主要多重身份验证方法进行身份验证，并且然后回答其安全问题正确进行回退。 |
| FAILED_PHONE_BUSY | 已在进行中的身份验证 | 多重身份验证已在处理此用户的身份验证。  这通常被因为在同一个登录过程中发送多个身份验证请求的 RADIUS 客户端。 |
| CONFIG_ISSUE | 无法接通电话 | 已尝试调用，但可能不是放置或没有相应解答。  这包括忙信号、 快忙信号 （已断开连接）、 三连音 （不能再在服务中的数字） 时超时响铃，等等。 |
| FAILED_INVALID_PHONENUMBER | 电话号码格式无效 | 电话号码包含无效的格式。  电话号码必须是数字，必须为 10 位数国家/地区代码 + 1 （美国和加拿大）。 |
| FAILED_USER_HUNGUP_ON_US | 用户已挂断电话 | 用户接听电话，但是没有按任何按钮就挂断。 |
| FAILED_INVALID_EXTENSION | 无效的扩展 | 该扩展包含无效字符。  仅数字、 逗号、 *，以及允许 #。  @ 前缀，也可以使用。 |
| FAILED_FRAUD_CODE_ENTERED | 已输入欺诈代码 | 用户会导致身份验证被拒绝和电话号码被阻止的调用期间选择举报欺诈行为。| 
| FAILED_SERVER_ERROR | 无法拨打电话 | 多重身份验证服务不能进行呼叫。 |
| FAILED_SMS_NOT_SENT | 无法发送短信 | 无法发送短信。  身份验证被拒绝。 |
| FAILED_SMS_OTP_INCORRECT | 短信 OTP 不正确 | 用户从他们用于接收文本消息输入不正确的一次性密码 (OTP)。  身份验证被拒绝。 |
| FAILED_SMS_OTP_PIN_INCORRECT | 短信 OTP + PIN 不正确 | 用户输入不正确的一次性密码 (OTP) 和/或不正确的用户 PIN。  身份验证被拒绝。 |
| FAILED_SMS_MAX_OTP_RETRY_REACHED | 超过最大文本消息 OTP 尝试次数 | 用户已超出最大一次性密码 (OTP) 尝试次数。 |
| FAILED_PHONE_APP_DENIED | 已拒绝移动应用程序 | 通过按拒绝按钮，拒绝用户的移动应用中的身份验证。 |
| FAILED_PHONE_APP_INVALID_PIN | 移动应用程序 PIN 无效 | 用户的移动应用中进行身份验证时输入 PIN 无效。 |
| FAILED_PHONE_APP_PIN_NOT_CHANGED | 移动应用程序 PIN 未更改 | 用户未成功完成的移动应用中需要的更改 PIN。 |
| FAILED_FRAUD_REPORTED | 已举报欺诈行为 | 用户已报告欺诈的移动应用中。 |
| FAILED_PHONE_APP_NO_RESPONSE | 移动应用程序没有响应 | 用户未响应移动应用身份验证请求。 |
| FAILED_PHONE_APP_ALL_DEVICES_BLOCKED | 移动应用的所有设备上阻止 | 此用户的移动应用设备不再对通知做出响应，并且已被阻止。 |
| FAILED_PHONE_APP_NOTIFICATION_FAILED | 移动应用程序通知失败 | 当尝试将通知发送到用户的设备上的移动应用时出错。 |
| FAILED_PHONE_APP_INVALID_RESULT | 移动应用程序结果无效 | 移动应用程序返回了无效的结果。 |
| FAILED_OATH_CODE_INCORRECT | OATH 代码不正确 | 用户输入 OATH 代码不正确。  身份验证被拒绝。 |
| FAILED_OATH_CODE_PIN_INCORRECT | OATH 代码 + PIN 不正确 | 用户输入 OATH 代码不正确和/或不正确的用户 PIN。  身份验证被拒绝。 |
| FAILED_OATH_CODE_DUPLICATE | 重复 OATH 代码 | 用户输入以前使用过的 OATH 代码。  身份验证被拒绝。 |
| FAILED_OATH_CODE_OLD | OATH 代码已过期 | 用户输入的 OATH 代码之前以前使用过的 OATH 代码。  身份验证被拒绝。 |
| FAILED_OATH_TOKEN_TIMEOUT | OATH 代码结果超时 | 用户时间太长，若要输入的 OATH 代码，并有多重身份验证尝试已超时。 |
| FAILED_SECURITY_QUESTIONS_TIMEOUT | 安全问题结果超时 | 用户时间太长，若要输入到安全问题的答案和多重身份验证尝试已超时。 |
| FAILED_AUTH_RESULT_TIMEOUT | 身份验证结果超时 | 用户时间太长，若要完成多重身份验证尝试。 |
| FAILED_AUTHENTICATION_THROTTLED | 身份验证限制 | 多重身份验证尝试已阻止该服务。 |

## <a name="next-steps"></a>后续步骤

* [面向用户](../user-help/multi-factor-authentication-end-user.md)
* [部署位置](concept-mfa-whichversion.md)
