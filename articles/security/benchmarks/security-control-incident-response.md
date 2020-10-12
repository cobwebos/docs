---
title: Azure 安全控制 - 事件响应
description: Azure 安全控制 - 事件响应
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 993793d21e6253188dfc199d8701cbe117503517
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "81408420"
---
# <a name="security-control-incident-response"></a>安全控制：事件响应

通过以下方式保护组织的信息及其声誉：开发并实施事件响应基础结构（如计划、定义的角色、训练、通信、管理监督），以便快速发现攻击，然后有效地控制损害，消除攻击者的存在，恢复网络和系统的完整性。

## <a name="101-create-an-incident-response-guide"></a>10.1：创建事件响应指导

| Azure ID | CIS ID | 责任方 |
|--|--|--|
| 10.1 | 19.1、19.2、19.3 | 客户 |

为组织制定事件响应指南。 确保在书面的事件响应计划中定义人员职责，以及事件处理/管理从检测到事件后审查的各个阶段。  

- [关于建立自己的安全事件响应流程的指南](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft 安全响应中心事件分析](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [利用 NIST 的“计算机安全事件处理指南”，帮助制定自己的事件响应计划](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

## <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：创建事件评分和优先级设定过程

| Azure ID | CIS ID | 责任方 |
|--|--|--|
| 10.2 | 19.8 | 客户 |

安全中心为每条警报分配严重性，以帮助你优先处理应该最先调查的警报。 严重性取决于安全中心在发出警报时所依据的检测结果和分析结果的置信度，以及导致发出警报的活动的恶意企图的置信度。 

此外，请用标记明确标记订阅（例如 生产、非生产）并创建命名系统来对 Azure 资源进行明确标识和分类，特别是处理敏感数据的资源。  你的责任是根据发生事件的 Azure 资源和环境的关键性确定修正警报的优先级。

- [Azure 安全中心中的安全警报](https://docs.microsoft.com/azure/security-center/security-center-alerts-overview)

- [使用标记整理 Azure 资源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

## <a name="103-test-security-response-procedures"></a>10.3：测试安全响应过程

| Azure ID | CIS ID | 责任方 |
|--|--|--|
| 10.3 | 19 | 客户 |

进行练习，测试定期事件的事件响应功能，以帮助保护 Azure 资源。 识别弱点和差距，并根据需要修改计划。

- [NIST 发布 - IT 计划和功能的测试、训练和演练计划指南](https://csrc.nist.gov/publications/detail/sp/800-84/final)

## <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全事件联系人详细信息，并针对安全事件配置警报通知

| Azure ID | CIS ID | 责任方 |
|--|--|--|
| 10.4 | 19.5 | 客户 |

如果 Microsoft 安全响应中心 (MSRC) 发现非法或未经授权的一方访问了你的数据，Microsoft 将使用安全事件联系人信息来与你取得联系。 事后审查事件，确保问题得到解决。

- [如何设置 Azure 安全中心安全联系人](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

## <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：将安全警报整合到事件响应系统中

| Azure ID | CIS ID | 责任方 |
|--|--|--|
| 10.5 | 19.6 | 客户 |

使用连续导出功能导出 Azure 安全中心警报和建议，以帮助确定 Azure 资源的风险。 使用连续导出可以手动导出或者持续导出警报和建议。 可以使用 Azure 安全中心数据连接器将警报流式传输到 Azure Sentinel。

- [如何配置连续导出](https://docs.microsoft.com/azure/security-center/continuous-export)

- [如何将警报流式传输到 Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

## <a name="106-automate-the-response-to-security-alerts"></a>10.6：自动响应安全警报

| Azure ID | CIS ID | 责任方 |
|--|--|--|
| 10.6 | 19 | 客户 |

使用 Azure 安全中心的工作流自动化功能，通过 "逻辑应用" 自动触发有关安全警报和建议的响应，以保护 Azure 资源。

- [如何配置工作流自动化和逻辑应用](https://docs.microsoft.com/azure/security-center/workflow-automation)


## <a name="next-steps"></a>后续步骤

- 请参阅下一个安全控制：[渗透测试和红队演练](security-control-penetration-tests-red-team-exercises.md)