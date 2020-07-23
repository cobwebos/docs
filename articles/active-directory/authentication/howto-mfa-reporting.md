---
title: Azure 多重身份验证的登录事件详细信息 - Azure Active Directory
description: 了解如何查看 Azure 多重身份验证事件和状态消息的登录活动。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 05/15/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: c9bf76729c3b5844918659283a65eeb347c4237d
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83639835"
---
# <a name="use-the-sign-ins-report-to-review-azure-multi-factor-authentication-events"></a>使用登录报告查看 Azure 多重身份验证事件

若要查看和了解 Azure 多重身份验证事件，可以使用 Azure Active Directory (Azure AD) 登录报告。 此报告显示提示用户进行多重身份验证时事件的身份验证详细信息，以及是否正在使用任何条件访问策略。 有关登录报告的详细信息，请参阅 [Azure AD 中的登录活动报告概述](../reports-monitoring/concept-sign-ins.md)。

本文介绍如何查看 Azure 门户中的 Azure AD 登录报告，然后查看 MSOnline V1 PowerShell 模块。

## <a name="view-the-azure-ad-sign-ins-report"></a>查看 Azure AD 登录报告

登录报告提供托管应用程序的使用和用户登录活动方面的信息，其中包括多重身份验证 (MFA) 使用方面的信息。 可以通过 MFA 数据了解 MFA 在组织中的使用情况。 它使你能够你回答如下问题：

- 登录时是否进行了 MFA？
- 用户如何完成 MFA？
- 用户无法完成 MFA 的原因是什么？
- 对多少用户进行了 MFA？
- 无法完成 MFA 质询的用户有多少？
- 最终用户遇到的常见 MFA 问题有哪些？

