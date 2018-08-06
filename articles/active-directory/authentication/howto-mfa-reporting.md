---
title: 用于 Azure MFA 的访问和使用情况报告 | Microsoft Docs
description: 介绍如何使用 Azure 多重身份验证功能 - 报告。
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: bb5a005ba553d6392bf1427a4c2bba9ac5aad191
ms.sourcegitcommit: 99a6a439886568c7ff65b9f73245d96a80a26d68
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2018
ms.locfileid: "39358660"
---
# <a name="reports-in-azure-multi-factor-authentication"></a>Azure 多重身份验证中的报告

Azure 多重身份验证提供了几个可通过 Azure 门户访问的报告，供你和你的组织使用。 下表列出了可用的报告：

| 报表 | 位置 | Description |
|:--- |:--- |:--- |
| 阻止的用户历史记录 | Azure AD > MFA 服务器 > 阻止/解除阻止用户 | 显示请求阻止或解除阻止用户的历史记录。 |
| 使用情况和欺诈警报 | Azure AD > 登录 | 提供有关总体使用情况、用户摘要和用户详细信息的信息；以及指定日期范围内提交的欺诈警报的历史记录。 |
| 本地组件的使用情况 | Azure AD > MFA 服务器 > 活动报告 | 提供有关通过 NPS 扩展、ADFS 和 MFA 服务器实现的 MFA 的总体使用情况信息。 |
| 跳过的用户历史记录 | Azure AD > MFA 服务器 > 免验证一次 | 提供请求对用户跳过多重身份验证的历史记录。 |
| 服务器状态 | Azure AD > MFA 服务器 > 服务器状态 | 显示与帐户关联的多重身份验证服务器的状态。 |

## <a name="view-mfa-reports"></a>查看 MFA 报告

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在左侧选择“Azure Active Directory” > “MFA 服务器”。
3. 选择要查看的报告。

   <center>![云](./media/howto-mfa-reporting/report.png)</center>

## <a name="azure-ad-sign-ins-report"></a>Azure AD 登录报告

通过 [Azure 门户](https://portal.azure.com)中的**登录活动报告**，可以获取确定环境运行状况所需的信息。

登录报告可以提供托管应用程序的使用和用户登录活动方面的信息，其中包括多重身份验证 (MFA) 使用方面的信息。 可以通过 MFA 数据了解 MFA 在组织中的使用情况。 可以通过它来了解以下情况：

- 登录时是否进行了 MFA？
- 用户如何完成 MFA？
- 用户无法完成 MFA 的原因是什么？
- 对多少用户进行了 MFA？
- 无法完成 MFA 质询的用户有多少？
- 最终用户遇到的常见 MFA 问题有哪些？

此数据可通过 [Azure 门户](https://portal.azure.com)和[报告 API](../active-directory-reporting-api-getting-started-azure-portal.md) 获取。

![云](./media/howto-mfa-reporting/sign-in-report.png)

### <a name="sign-ins-report-structure"></a>登录报告结构

可以通过 MFA 登录活动报告了解以下信息：

**需要 MFA：** 登录时是否需要 MFA。 由于每用户 MFA、条件访问或其他原因，可能需要 MFA。 可能的值为“是”或“否”。

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

**条件访问** 查找有关影响了登录尝试的条件访问策略的信息，包括：

- 策略名称
- 授权控制
- 会话控制
- 结果

## <a name="powershell-reporting"></a>PowerShell 报告

使用后面的 PowerShell 标识已注册 MFA 的用户。

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName```

使用后面的 PowerShell 标识未注册 MFA 的用户。

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName```

## <a name="next-steps"></a>后续步骤

* [面向用户](../user-help/multi-factor-authentication-end-user.md)
* [部署位置](concept-mfa-whichversion.md)
