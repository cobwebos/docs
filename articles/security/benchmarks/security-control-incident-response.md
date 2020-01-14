---
title: Azure 安全控制-事件响应
description: 安全控制事件响应
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: fb3560aa2d3fbf48ab63c4da4d3a8d69cb677209
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934420"
---
# <a name="security-control-incident-response"></a>安全控制：事件响应

通过开发和实施事件响应基础结构（例如，计划、定义的角色、培训、通信、管理监管）来保护组织的信息及其信誉，从而快速发现攻击，然后有效地遏制损害，eradicating 攻击者的状态，并还原网络和系统的完整性。

## <a name="101-create-an-incident-response-guide"></a>10.1：创建事件响应指南

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 10.1 | 19.1、19.2、19。3 | 客户 |

构建组织的事件响应指南。 请确保有一些书面事件响应计划，这些计划定义了人员的所有角色，以及从检测到后事件检查的事件处理/管理阶段。

如何在 Azure 安全中心内配置 Workflow 自动化：

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

有关构建你自己的安全事件响应过程的指导：

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Microsoft 安全响应中心事件解析：

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

客户还可以利用 NIST 的计算机安全事件处理指南来帮助创建自己的事件响应计划：

https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

## <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：创建事件计分和优先级过程

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 10.2 | 19.8 | 客户 |

安全中心为每个警报分配一个严重性，以帮助你确定应该首先调查的警报的优先级。 严重性取决于安全中心在查找或用于发出警报的分析中的置信度，以及导致警报的活动的恶意意图的置信度。

此外，清楚地标记订阅（例如 生产、非生产），并创建一个命名系统来明确识别和分类 Azure 资源。

## <a name="103-test-security-response-procedures"></a>10.3：测试安全响应过程

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 10.3 | 19 | 客户 |

执行练习来测试系统的事件响应功能是否正常。 确定薄弱点和间隙，并根据需要修改计划。

请参阅 NIST 发布：针对 IT 计划和功能的测试、培训和试验计划指南：

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

## <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全事件联系人详细信息并为安全事件配置警报通知

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 10.4 | 19.5 | 客户 |

如果 Microsoft 安全响应中心（MSRC）发现客户数据被非法或未授权的一方访问，Microsoft 将使用安全事件联系人信息与你联系。  在事实后查看事件，以确保解决问题。

如何设置 Azure 安全中心安全联系人：

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

## <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：将安全警报合并到事件响应系统中

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 10.5 | 19.6 | 客户 |

使用连续导出功能导出 Azure 安全中心警报和建议。 使用连续导出，可以手动或以持续、持续的方式导出警报和建议。 可以使用 Azure 安全中心数据连接器流式传输警报 Sentinel。

如何配置连续导出：

https://docs.microsoft.com/azure/security-center/continuous-export

如何将警报流式传输到 Azure Sentinel：

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

## <a name="106-automate-the-response-to-security-alerts"></a>10.6：自动响应安全警报

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 10.6 | 19 | 客户 |

使用 Azure 安全中心中的 "工作流自动化" 功能，通过 &quot;逻辑应用来自动触发响应&quot; 安全警报和建议。

如何配置工作流自动化和逻辑应用：

https://docs.microsoft.com/azure/security-center/workflow-automation

## <a name="next-steps"></a>后续步骤

请参阅下一个安全控制：[渗透测试和 Red 团队练习](security-control-penetration-tests-red-team-exercises.md)