若要查看 [Azure 门户](https://portal.azure.com)中的登录活动报告，请完成以下步骤。 还可以使用[报告 API](../reports-monitoring/concept-reporting-api.md) 来查询数据。

1. 使用拥有全局管理员权限的帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 搜索并选择“Azure Active Directory”，然后从左侧菜单中选择“用户” 。
1. 从左侧菜单中的“活动”下，选择“登录”。
1. 显示了登录事件的列表，包括状态。 可以选择一个事件以查看更多详细信息。

    事件详细信息的“身份验证详细信息”或“条件访问”选项卡显示状态代码或触发了 MFA 提示的策略 。

    [![](media/howto-mfa-reporting/sign-in-report-cropped.png "Screenshot of example Azure Active Directory sign-ins report in the Azure portal")](media/howto-mfa-reporting/sign-in-report.png#lightbox)

如果可用，将显示身份验证，如短信、Microsoft Authenticator 应用通知或电话呼叫。

以下详细信息显示在登录事件的“身份验证详细信息”窗口上，该事件显示 MFA 请求是得到满足还是遭到拒绝：

* 如果 MFA 通过，则此列会提供 MFA 如何通过的详细信息。
   * 已在云中完成
   * 已到期，因为在租户上配置了相关策略
   * 系统提示注册
   * 已通过（根据令牌中的声明）
   * 已通过（根据外部提供程序提供的声明）
   * 已通过（采用强身份验证）
   * 已跳过，因为执行的流为 Windows 中转站登录流
   * 已跳过，因为应用密码原因
   * 已跳过，因为位置原因
   * 已跳过，因为设备已注册
   * 已跳过，因为系统已记住该设备
   * 已成功完成

* 如果 MFA 未通过，则此列会提供未通过的原因。
   * 身份验证正在进行中
   * 重复进行身份验证尝试
   * 输入错误代码次数过多
   * 身份验证无效
   * 移动应用验证码无效
   * 配置错误
   * 电话呼叫转到语音邮件
   * 电话号码格式无效
   * 服务错误
   * 无法接通用户的电话
   * 无法向设备发送移动应用通知
   * 无法发送移动应用通知
   * 用户已拒绝身份验证
   * 用户未响应移动应用通知
   * 用户没有注册任何验证方法
   * 用户输入了不正确的代码
   * 用户输入了不正确的 PIN
   * 用户挂断了电话，没有成功进行身份验证
   * 用户被阻止
   * 用户从未输入验证代码
   * 找不到用户
   * 验证代码已使用过一次

## <a name="powershell-reporting-on-users-registered-for-mfa"></a>针对已注册 MFA 用户的 PowerShell 报告

首先，请确保已安装 [MSOnline V1 PowerShell 模块](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-1.0)。

使用后面的 PowerShell 标识已注册 MFA 的用户。 这一组命令会排除已禁用的用户，因为这些帐户无法针对 Azure AD 进行身份验证：

```powershell
Get-MsolUser -All | Where-Object {$_.StrongAuthenticationMethods -ne $null -and $_.BlockCredential -eq $False} | Select-Object -Property UserPrincipalName
```

使用后面的 PowerShell 标识未注册 MFA 的用户。 这一组命令会排除已禁用的用户，因为这些帐户无法针对 Azure AD 进行身份验证：

```powershell
Get-MsolUser -All | Where-Object {$_.StrongAuthenticationMethods.Count -eq 0 -and $_.BlockCredential -eq $False} | Select-Object -Property UserPrincipalName
```

标识已注册的用户和输出方法：

```powershell
Get-MsolUser -All | Select-Object @{N='UserPrincipalName';E={$_.UserPrincipalName}},

@{N='MFA Status';E={if ($_.StrongAuthenticationRequirements.State){$_.StrongAuthenticationRequirements.State} else {"Disabled"}}},

@{N='MFA Methods';E={$_.StrongAuthenticationMethods.methodtype}} | Export-Csv -Path c:\MFA_Report.csv -NoTypeInformation
```

## <a name="downloaded-activity-reports-result-codes"></a>已下载的活动报告结果代码

下表使用来自先前门户步骤或 PowerShell 命令的下载版活动报告来帮助排查事件。 这些结果代码不会直接显示在 Azure 门户中。

| 呼叫结果 | 说明 | 广泛说明 |
| --- | --- | --- |
| SUCCESS_WITH_PIN | 已输入 PIN | 用户已输入 PIN。  如果身份验证成功，则用户输入了正确的 PIN。  如果身份验证被拒绝，则他们输入的 PIN 不正确或用户被设置为标准模式。 |
| SUCCESS_NO_PIN | 仅输入 # | 如果用户被设置为 PIN 模式且身份验证被拒绝，这表示用户没有输入自己的 PIN，而仅输入了 #。  如果用户被设置为标准模式且身份验证成功，这表示用户仅输入了 #，这在标准模式下是正确的做法。 |
| SUCCESS_WITH_PIN_BUT_TIMEOUT | 输入后未按 # | 由于未输入 #，因此用户未发送任何 DTMF 数字。  除非已输入 # 表明输入完成，否则不会发送已输入的其他数字。 |
|SUCCESS_NO_PIN_BUT_TIMEOUT | 无电话输入 - 已超时 | 呼叫已被接听，但是无人应答。  这通常表明呼叫由语音信箱接听。 |
| SUCCESS_PIN_EXPIRED | PIN 已到期且未更改 | 用户的 PIN 已到期且系统已提示用户更改 PIN，但他们未成功完成对 PIN 的更改。 |
| SUCCESS_USED_CACHE | 已用缓存 | 未进行多重身份验证调用即身份验证成功，因为在配置的缓存时间范围内，先前已对同一用户名进行过成功的身份验证。 |
| SUCCESS_BYPASSED_AUTH | 已免身份验证 | 已使用为用户发起的免验证一次成功进行身份验证。  有关免验证的详细信息，请参阅“免验证用户的历史记录”报告。 |
| SUCCESS_USED_IP_BASED_CACHE | 已用的基于 IP 的缓存 | 未进行多重身份验证调用即身份验证成功，因为在配置的缓存时间范围内，先前已对同一用户名、身份验证类型、应用程序名称和 IP 进行过成功的身份验证。 |
| SUCCESS_USED_APP_BASED_CACHE | 已用的基于应用的缓存 | 未进行多重身份验证调用即身份验证成功，因为在配置的缓存时间范围内，先前已对同一用户名、身份验证类型和应用程序名称进行过成功的身份验证。 |
| SUCCESS_INVALID_INPUT | 电话输入无效 | 通过电话发送的响应无效。  这可能是因为响应来自传真机或调制解调器，或者用户可能将 * 作为其 PIN 的一部分进行输入。 |
| SUCCESS_USER_BLOCKED | 用户被阻止 | 用户的电话号码被阻止。  被阻止的号码可以由用户在身份验证调用期间启动，也可以由管理员使用 Azure 门户启动。 <br> 注意：  欺诈警告还会导致号码被阻止。 |
| SUCCESS_SMS_AUTHENTICATED | 已通过短信进行身份验证 | 对于双向测试消息，用户正确地使用了其一次性密码 (OTP) 或 OTP + PIN 进行回复。 |
| SUCCESS_SMS_SENT | 已发送短信 | 对于短信，已成功发送包含一次性密码 (OTP) 的短信。  用户将在应用程序中输入 OTP 或 OTP + PIN 以完成身份验证。 |
| SUCCESS_PHONE_APP_AUTHENTICATED | 已通过移动应用进行身份验证 | 用户已成功通过移动应用进行身份验证。 |
| SUCCESS_OATH_CODE_PENDING | OATH 代码待处理 | 系统提示用户输入其 OATH 代码，但用户未做出响应。 |
| SUCCESS_OATH_CODE_VERIFIED | 已验证 OATH 代码 | 出现提示后，用户输入了有效的 OATH 代码。 |
| SUCCESS_FALLBACK_OATH_CODE_VERIFIED | 已验证回退 OATH 代码 | 用户被拒绝使用其主要多重身份验证方法进行身份验证，并向用户提供了有效的 OATH 代码以进行回退。 |
| SUCCESS_FALLBACK_SECURITY_QUESTIONS_ANSWERED | 已回答回退安全问题 | 用户被拒绝使用其主要多重身份验证方法进行身份验证，并正确回答了其安全问题以进行回退。 |
| FAILED_PHONE_BUSY | 身份验证已在进行 | 多重身份验证已在处理此用户的身份验证。  这通常是由 RADIUS 客户端导致的，在同一登录期间，这些客户端会发送多个身份验证请求。 |
| CONFIG_ISSUE | 无法接通电话 | 已尝试呼叫，但无法发出呼叫或未应答。  这包括忙信号、快忙信号（已断开连接）、三连音（号码已不在服务中）、振铃时超时等。 |
| FAILED_INVALID_PHONENUMBER | 电话号码格式无效 | 电话号码格式无效。  电话号码必须为数字，且对于国家/地区代码为 +1（美国及加拿大）的必须为 10 位数。 |
| FAILED_USER_HUNGUP_ON_US | 用户已挂断电话 | 用户接听了电话，但没有按任何按钮就挂断了电话。 |
| FAILED_INVALID_EXTENSION | 分机号无效 | 分机号包含无效字符。  只允许使用数字、逗号、* 和 #。  还可以使用 @ 前缀。 |
| FAILED_FRAUD_CODE_ENTERED | 已输入欺诈行为代码 | 用户在通话期间选择举报欺诈行为，导致身份验证被拒绝且电话号码被阻止。| 
| FAILED_SERVER_ERROR | 无法发出呼叫 | 多重身份验证服务无法发出呼叫。 |
| FAILED_SMS_NOT_SENT | 无法发送短信 | 无法发送短信。  身份验证被拒绝。 |
| FAILED_SMS_OTP_INCORRECT | 短信 OTP 不正确 | 用户从收到的短信中输入的一次性密码 (OTP) 不正确。  身份验证被拒绝。 |
| FAILED_SMS_OTP_PIN_INCORRECT | 短信 OTP + PIN 不正确 | 用户输入的一次性密码 (OTP) 和/或用户 PIN 不正确。  身份验证被拒绝。 |
| FAILED_SMS_MAX_OTP_RETRY_REACHED | 已超出短信 OTP 最大尝试次数 | 用户已超出一次性密码 (OTP) 最大尝试次数。 |
| FAILED_PHONE_APP_DENIED | 已拒绝移动应用 | 用户通过按“拒绝”按钮，拒绝了在移动应用中进行身份验证。 |
| FAILED_PHONE_APP_INVALID_PIN | 移动应用 PIN 无效 | 在移动应用中进行身份验证时，用户所输入的 PIN 无效。 |
| FAILED_PHONE_APP_PIN_NOT_CHANGED | 移动应用 PIN 未更改 | 用户未在移动应用中成功完成所需的 PIN 更改。 |
| FAILED_FRAUD_REPORTED | 已举报欺诈行为 | 用户已举报移动应用中的欺诈行为。 |
| FAILED_PHONE_APP_NO_RESPONSE | 移动应用没有响应 | 用户未对移动应用的身份验证请求做出响应。 |
| FAILED_PHONE_APP_ALL_DEVICES_BLOCKED | 已在所有设备上阻止移动应用 | 此用户的移动应用设备不再对通知做出响应且已被阻止。 |
| FAILED_PHONE_APP_NOTIFICATION_FAILED | 移动应用通知失败 | 尝试在用户的设备上向移动应用发送通知时失败。 |
| FAILED_PHONE_APP_INVALID_RESULT | 移动应用结果无效 | 移动应用返回的结果无效。 |
| FAILED_OATH_CODE_INCORRECT | OATH 代码不正确 | 用户输入的 OATH 代码不正确。  身份验证被拒绝。 |
| FAILED_OATH_CODE_PIN_INCORRECT | OATH 代码 + PIN 不正确 | 用户输入的 OATH 代码和/或用户 PIN 不正确。  身份验证被拒绝。 |
| FAILED_OATH_CODE_DUPLICATE | OATH 代码重复 | 用户输入了一个以前使用过的 OATH 代码。  身份验证被拒绝。 |
| FAILED_OATH_CODE_OLD | OATH 代码已过期 | 用户输入的 OATH 代码早于先前使用的 OATH 代码。  身份验证被拒绝。 |
| FAILED_OATH_TOKEN_TIMEOUT | OATH 代码结果超时 | 用户输入 OATH 代码所用的时间过长，多重身份验证尝试已超时。 |
| FAILED_SECURITY_QUESTIONS_TIMEOUT | 安全性问题结果超时 | 用户输入安全性问题答案所用的时间过长，多重身份验证尝试已超时。 |
| FAILED_AUTH_RESULT_TIMEOUT | 身份验证结果超时 | 用户完成多重身份验证尝试所用的时间过长。 |
| FAILED_AUTHENTICATION_THROTTLED | 已限制身份验证 | 服务已限制多重身份验证尝试。 |

## <a name="additional-mfa-reports"></a>其他 MFA 报告

以下其他信息和报告可用于 MFA 事件，包括可用于 MFA 服务器的信息和报告：

| 报表 | 位置 | 说明 |
|:--- |:--- |:--- |
| 阻止的用户历史记录 | Azure AD > 安全性 > MFA > 阻止/解除阻止用户 | 显示请求阻止或解除阻止用户的历史记录。 |
| 本地组件的使用情况 | Azure AD > 安全性 > MFA > 活动报告 | 提供有关通过 NPS 扩展、ADFS 和 MFA 服务器实现的 MFA 服务器的总体使用情况信息。 |
| 跳过的用户历史记录 | Azure AD > 安全性 > MFA > 免验证一次 | 提供 MFA 服务器请求历史记录，请求内容为跳过用户 MFA。 |
| 服务器状态 | Azure AD > 安全性 > MFA > 服务器状态 | 显示与帐户关联的 MFA 服务器的状态。 |

## <a name="next-steps"></a>后续步骤

本文提供了登录活动报告的概述。 有关此报告所包含内容以及了解数据的更多详细信息，请参阅 [Azure AD 中的登录活动报告](../reports-monitoring/concept-sign-ins.md)。
