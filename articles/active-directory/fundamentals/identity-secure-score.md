---
title: Azure AD 中的标识安全评分是什么？ - 预览版 | Microsoft Docs
description: 了解如何使用标识安全评分来改进 Azure AD 租户的安全状况。
services: active-directory
keywords: 标识安全评分, Azure AD, 公司资源的安全访问
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: fundamentals
ms.topic: overview
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/19/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: a9bb22cae2e9fcd607a81d442f4fb51f12c43454
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2018
ms.locfileid: "47224131"
---
# <a name="what-is-the-identity-secure-score-in-azure-ad---preview"></a>Azure AD 中的标识安全评分是什么？ - 预览版

你的 Azure AD 租户有多安全？ 如果不知道此问题的答案，请阅读本文，了解标识安全评分如何帮助你监视和改进标识安全状况。 

## <a name="what-is-an-identity-secure-score"></a>标识安全评分是什么？

标识安全评分是介于 1 和 248 之间的数字，反映你在 Microsoft 建议的安全最佳做法方面的遵守程度。


![安全评分](./media/identity-secure-score/01.png)



评分有助于：

- 客观衡量标识安全状况

- 规划标识安全改进

- 审查改进措施的成败 


可以在安全评分仪表板上访问评分和标识相关的信息。 此仪表板提供：

- 评分

    ![安全评分](./media/identity-secure-score/02.png)

- 比较图

    ![安全评分](./media/identity-secure-score/03.png)

- 趋势图

    ![安全评分](./media/identity-secure-score/04.png)

- 标识安全最佳做法列表。 

    ![安全评分](./media/identity-secure-score/05.png)


遵循改进操作可以：

- 改进安全状况和评分。
 
- 利用 Microsoft 的标识功能。 



## <a name="how-do-i-get-my-secure-score"></a>如何获取安全评分？

所有 Azure AD 版本都提供标识安全评分。

若要访问评分，请转到 [Azure AD 概述仪表板](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/IdentitySecureScore)。



## <a name="how-does-it-work"></a>工作原理

Azure 每隔 48 小时检查安全配置，并将你的设置与建议的最佳做法进行比较。 然后，根据此项评估的结果为租户计算新的评分。 这意味着，最长可能需要在 48 小时之后，所做的配置更改才能反映在评分中。 

每条建议是根据 Azure AD 配置来衡量的。 如果使用第三方产品启用建议的最佳做法，可以在改进措施的设置中指明这一点。

![安全评分](./media/identity-secure-score/07.png)


此外，还可以选择设置为当建议不适用于你的环境时将其忽略。 评分计算不考虑已忽略的建议。 
 
![安全评分](./media/identity-secure-score/06.png)



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

### <a name="what-does-not-scored-mean"></a>[未评分] 是什么意思？

标记为 [未评分] 的操作是可以在组织中执行的，但由于尚未挂接到工具中，因而不会评分的操作。 因此，仍可以改进安全性，但这些操作不能马上带来积分。

### <a name="how-often-is-my-score-updated"></a>评分的更新频率如何？

评分每天计算一次（大约在太平洋标准时间 1:00 AM）。 如果对衡量的操作做了更改，评分将在下一日自动更新。 最长需要在 48 小时之后，更改才会反映在评分中。


### <a name="my-score-changed-how-do-i-figure-out-why"></a>我的评分已更改。 如何找出更改的原因？

在[安全评分门户](https://securescore.microsoft.com/#!/score)中的评分分析器页上，单击特定日期的数据点，然后向下滚动查看该日期的已完成和未完成操作，即可找出已更改的内容。

### <a name="does-the-secure-score-measure-my-risk-of-getting-breached"></a>安全评分是否衡量违规的风险？

简单地说，不会。 安全评分并不会表明违规可能性的绝对衡量结果。 它展示的信息仅限于采用的哪些功能可以缓解违规风险。 没有任何服务可以保证你不会违规，不应以任何方式将安全评分解释为一项保证。

### <a name="how-should-i-interpret-my-score"></a>如何解释评分？

将会提供有关配置建议安全功能或执行安全相关任务（例如读取报告）的要点。 某些操作是根据部分完成状态评分的，例如，为用户启用多重身份验证 (MFA)。 安全评分直接代表了所用的 Microsoft 安全服务。 请记住，安全性与可用性之间应始终保持平衡。 所有安全控制措施都有影响用户的一面。 用户影响较小的控制措施对用户日常操作的影响很小，甚至没有任何影响。

若要查看评分历史记录，请在[安全评分门户](https://securescore.microsoft.com/#!/score)中转到评分分析器页。 选择特定的日期以查看在该日期启用的控制措施，以及从每个控制措施赢得的积分。


### <a name="how-does-the-identity-secure-score-relate-to-the-office-365-secure-score"></a>标识安全评分与 Office 365 安全评分之间有什么关系？ 

[Office 365 安全评分](https://docs.microsoft.com/office365/securitycompliance/office-365-secure-score)即将迁移到五个不同评分的聚合：

- 标识

- 数据

- 设备

- 基础结构

- 应用

标识安全评分代表 Office 365 安全评分的标识部分。 这意味着，针对标识安全评分与 Office 365 中标识评分提供的建议相同。 


## <a name="next-steps"></a>后续步骤

若要观看有关 Office 365 安全评分的视频，请单击[此处](https://www.youtube.com/watch?v=jzfpDJ9Kg-A)。
 
