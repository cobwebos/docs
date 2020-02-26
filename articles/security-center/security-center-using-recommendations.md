---
title: 使用 Azure 安全中心建议增强安全性 | Microsoft 文档
description: " 了解如何使用 Azure 安全中心内的安全策略和建议来帮助减少安全攻击。 "
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2019
ms.author: memildin
ms.openlocfilehash: a1034eb47010da2b0e795ee8c79646f06151cac1
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77603284"
---
# <a name="use-azure-security-center-recommendations-to-enhance-security"></a>使用 Azure 安全中心建议增强安全性
可以通过配置安全策略，并实施 Azure 安全中心提供的建议，降低发生重大安全事件的可能性。 本文说明了如何使用安全中心内的安全策略和建议来帮助减少安全攻击。 

安全中心会自动运行连续扫描，以分析 Azure 资源的安全状态。 在安全中心识别潜在的安全漏洞时，它会创建一些建议，这些建议会指导完成配置所需安全控件的过程。 安全中心会在24小时内更新其建议，但以下情况例外：

- 操作系统安全配置建议在48小时内更新
- Endpoint Protection 问题建议在8小时内更新

## <a name="scenario"></a>场景
此方案显示如何通过监视安全中心建议并采取措施，来使用安全中心帮助降低发生安全事件的可能性。 本方案使用虚构的公司 Contoso，以及安全中心[规划和操作指南](security-center-planning-and-operations-guide.md#security-roles-and-access-controls)中提供的角色。 在此方案中，我们将着重探讨以下角色：

![方案角色](./media/security-center-using-recommendations/scenario-roles.png)

Contoso 最近将其某些本地资源迁移到了 Azure。 Contoso 希望保护其资源并减少其云中资源的漏洞。

## <a name="use-azure-security-center"></a>使用 Azure 安全中心
Contoso 的 IT 安全部门的 David 已选择将 Contoso 订阅上的安全中心载入 Azure 安全中心，以预防和检测安全漏洞。 

安全中心自动分析 Contoso 的 Azure 资源的安全状态，并应用默认安全策略。 安全中心识别到潜在的安全漏洞时，会基于安全策略中设置的控件创建**建议**。 

David 跨所有订阅运行 Azure 安全标准层，以获取完整的一套建议和安全功能。 Jeff 还加入所有尚未迁移到云的本地服务器，使其能够利用安全中心跨其[Windows](quick-onboard-windows-computer.md)和[Linux](quick-onboard-linux-computer.md)服务器的混合支持。

Jeff 是云工作负荷所有者。 Jeff 负责根据 Contoso 的安全策略应用安全控件。 

Jeff 执行以下任务：

- 监视安全中心提供的安全建议
- 评估安全建议，并决定是否应该应用或取消建议。
- 应用安全建议

### <a name="remediate-threats-using-recommendations"></a>使用建议修正威胁
出于日常监视活动的一部分，李明登录到 Azure 并打开安全中心。 

1. Jeff 选择工作负荷的订阅。

2. Jeff 检查**安全分数**，以全面了解订阅的安全程度，并发现分数为548。

3. Jeff 必须决定要首先处理哪些建议。 因此，Jeff 单击 "安全分数"，并根据其对[安全分数的影响](security-center-secure-score.md)，开始处理建议。

4. 由于 Jeff 有大量连接的 VM 和服务器，因此他决定关注“计算和应用”。

5. 当 Jeff 单击 "**计算" 和 "应用**" 时，它们会看到建议列表，并根据安全分数影响来处理它们。

6. Jeff 拥有很多面向 Internet 的 Vm，因为它们的端口是公开的，因此它们担心攻击者可以获得对服务器的控制。 因此，Jeff 选择使用实时[**VM 访问**](security-center-just-in-time.md)。

Jeff 继续浏览高优先级和中优先级建议，并进行有关实施的决策。 对于每个建议，李明会查看安全中心提供的详细信息，以了解受影响的资源、安全分数影响的内容、每个建议的含义，以及如何缓解每个问题的补救步骤。

## <a name="conclusion"></a>结束语
监视安全中心内的建议有助于在发生攻击之前消除安全漏洞。 修正建议后，安全分数和工作负荷的安全状况会提高。 安全中心自动发现部署的新资源，根据安全策略对其进行评估，并提供新的建议对其进行保护。


## <a name="next-steps"></a>后续步骤
确保拥有一个适当的监视进程，定期检查安全中心中的建议，以便可以确保在一段时间内保护资源的安全。

此方案说明了如何使用安全中心内的安全策略和建议来帮助减少安全攻击。

了解如何通过[管理和响应安全警报](security-center-managing-and-responding-alerts.md)来应对威胁。
