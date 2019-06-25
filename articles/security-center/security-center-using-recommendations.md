---
title: 使用 Azure 安全中心建议增强安全性 | Microsoft 文档
description: " 了解如何使用 Azure 安全中心内的安全策略和建议来帮助减少安全攻击。 "
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/26/2019
ms.author: v-mohabe
ms.openlocfilehash: 60bb1c3b81ef990993a2ce659a2b189c9d8a0eba
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65967969"
---
# <a name="use-azure-security-center-recommendations-to-enhance-security"></a>使用 Azure 安全中心建议增强安全性
可以通过配置安全策略，并实施 Azure 安全中心提供的建议，降低发生重大安全事件的可能性。 本文说明了如何使用安全中心内的安全策略和建议来帮助减少安全攻击。 

安全中心自动运行连续的扫描来分析你的 Azure 资源的安全状态。 在安全中心识别潜在的安全漏洞时，它会创建一些建议，这些建议会指导完成配置所需安全控件的过程。 安全中心更新其建议在 24 小时内，但存在以下例外：

- 在 48 小时内更新操作系统的安全配置建议
- 在 8 小时内更新终结点保护问题建议

## <a name="scenario"></a>场景
此方案显示如何通过监视安全中心建议并采取措施，来使用安全中心帮助降低发生安全事件的可能性。 本方案使用虚构的公司 Contoso，以及安全中心[规划和操作指南](security-center-planning-and-operations-guide.md#security-roles-and-access-controls)中提供的角色。 在此方案中，我们将着重探讨以下角色：

![方案角色](./media/security-center-using-recommendations/scenario-roles.png)

Contoso 最近将其某些本地资源迁移到了 Azure。 Contoso 希望保护其资源并减少其云中资源的漏洞。

## <a name="use-azure-security-center"></a>使用 Azure 安全中心
Contoso 的 IT 安全部门的 David 已选择将 Contoso 订阅上的安全中心载入 Azure 安全中心，以预防和检测安全漏洞。 

安全中心自动分析 Contoso 的 Azure 资源的安全状态，并应用默认安全策略。 安全中心识别到潜在的安全漏洞时，会基于安全策略中设置的控件创建**建议**。 

David 在其所有订阅中运行 Azure 安全标准层，以获取可用的建议和安全功能的完整套件。 Jeff 也将所有尚未迁移的现有本地服务器载入云，以便他可以跨 [Windows](quick-onboard-windows-computer.md) 和 [Linux](quick-onboard-linux-computer.md) 服务器利用安全中心的混合支持。

Jeff 是云工作负荷所有者。 Jeff 负责根据 Contoso 的安全策略应用安全控件。 

Jeff 执行以下任务：

- 监视安全中心提供的安全建议
- 评估安全建议，并决定他是应当应用还是取消
- 应用安全建议

### <a name="remediate-threats-using-recommendations"></a>使用建议修正威胁
作为每日监视活动的一部分，Jeff 登录 Azure 并打开安全中心。 

1. Jeff 选择其工作负荷的订阅。

2. Jeff 查看自己的安全功能分数，以全面了解订阅的安全状态，他看到分数是 548  。

3. Jeff 必须决定要首先处理哪些建议。 因此，Jeff 单击安全功能分数并开始根据其提高[安全功能分数影响](security-center-secure-score.md)的程度来处理建议。

4. 由于 Jeff 有大量连接的 VM 和服务器，因此他决定关注“计算和应用”  。

5. 当 Jeff 单击“计算和应用”时，他会看到建议列表，并根据安全功能分数影响处理这些建议  。

6. Jeff 拥有许多面向 VM 的 Internet，并且由于其端口是公开的，所以他担心攻击者可能控制服务器。 因此 Jeff 选择使用（实时 VM 访问）[security-center-just-in-time.md]  。

Jeff 继续浏览高优先级和中优先级建议，并进行有关实施的决策。 对于每项建议，Jeff 会查看安全中心提供的详细信息，以了解受影响的资源、安全功能分数的影响、每项建议的含义以及缓解每个问题的修正步骤。

## <a name="conclusion"></a>结束语
监视安全中心内的建议有助于在发生攻击之前消除安全漏洞。 修正建议时，安全功能分数和工作负荷的安全状况会提高。 安全中心自动发现部署的新资源，根据安全策略对其进行评估，并提供新的建议对其进行保护。


## <a name="next-steps"></a>后续步骤
确保拥有一个适当的监视进程，定期检查安全中心中的建议，以便可以确保在一段时间内保护资源的安全。

此方案说明了如何使用安全中心内的安全策略和建议来帮助减少安全攻击。 请参阅[事件响应方案](security-center-incident-response.md)，了解如何在攻击发生之前制定事件响应计划。

了解如何通过[事件响应](security-center-incident-response.md)来响应威胁。
