---
title: Azure 门户中多重身份验证报告的参考 | Microsoft Docs
description: Azure 门户中多重身份验证报告的参考信息
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 03/16/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: e2d48eeaa98fd60841e2f25c09eb46bb848f78df
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36221890"
---
# <a name="reference-for-multi-factor-authentication-reporting-in-the-azure-portal"></a>Azure 门户中多重身份验证报告的参考

通过 [Azure 门户](https://portal.azure.com)中的 [Azure Active Directory (Azure AD) 报告](active-directory-reporting-azure-portal.md)，可以获取确定环境运行状况所需的信息。

[登录活动报告](active-directory-reporting-activity-sign-ins.md)提供托管应用程序的使用和用户登录活动方面的信息，其中包括多重身份验证 (MFA) 使用方面的信息。 

可以通过 MFA 数据了解 MFA 在组织中的使用情况。 可以通过它来了解以下情况： 

- 登录时是否进行了 MFA？ 

- 用户如何完成 MFA？ 

- 用户无法完成 MFA 的原因是什么？  

可以通过聚合所有登录数据来更好地了解组织中的 MFA 体验。 可以通过数据来了解以下情况： 

- 对多少用户进行了 MFA？  

- 无法完成 MFA 质询的用户有多少？ 

- 最终用户遇到的常见 MFA 问题有哪些？ 


此数据可通过 Azure 门户和[报告 API](active-directory-reporting-api-getting-started-azure-portal.md) 获取。 


## <a name="data-structure"></a>数据结构


可以通过 MFA 登录活动报告了解以下信息：

**需要 MFA：** 登录时是否需要 MFA。 进行单用户 MFA、条件访问或其他操作时，可能需要 MFA。 可能的值为 `Yes` 或 `No`。

**MFA 身份验证方法：** 用户用来完成 MFA 的身份验证方法。 可能的值包括： 

- 短信 

- 移动应用通知 

- 电话呼叫（身份验证电话） 

- 移动应用验证码 

- 电话呼叫（办公电话） 

- 电话呼叫（其他身份验证电话） 

**MFA 身份验证详细信息：** 清理版的电话号码，例如：+X XXXXXXXX64。 

**MFA 结果：** 有关 MFA 是通过还是未通过的详细信息：

- 如果 MFA 通过，则此列会提供 MFA 如何通过的详细信息。 

- 如果 MFA 未通过，则此列会提供未通过的原因。 可能的值为 `Satisfied` 或 `Denied`。 

以下部分列出了 MFA 结果字段可能的字符串值。

## <a name="status-strings"></a>状态字符串

此部分列出了 MFA 结果状态字符串的可能值。

### <a name="satisfied-status-strings"></a>“已通过”状态字符串


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

 
### <a name="denied-status-strings"></a>“未通过”状态字符串

Azure 多重身份验证未通过； 

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



## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅 [Azure Active Directory 报告](active-directory-reporting-azure-portal.md)。




























