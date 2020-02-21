---
title: 什么是身份安全分数？ -Azure Active Directory
description: 如何使用标识安全分数来改善目录的安全状况
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
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2020
ms.locfileid: "77523096"
---
# <a name="what-is-the-identity-secure-score-in-azure-active-directory"></a>什么是 Azure Active Directory 中的标识安全评分？

你的 Azure AD 租户有多安全？ 如果您不知道如何回答此问题，本文将介绍标识安全分数如何帮助您监视和提高标识安全状况。

## <a name="what-is-an-identity-secure-score"></a>标识安全评分是什么？

标识安全分数是1到223之间的数字，其作用就是使用 Microsoft 的最佳实践建议进行安全调整的指示。 标识安全分数中的每个改进操作都针对您的特定配置进行定制。  

![安全功能分数](./media/identity-secure-score/identity-secure-score-overview.png)

评分有助于：

- 客观衡量标识安全状况
- 规划标识安全改进
- 审查改进措施的成败

可以在安全评分仪表板上访问评分和标识相关的信息。 此仪表板提供：

- 标识安全分数
- 显示标识安全分数如何与相同行业和相似大小的其他租户进行比较的比较图
- 显示标识安全分数如何随时间变化的趋势图
- 可能改进的列表

遵循改进操作可以：

- 提高安全状况和评分
- 利用你的组织提供的功能作为标识投资的一部分

## <a name="how-do-i-get-my-secure-score"></a>如何获取安全评分？

所有版本的 Azure AD 都提供标识安全分数。 组织可以从**Azure 门户** > **Azure Active Directory** > **安全** > **标识安全分数**中访问其标识安全分数。

## <a name="how-does-it-work"></a>工作原理

Azure 每隔 48 小时检查安全配置，并将你的设置与建议的最佳做法进行比较。 根据此评估结果，将为你的目录计算一个新分数。 您的安全配置可能并不完全符合最佳实践指导原则，而且改进操作仅部分得到满足。 在这些情况下，你只会获得可用于控件的最大分数部分。

每条建议是根据 Azure AD 配置来衡量的。 如果使用第三方产品来启用最佳做法建议，则可在 "改进" 操作的设置中指示此配置。 你还可以选择将建议设置为在不适用于你的环境时将其忽略。 评分计算不考虑已忽略的建议。

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

### <a name="how-are-controls-scored"></a>控件的评分方式是什么？

可以通过两种方式对控件进行评分。 某些会按二进制方式评分-如果你已根据我们的建议配置了功能或设置，则会获得100% 的分数。 其他分数以总配置的百分比计算。 例如，如果您在使用 MFA 保护所有用户时获得30个点，并且仅保护了5个100的用户，则您将获得2个分数的部分分数（5个受保护的/100 总计 * 30 最大点数 = 2 pt 部分分数）。

### <a name="what-does-not-scored-mean"></a>[未评分] 是什么意思？

标记为 [未评分] 的操作是可以在组织中执行的，但由于尚未挂接到工具中，因而不会评分的操作。 因此，仍可以改进安全性，但这些操作不能马上带来积分。

### <a name="how-often-is-my-score-updated"></a>评分的更新频率如何？

评分每天计算一次（大约在太平洋标准时间 1:00 AM）。 如果对衡量的操作做了更改，评分将在下一日自动更新。 最长需要在 48 小时之后，更改才会反映在评分中。

### <a name="my-score-changed-how-do-i-figure-out-why"></a>我的评分已更改。 如何找出更改的原因？

转到[Microsoft 365 安全中心](https://security.microsoft.com/)，你可以在其中找到完整的 Microsoft 安全分数。 通过查看 "历史记录" 选项卡上的详细更改，可以轻松地查看安全分数的所有更改。

### <a name="does-the-secure-score-measure-my-risk-of-getting-breached"></a>安全分数是否会衡量遭受违例的风险？

简单地说，不会。 安全分数并不表示可能会受到破坏的可能性的绝对度量。 它展示的信息仅限于采用的哪些功能可以缓解违规风险。 无服务可以保证您不会被破坏，安全分数不应以任何方式解释为保证。

### <a name="how-should-i-interpret-my-score"></a>如何解释评分？

将会提供有关配置建议安全功能或执行安全相关任务（例如读取报告）的要点。 某些操作是根据部分完成状态评分的，例如，为用户启用多重身份验证 (MFA)。 你的安全分数直接代表你使用的 Microsoft 安全服务。 请记住，安全性必须与可用性进行平衡。 所有安全控制措施都有影响用户的一面。 用户影响较小的控制措施对用户日常操作的影响很小，甚至没有任何影响。

若要查看分数历史记录，请转到[Microsoft 365 安全中心](https://security.microsoft.com/)，并查看你的总体 Microsoft 安全分数。 您可以查看对整体安全分数的更改单击查看历史记录。 选择特定的日期以查看在该日期启用的控制措施，以及从每个控制措施赢得的积分。

### <a name="how-does-the-identity-secure-score-relate-to-the-office-365-secure-score"></a>标识安全评分与 Office 365 安全评分之间有什么关系？

[Microsoft 安全评分](https://docs.microsoft.com/office365/securitycompliance/microsoft-secure-score)包含五个不同的控制和评分类别：

- 标识
- data
- 设备
- 基础结构
- 应用

标识安全分数表示 Microsoft 安全分数的标识部分。 这种重叠意味着对于标识安全分数和 Microsoft 的标识评分的建议是相同的。

## <a name="next-steps"></a>后续步骤

[了解有关 Microsoft 安全分数的详细信息](https://docs.microsoft.com/office365/securitycompliance/microsoft-secure-score)
