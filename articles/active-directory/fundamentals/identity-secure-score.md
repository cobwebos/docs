---
title: 什么是安全标识的分数？ - Azure Active Directory
description: 如何使用安全的标识的分数来提高你的目录的安全状况
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: tilarso
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6cdff2305914ca6e4144f7784d1a60026a1d27c0
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2019
ms.locfileid: "65988697"
---
# <a name="what-is-the-identity-secure-score-in-azure-active-directory"></a>什么是 Azure Active Directory 中的标识安全评分？

你的 Azure AD 租户有多安全？ 如果不知道如何回答此问题，此文章介绍了如何标识安全分数可帮助你监视和提高您标识的安全状况。

## <a name="what-is-an-identity-secure-score"></a>标识安全评分是什么？

标识安全得分是介于 1 和充当你是 Microsoft 的最佳做法建议的安全如何对齐的指示器的 223 之间的数字。 标识安全分数中的每个改进操作适用于您的特定配置。  

![安全功能分数](./media/identity-secure-score/identity-secure-score-overview.png)

评分有助于：

- 客观衡量标识安全状况
- 规划标识安全改进
- 审查改进措施的成败

可以在安全评分仪表板上访问评分和标识相关的信息。 此仪表板提供：

- 标识安全分数
- 显示你的身份如何保护分数的比较图表将与其他租户中的相同的行业和相似的大小进行比较
- 显示标识安全分数如何随时间而变化的趋势图表
- 一系列可能的改进

遵循改进操作可以：

- 提高安全状况和您的分数
- 充分利用你的组织标识投资的一部分提供的功能

## <a name="how-do-i-get-my-secure-score"></a>如何获取安全评分？

标识安全分数是在所有版本的 Azure AD 中可用。 若要访问评分，请转到 [Azure AD 概述仪表板](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/IdentitySecureScore)。

## <a name="how-does-it-work"></a>它是如何工作的?

Azure 每隔 48 小时检查安全配置，并将你的设置与建议的最佳做法进行比较。 根据此评估的结果，为你的目录来计算新分数。 很可能你的安全配置不完全符合最佳实践指导，而且仅部分满足改进操作。 在这些情况下，您将只颁奖可用于该控件的最大分数的一部分。

每条建议是根据 Azure AD 配置来衡量的。 如果使用第三方产品来启用建议的最佳做法，你可以指示改进操作的此配置设置中。 您还可以选择设置建议，如果它们不应用于您的环境，则忽略。 评分计算不考虑已忽略的建议。

![忽略或将操作标记为受第三方](./media/identity-secure-score/identity-secure-score-ignore-or-third-party-reccomendations.png)

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

### <a name="how-are-controls-scored"></a>如何评分控件？

可以通过两种方式评分控件。 一些评分以二进制方式-如果有功能或配置的设置基于我们的建议获取评分的 100%。 其他分数进行计算的总配置一个百分比。 例如，如果你保护所有用户使用 MFA，且只能包含 100 个受保护的总用户 5 访问时，将获得 30 点所指出的改进建议，将为您提供部分分数大约 2 个点 (受保护的 5 / 100 总量 * 30 的最大 pts = 2 pts 部分分数).

### <a name="what-does-not-scored-mean"></a>[未评分] 是什么意思？

标记为 [未评分] 的操作是可以在组织中执行的，但由于尚未挂接到工具中，因而不会评分的操作。 因此，仍可以改进安全性，但这些操作不能马上带来积分。

### <a name="how-often-is-my-score-updated"></a>评分的更新频率如何？

评分每天计算一次（大约在太平洋标准时间 1:00 AM）。 如果对衡量的操作做了更改，评分将在下一日自动更新。 最长需要在 48 小时之后，更改才会反映在评分中。

### <a name="my-score-changed-how-do-i-figure-out-why"></a>我的评分已更改。 如何找出更改的原因？

转到[Microsoft 365 安全中心](https://security.microsoft.com/)，您将找到您完整的 Microsoft 安全分数。 可以查看历史记录选项卡上的深入更改，轻松安全分数中查看所有更改。

### <a name="does-the-secure-score-measure-my-risk-of-getting-breached"></a>安全分数度量值获取违例我的风险？

简单地说，不会。 安全分数中并不表示绝对度量值是以获取破坏的可能性。 它展示的信息仅限于采用的哪些功能可以缓解违规风险。 没有服务可以保证你将不侵犯，并安全分数不应当被解释为以任何方式保证。

### <a name="how-should-i-interpret-my-score"></a>如何解释评分？

将会提供有关配置建议安全功能或执行安全相关任务（例如读取报告）的要点。 某些操作是根据部分完成状态评分的，例如，为用户启用多重身份验证 (MFA)。 安全分数直接是代表你使用的 Microsoft 安全服务。 请记住，必须使用可用性平衡安全。 所有安全控制措施都有影响用户的一面。 用户影响较小的控制措施对用户日常操作的影响很小，甚至没有任何影响。

若要查看得分历史记录，请转到[Microsoft 365 安全中心](https://security.microsoft.com/)并查看您的总体 Microsoft 安全分数。 你可以查看更改到整体安全分数单击查看历史记录。 选择特定的日期以查看在该日期启用的控制措施，以及从每个控制措施赢得的积分。

### <a name="how-does-the-identity-secure-score-relate-to-the-office-365-secure-score"></a>标识安全评分与 Office 365 安全评分之间有什么关系？

[Microsoft 安全分数](https://docs.microsoft.com/office365/securitycompliance/microsoft-secure-score)包含五个不同的控件和分数类别：

- 标识
- 数据
- 设备
- 基础结构
- 应用程序

安全的标识的分数表示 Microsoft 安全分数的标识部分。 这种重叠意味着标识建议保护分数和 Microsoft 中的标识分数是相同的。

## <a name="next-steps"></a>后续步骤

[了解有关 Microsoft 安全分数的详细信息](https://docs.microsoft.com/office365/securitycompliance/microsoft-secure-score)
