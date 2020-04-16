---
title: Azure 安全控制 - 事件响应
description: Azure 安全控制事件响应
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 993793d21e6253188dfc199d8701cbe117503517
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408420"
---
# <a name="security-control-incident-response"></a>安全控制：事件响应

通过开发和实施事件响应基础结构（例如计划、定义的角色、培训、通信、管理监督），以快速发现攻击，然后有效地遏制损害，消除攻击者的存在，恢复网络和系统的完整性，保护组织的信息及其声誉。

## <a name="101-create-an-incident-response-guide"></a>10.1：创建事件响应指南

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 10.1 | 19.1, 19.2, 19.3 | 客户 |

为您的组织构建事件响应指南。 确保有书面事件响应计划，定义人员的所有角色以及事件处理/管理阶段，从检测到事件后审查。  

- [有关构建您自己的安全事件响应流程的指导](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [微软安全响应中心事件剖析](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [利用 NIST 的计算机安全事件处理指南，帮助创建您自己的事件响应计划](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

## <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2： 创建事件评分和优先级处理过程

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 10.2 | 19.8 | 客户 |

安全中心为每个警报分配严重性，以帮助您确定应首先调查哪些警报的优先级。 严重性取决于安全中心在发出警报时所依据的检测结果和分析结果的置信度，以及导致发出警报的活动的恶意企图的置信度。 

此外，清楚地标记订阅（对于前 使用标记创建命名系统以明确识别和分类 Azure 资源，尤其是处理敏感数据的资源，生产非 prod）。  您有责任根据事件发生的 Azure 资源和环境的重要性确定警报的修复优先级。

- [Azure 安全中心的安全警报](https://docs.microsoft.com/azure/security-center/security-center-alerts-overview)

- [使用标记组织 Azure 资源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

## <a name="103-test-security-response-procedures"></a>10.3： 测试安全响应程序

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 10.3 | 19 | 客户 |

执行练习，以常规节奏测试系统的事件响应功能，以帮助保护 Azure 资源。 找出薄弱环节和差距，并根据需要修订计划。

- [NIST 出版物 - IT 计划和功能测试、培训和锻炼计划指南](https://csrc.nist.gov/publications/detail/sp/800-84/final)

## <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全事件联系人详细信息，并配置安全事件的警报通知

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 10.4 | 19.5 | 客户 |

如果 Microsoft 安全响应中心 （MSRC） 发现您的数据已被非法或未经授权的方访问，Microsoft 将使用安全事件联系信息与您联系。 事后查看事件，以确保问题得到解决。

- [如何设置 Azure 安全中心安全联系人](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

## <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5： 将安全警报纳入事件响应系统

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 10.5 | 19.6 | 客户 |

使用"连续导出"功能导出 Azure 安全中心警报和建议，以帮助识别 Azure 资源的风险。 "连续导出"允许您手动或持续、持续的方式导出警报和建议。 您可以使用 Azure 安全中心数据连接器将警报流式传输到 Azure 哨兵。

- [如何配置连续导出](https://docs.microsoft.com/azure/security-center/continuous-export)

- [如何将警报流式传输到 Azure 哨兵](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

## <a name="106-automate-the-response-to-security-alerts"></a>10.6： 自动响应安全警报

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 10.6 | 19 | 客户 |

使用 Azure 安全中心的工作流自动化功能通过安全警报和建议上的"逻辑应用"自动触发响应，以保护 Azure 资源。

- [如何配置工作流自动化和逻辑应用](https://docs.microsoft.com/azure/security-center/workflow-automation)


## <a name="next-steps"></a>后续步骤

- 请参阅下一个安全控制：[渗透测试和红队练习](security-control-penetration-tests-red-team-exercises.md)