---
title: Azure Active Directory 标识保护是什么？
description: 通过 Azure AD 标识保护检测、修正、调查和分析风险
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: overview
ms.date: 08/24/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.custom: contperfq1
ms.collection: M365-identity-device-management
ms.openlocfilehash: ffe7f58f27d028dd114f548521feeeb74bb12cf1
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2020
ms.locfileid: "89437694"
---
# <a name="what-is-identity-protection"></a>什么是“标识保护”？

标识保护是一种使组织能够完成三项关键任务的工具：

- 自动检测和修正基于标识的风险。
- 使用门户中的数据调查风险。
- 将风险检测数据导出到第三方实用程序，供进一步分析。

标识保护利用 Microsoft 从 Azure AD 组织、Microsoft 帐户中的用户群以及 Xbox 游戏中获得的自身经验来保护用户。 Microsoft 每天分析 6.5 万亿条信号，以识别威胁并保护客户安全。

可以将由标识保护生成并发送到标识保护的信号进一步发送给条件访问等工具，供其制定访问决策，也可以将信号发送回安全信息和事件管理 (SIEM) 工具，以根据组织的强制执行策略进行深入调查。

## <a name="why-is-automation-important"></a>为什么自动化非常重要？

Microsoft 标识安全和保护团队的负责人 Alex Weinert 在他[于 2018 年 10 月发布的博客文章](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Eight-essentials-for-hybrid-identity-3-Securing-your-identity/ba-p/275843)中解释了为什么在处理大量事件时自动化如此重要：

> 机器学习和启发式系统每天为超过 8 亿个不同帐户进行的 180 亿次登录尝试提供风险评分，其中 3 亿次登录尝试看得出是由攻击者（例如，犯罪分子、黑客等实体）完成的。
>
> 去年在 Ignite 上，我谈到了针对我们标识系统的三大主要攻击。 以下是关于这些攻击的近期数据
>   
>   - **安全漏洞重播**：2018 年 5 月检测到 46 亿次攻击
>   - **密码喷射**：2018 年 4 月为 35 万次
>   - **钓鱼**：这种攻击很难准确量化，但我们在 2018 年 3 月发现了 2300 万个风险事件，其中大多数风险事件都与钓鱼相关

## <a name="risk-detection-and-remediation"></a>风险检测和修正

标识保护按照以下类别标识风险：

| 风险检测类型 | 说明 |
| --- | --- |
| 异常位置登录 | 从异常位置（基于用户最近的登录）进行登录。 |
| 匿名 IP 地址 | 从匿名 IP 地址登录（例如：Tor 浏览器，匿名程序 VPN）。 |
| 不熟悉的登录属性 | 使用给定用户最近未曾出现过的属性进行登录。 |
| 受恶意软件感染的 IP 地址 | 从受恶意软件感染的 IP 地址进行登录。 |
| 凭据泄漏 | 此风险检测指示用户的有效凭据已泄漏。 |
| 密码喷射 | 表示使用常见密码以统一的暴力攻击方式攻击了多个用户名。 |
| Azure AD 威胁智能 | Microsoft 的内部和外部威胁智能源已识别出已知的攻击模式。 |

要详细了解这些风险以及如何/何时计算，请参阅[什么是风险](concept-identity-protection-risks.md)一文。

风险信号可能会触发修正措施，例如要求用户执行以下操作：执行 Azure 多重身份验证、使用自助式密码重置来重置其密码或阻止操作直到管理员执行操作。

## <a name="risk-investigation"></a>风险调查

管理员可以查看检测结果，并按需对其执行手动操作。 管理员可以使用以下三项主要报告在标识保护中进行调查：

- 有风险用户
- 有风险的登录
- 风险检测

有关详细信息，请参阅[如何：调查风险](howto-identity-protection-investigate-risk.md)一文。

### <a name="risk-levels"></a>风险级别

标识保护将风险分为三个级别：低、中和高。 

虽然 Microsoft 不提供有关风险计算方式的具体详细信息，但可以说每个级别在用户或登录受到攻击时都提供了更高的置信度。 例如，与某用户的一个实例出现不熟悉的登录属性相比，将凭据泄露给另一个用户所带来的威胁性更高。

## <a name="exporting-risk-data"></a>导出风险数据

来自标识保护的数据可以导出到其他工具，以进行存档以及深入调查和相关性分析。 使用基于 API 的 Microsoft Graph，组织可以收集这些数据，以便在 SIEM 等工具中进一步处理。 若要了解如何访问标识保护 API，请参阅 [Azure Active Directory 标识保护和 Microsoft Graph 入门](howto-identity-protection-graph-api.md)一文

要了解如何将标识保护信息与 Azure Sentinel 集成，请参阅[从 Azure AD 标识保护连接数据](../../sentinel/connect-azure-ad-identity-protection.md)一文。

## <a name="permissions"></a>权限

标识保护要求用户是安全读者、安全操作员、安全管理员、全局读者或全局管理员才可访问。

| 角色 | 有权执行的操作 | 无权执行的操作 |
| --- | --- | --- |
| 全局管理员 | 对“标识保护”具有完全访问权限 |   |
| 安全管理员 | 对“标识保护”具有完全访问权限 | 重置用户密码 |
| 安全操作员 | 查看所有标识保护报表和“概览”边栏选项卡 <br><br> 消除用户风险，确认安全登录，确认泄露 | 配置或更改策略 <br><br> 重置用户密码 <br><br> 配置警报 |
| 安全读取者 | 查看所有标识保护报表和“概览”边栏选项卡 | 配置或更改策略 <br><br> 重置用户密码 <br><br> 配置警报 <br><br> 提供有关检测的反馈 |

目前，安全操作员角色无法访问风险登录报告。

条件访问管理员还可以创建将登录风险作为条件考虑进去的策略。有关详细信息，请参阅[条件访问：条件](../conditional-access/concept-conditional-access-conditions.md#sign-in-risk)一文。

## <a name="license-requirements"></a>许可要求

[!INCLUDE [Active Directory P2 license](../../../includes/active-directory-p2-license.md)]

| 功能 | 详细信息  | Azure AD Free/Office 365 应用 | Azure AD Premium P1|Azure AD Premium P2 |
| --- | --- | --- | --- | --- |
| 风险策略 | 用户风险策略（通过标识保护实现）  | 否 | 否 |是 | 
| 风险策略 | 登录风险策略（通过标识保护或条件访问实现）  | 否 |  否 |是 |
| 安全报表 | 概述 |  否 | 否 |是 |
| 安全报表 | 有风险用户  | 有限信息 | 有限信息 | 完全访问权限|
| 安全报表 | 有风险的登录  | 有限信息 | 有限信息 | 完全访问权限|
| 安全报表 | 风险检测   | 否 | 有限信息| 完全访问权限|
| 通知 | 检测到用户存在风险的警报  | 否 | 否 |是 |
| 通知 | 每周摘要| 否 | 否 | 是 | 
| | MFA 注册策略 | 否 | 否 | 是 |

## <a name="next-steps"></a>后续步骤

- [安全性概述](concept-identity-protection-security-overview.md)

- [什么是风险](concept-identity-protection-risks.md)

- [可用于规避风险的策略](concept-identity-protection-policies.md)
