---
title: 什么是条件访问仅报告模式？ -Azure Active Directory
description: 如何使用条件性访问策略部署的报表模式帮助
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75880f4c533a503852d62ff940e53d4bcc30d218
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2020
ms.locfileid: "77186116"
---
# <a name="what-is-conditional-access-report-only-mode"></a>什么是条件访问仅报告模式？

在适当的情况下，我们的客户广泛使用条件性访问，通过应用适当的访问控制来保持安全。 但是，在组织中部署条件性访问策略时，面临的难题之一是确定对最终用户的影响。 可能很难预测常见部署计划所影响的用户的数量和名称，如阻止旧身份验证、需要对用户进行多重身份验证或实现登录风险策略。 

仅限报表模式是一种新的条件性访问策略状态，它使管理员能够在其环境中启用条件访问策略之前对其影响进行评估。  在发布仅限报表模式时：

- 可以在仅限报表模式下启用条件访问策略。
- 在登录过程中，将计算仅限报表模式下的策略，但不执行这些策略。
- 结果记录在登录日志详细信息的 "**条件访问**" 和 "**仅报告（预览）** " 选项卡中。
- 具有 Azure Monitor 订阅的客户可以使用条件性访问见解工作簿来监视其条件访问策略的影响。

> [!WARNING]
> 仅限报表模式下需要相容设备的策略可能会提示用户在 Mac、iOS 和 Android 上选择设备证书，即使未实施设备符合性。 在使设备符合要求之前，这些提示可能会重复。 若要防止最终用户在登录过程中收到提示，请从执行设备相容性检查的仅报告策略中排除设备平台 Mac、iOS 和 Android。

![Azure AD 登录日志中的报表选项卡](./media/concept-conditional-access-report-only/report-only-detail-in-sign-in-log.png)

## <a name="policy-results"></a>策略结果

如果针对给定登录计算仅限报表模式下的策略，则有四个新的可能结果值：

| 结果 | 说明 |
| --- | --- |
| 仅报告：成功 | 满足所有已配置的策略条件、所需的非交互式授权控制和会话控制。 例如，多重身份验证要求由令牌中已经存在的 MFA 声明满足，或者通过在兼容的设备上执行设备检查来满足合规的设备策略。 |
| 仅报告：失败 | 满足所有已配置的策略条件，但未满足所有必需的非交互式授予控件或会话控制。 例如，策略适用于配置了 block 控件的用户，或者设备未通过合规的设备策略。 |
| 仅限报表：所需的用户操作 | 满足所有已配置的策略条件，但需要执行用户操作来满足所需的授权控制或会话控制。 对于报表模式，系统不会提示用户满足所需的控件。 例如，不会提示用户提供多重身份验证质询或使用条款。   |
| 仅报告：未应用 | 不是所有已配置的策略条件都得到满足。 例如，从策略中排除用户或策略仅适用于某些受信任的命名位置。 |

## <a name="conditional-access-insights-workbook"></a>条件性访问见解工作簿

管理员可以在仅报告模式下创建多个策略，因此，有必要了解每个策略的各个影响，以及对多个策略进行评估的组合影响。 新的条件性访问见解工作簿使管理员能够直观显示条件性访问查询，并监视某个给定时间范围、一组应用程序和用户的策略的影响。 
 
## <a name="next-steps"></a>后续步骤

[在条件性访问策略中配置仅报告模式](howto-conditional-access-report-only.md)
