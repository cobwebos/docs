---
title: 什么是身份安全分数？ - Azure Active Directory
description: 如何使用标识安全分数来改进目录的安全状态
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: tilarso
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f75dea2cffbe710bf2778ceab5eacc91ffcca9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523096"
---
# <a name="what-is-the-identity-secure-score-in-azure-active-directory"></a>什么是 Azure Active Directory 中的标识安全评分？

你的 Azure AD 租户有多安全？ 如果您不知道如何回答这个问题，本文将介绍身份安全分数如何帮助您监控和改进身份安全状态。

## <a name="what-is-an-identity-secure-score"></a>标识安全评分是什么？

身份安全分数介于 1 和 223 之间，作为您与 Microsoft 安全最佳实践建议的一致性的指示器。 身份安全分数中的每个改进操作都根据您的特定配置量身定制。  

![安全功能分数](./media/identity-secure-score/identity-secure-score-overview.png)

评分有助于：

- 客观衡量标识安全状况
- 规划标识安全改进
- 审查改进措施的成败

可以在安全评分仪表板上访问评分和标识相关的信息。 此仪表板提供：

- 您的身份安全分数
- 显示您的身份安全分数与同行业中其他租户和类似大小的比较图
- 显示您的身份安全分数随时间变化的趋势图
- 可能改进的列表

遵循改进操作可以：

- 改善您的安全姿势和分数
- 利用作为身份投资的一部分您的组织可用的功能

## <a name="how-do-i-get-my-secure-score"></a>如何获取安全评分？

标识安全分数在所有版本的 Azure AD 中都可用。 组织可以从**Azure 门户** > **Azure 活动目录** > **安全** > **标识安全分数**访问其标识安全分数。

## <a name="how-does-it-work"></a>工作原理

Azure 每隔 48 小时检查安全配置，并将你的设置与建议的最佳做法进行比较。 根据此评估的结果，为您的目录计算一个新分数。 您的安全配置可能未与最佳实践指南完全一致，并且改进操作仅部分满足。 在这些情况下，您只能获得控件可用的最大分数的一部分。

每条建议是根据 Azure AD 配置来衡量的。 如果使用第三方产品来启用最佳实践建议，则可以在改进操作的设置中指示此配置。 如果建议不适用于您的环境，您还可以选择设置要忽略的建议。 评分计算不考虑已忽略的建议。

![忽略或标记第三方涵盖的操作](./media/identity-secure-score/identity-secure-score-ignore-or-third-party-reccomendations.png)

## <a name="how-does-it-help-me"></a>评分有什么作用？

安全评分有助于：

- 客观衡量标识安全状况
- 规划标识安全改进
- 审查改进措施的成败

## <a name="what-you-should-know"></a>要点

### <a name="who-can-use-the-identity-secure-score"></a>谁可以使用标识安全评分？

以下角色可以使用标识安全评分：

- 全局管理员
- 安全管理员
- 安全读取者

### <a name="how-are-controls-scored"></a>如何对控件进行评分？

控件可通过两种方式评分。 有些是以二进制方式评分的 - 如果您根据我们的建议配置了功能或设置，您将获得 100% 的分数。 其他分数按总配置的百分比计算。 例如，如果改进建议声明，如果您使用 MFA 保护所有用户，并且仅保护了 100 个用户中的 5 个，您将获得大约 2 分的部分分数（5 个受保护 / 100 个总计 = 30 个最大点 = 2 点部分分数），您将获得 30 分。

### <a name="what-does-not-scored-mean"></a>[未评分] 是什么意思？

标记为 [未评分] 的操作是可以在组织中执行的，但由于尚未挂接到工具中，因而不会评分的操作。 因此，仍可以改进安全性，但这些操作不能马上带来积分。

### <a name="how-often-is-my-score-updated"></a>评分的更新频率如何？

评分每天计算一次（大约在太平洋标准时间 1:00 AM）。 如果对衡量的操作做了更改，评分将在下一日自动更新。 最长需要在 48 小时之后，更改才会反映在评分中。

### <a name="my-score-changed-how-do-i-figure-out-why"></a>我的评分已更改。 如何找出更改的原因？

前往[微软365安全中心](https://security.microsoft.com/)，在那里你会发现你的微软安全得分。 通过查看历史记录选项卡上的深入更改，可以轻松查看安全分数的所有更改。

### <a name="does-the-secure-score-measure-my-risk-of-getting-breached"></a>安全分数是否衡量我被攻破的风险？

简单地说，不会。 安全分数并不表示您被突破的可能性的绝对衡量。 它展示的信息仅限于采用的哪些功能可以缓解违规风险。 任何服务都无法保证您不会被违反，安全分数不应以任何方式被解释为保证。

### <a name="how-should-i-interpret-my-score"></a>如何解释评分？

将会提供有关配置建议安全功能或执行安全相关任务（例如读取报告）的要点。 某些操作是根据部分完成状态评分的，例如，为用户启用多重身份验证 (MFA)。 您的安全分数直接代表您使用的 Microsoft 安全服务。 请记住，安全性必须与可用性相平衡。 所有安全控制措施都有影响用户的一面。 用户影响较小的控制措施对用户日常操作的影响很小，甚至没有任何影响。

要查看您的分数历史记录，请前往 Microsoft [365 安全中心](https://security.microsoft.com/)并查看您的整体 Microsoft 安全分数。 您可以查看对整体安全分数的更改，单击"查看历史记录"。 选择特定的日期以查看在该日期启用的控制措施，以及从每个控制措施赢得的积分。

### <a name="how-does-the-identity-secure-score-relate-to-the-office-365-secure-score"></a>标识安全评分与 Office 365 安全评分之间有什么关系？

[Microsoft 安全分数](https://docs.microsoft.com/office365/securitycompliance/microsoft-secure-score)包含五个不同的控制和分数类别：

- 标识
- 数据
- 设备
- 基础结构
- 应用

标识安全分数表示 Microsoft 安全分数的标识部分。 此重叠意味着您对 Microsoft 中标识安全分数和身份分数的建议是相同的。

## <a name="next-steps"></a>后续步骤

[了解有关微软安全分数的更多](https://docs.microsoft.com/office365/securitycompliance/microsoft-secure-score)
