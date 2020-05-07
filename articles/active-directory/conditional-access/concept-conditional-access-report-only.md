---
title: 什么是条件访问仅限报告模式？ - Azure Active Directory
description: 仅限报告模式对条件访问策略部署有何帮助
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/01/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c5643b0ce9d93c28372b30e6fc114816cafbce6
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82690476"
---
# <a name="what-is-conditional-access-report-only-mode"></a>什么是条件访问仅限报告模式？

在适当的情况下，我们的客户广泛使用条件访问，通过应用适当的访问控制来确保安全。 但是，在组织中部署条件访问策略时，面临的难题之一是确定对最终用户的影响。 可能很难预测受到常见部署计划（例如，阻止旧版身份验证、需要对用户群进行多重身份验证或实现登录风险策略）影响的用户的数量和名称。 

仅限报告模式是一种新的条件访问策略状态，可使管理员能够在其环境中启用条件访问策略之前对其影响进行评估。  随着仅限报告模式的发布：

- 可以在仅限报告模式下启用条件访问策略。
- 在登录过程中，将评估仅限报告模式下的策略，但不强制执行这些策略。
- 结果记录在登录日志详细信息的 "**条件访问**" 和 "**仅报告**" 选项卡中。
- 具有 Azure Monitor 订阅的客户可以使用条件访问见解工作簿来监视其条件访问策略的影响。

> [!WARNING]
> 仅限报告模式下要求符合设备的策略在策略评估过程中可能会提示用户在 Mac、iOS 和 Android 上选择设备证书，即使未强制执行设备合规性。 在使设备符合要求之前，这些提示可能会重复出现。 若要防止最终用户在登录过程中收到提示，请从执行设备合规性检查的仅限报告策略中排除设备平台 Mac、iOS 和 Android。

![Azure AD 登录日志中的“仅限报告”选项卡](./media/concept-conditional-access-report-only/report-only-detail-in-sign-in-log.png)

## <a name="policy-results"></a>策略结果

如果针对给定登录评估仅限报告模式下的策略，则有四个新的可能结果值：

| 结果 | 说明 |
| --- | --- |
| 仅报告：成功 | 满足所有配置的策略条件、所需的非交互式授权控制和会话控制。 例如，通过令牌中已经存在的 MFA 声明来满足多重身份验证要求，或者通过在符合的设备上执行设备检查来满足符合设备策略。 |
| 仅报告：失败 | 满足所有配置的策略条件，但并未满足所有所需的非交互式授权控制或会话控制。 例如，策略适用于配置了阻止控制的用户，或者未通过符合设备策略的设备。 |
| 仅限报表：所需的用户操作 | 满足所有配置的策略条件，但需要用户操作来满足所需的授权控制或会话控制。 对于仅限报告模式，系统不会提示用户满足所需的控制。 例如，系统不会提示用户提供多重身份验证质询或使用条款。   |
| 仅报告：未应用 | 并非满足所有配置的策略条件。 例如，从策略中排除用户或策略仅适用于某些受信任的命名位置。 |

## <a name="conditional-access-insights-workbook"></a>条件访问见解工作簿

管理员可以在仅限报告模式下创建多个策略，因此有必要了解每个策略的单独影响，以及对多个策略进行评估的综合影响。 新的条件访问见解工作簿使管理员能够可视化条件访问查询，并针对某个给定时间范围、一组应用程序和用户监视策略的影响。 
 
## <a name="next-steps"></a>后续步骤

[在条件性访问策略中配置仅报告模式](howto-conditional-access-report-only.md)
