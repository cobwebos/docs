---
title: Azure 安全基准 V2-管理和策略
description: Azure 安全基准 V2 管理和策略
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 420ab87591e230592ec8f728c6acb155266b5912
ms.sourcegitcommit: dc68a2c11bae2e9d57310d39fbed76628233fd7f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2020
ms.locfileid: "91403520"
---
# <a name="security-control-v2-governance-and-strategy"></a>安全控制 V2：监管和策略

调控和策略提供的指导可确保一致的安全策略和记录的管理方法来指导和维持安全保障，包括为不同的云安全功能、统一的技术策略和支持策略和标准建立角色和责任。

## <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1：定义资产管理和数据保护策略

| Azure ID | CIS 控制 v2.0 ID (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| GS-1 | 2，13 | SC、AC |

确保为系统和数据的持续监视和保护记录并传达清晰的策略。 确定业务关键数据和系统的发现、评估、保护和监视的优先级。 

此策略应包含以下元素的文档指南、策略和标准： 

- 数据分类标准符合业务风险

- 安全组织查看风险和资产清单 

- 使用 Azure 服务时的安全组织审批 

- 资产在其生命周期中的安全性

- 根据组织数据分类所需的访问控制策略

- 使用 Azure 本机和第三方数据保护功能

- 传输和静态用例的数据加密要求

- 适当的加密标准

有关详细信息，请参阅以下资源：
- [Azure 安全体系结构建议-存储、数据和加密](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Azure 安全性基础知识-Azure 数据安全、加密和存储](../fundamentals/encryption-overview.md)

- [云采用框架-Azure 数据安全和加密最佳实践](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

- [Azure 安全基准-资产管理](security-controls-v2-asset-management.md)

- [Azure 安全基准-数据保护](security-controls-v2-data-protection.md)

**责任**：客户

**客户安全利益干系人** ([详细](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [所有利益干系人](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2：定义企业分段策略

| Azure ID | CIS 控制 v2.0 ID (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| GS-2 | 4，9，16 | AC、CA、SC |

建立企业范围的策略，以便使用标识、网络、应用程序、订阅、管理组和其他控件的组合对资产进行分段。

仔细权衡安全分离需求与实现需要彼此通信和访问数据的系统日常操作的需求。

确保跨控件类型（包括网络安全、标识和访问模型以及应用程序权限/访问模型以及人机处理控件）一致地实现分段策略。

- [有关 Azure 中的分段策略的指南 (视频) ](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [有关 Azure 中的分段策略的指南 (文档) ](/security/compass/governance#enterprise-segmentation-strategy)

- [使网络分段与企业分段策略相匹配](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**责任**：客户

**客户安全利益干系人** ([详细](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [所有利益干系人](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-3-define-security-posture-management-strategy"></a>GS-3：定义安全状态管理策略

| Azure ID | CIS 控制 v2.0 ID (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| GS-3 | 20、3、5 | RA、CM、SC |

持续衡量并缓解你的个人资产及其托管环境的风险。 确定高价值资产和高度暴露的攻击面，例如已发布的应用程序、网络入口和出口点、用户和管理员终结点等。

- [Azure 安全基准-状况和漏洞管理](security-controls-v2-posture-vulnerability-management.md)

**责任**：客户

**客户安全利益干系人** ([详细](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [所有利益干系人](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4：协调组织角色、职责和责任

| Azure ID | CIS 控制 v2.0 ID (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| GS-4 | 空值 | PL，PM |

确保为安全组织中的角色和责任记录并传达清晰的策略。 为安全决策提供明确的责任，为每个人提供对共享责任模型的培训，并向技术团队培训技术团队以保护云。

- [Azure 安全最佳做法 1-人员：教育团队开展云安全旅程](https://aka.ms/AzSec1)

- [Azure 安全最佳实践 2-人员：向团队培训云安全技术](https://aka.ms/AzSec2)

- [Azure 安全最佳实践 3-过程：为云安全决策分配责任](https://aka.ms/AzSec3)

**责任**：客户

**客户安全利益干系人** ([详细](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [所有利益干系人](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-5-define-network-security-strategy"></a>GS-5：定义网络安全策略

| Azure ID | CIS 控制 v2.0 ID (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| GS-5 | 9 | CA，SC |

建立 Azure 网络安全方法，作为组织整体安全访问控制策略的一部分。  

此策略应包含以下元素的文档指南、策略和标准： 

- 集中式网络管理和安全责任

- 虚拟网络分段模型与企业分段策略一致

- 不同威胁和攻击方案中的补救策略

- Internet 边缘和入口和出口策略

- 混合云和本地互连策略

- 最新的网络安全项目 (例如网络关系图、引用网络体系结构) 

有关详细信息，请参阅以下资源：

- [Azure 安全最佳实践 11-体系结构。单一统一安全策略](https://aka.ms/AzSec11)

- [Azure 安全基准-网络安全](security-controls-v2-network-security.md)

- [Azure 网络安全概述](../fundamentals/network-overview.md)

- [企业网络体系结构策略](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**责任**：客户

**客户安全利益干系人** ([详细](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [所有利益干系人](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6：定义标识和特权访问策略

| Azure ID | CIS 控制 v2.0 ID (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| GS-6 | 16，4 | AC、AU、SC |

建立 Azure 标识和特权访问方法，作为组织整体安全访问控制策略的组成部分。  

此策略应包含以下元素的文档指南、策略和标准： 

- 集中标识和身份验证系统及其与其他内部和外部标识系统的互连

- 不同用例和条件中的强身份验证方法

- 对高特权用户的保护

- 异常用户活动监视和处理  

- 用户标识和访问评审和协调过程

有关详细信息，请参阅以下资源：

- [Azure 安全基准-标识管理](security-controls-v2-identity-management.md)

- [Azure 安全基准-特权访问](security-controls-v2-privileged-access.md)

- [Azure 安全最佳实践 11-体系结构。单一统一安全策略](https://aka.ms/AzSec11)

- [Azure 标识管理安全概述](../fundamentals/identity-management-overview.md)

**责任**：客户

**客户安全利益干系人** ([详细](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [所有利益干系人](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7：定义日志记录和威胁响应策略

| Azure ID | CIS 控制 v2.0 ID (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| GS-7 | 19 | IR，AU，RA，SC |

建立日志记录和威胁响应策略，以快速检测和修正威胁，同时满足合规性要求。 确定提供具有高质量警报和无缝体验的分析师的优先级，以便他们能够专注于威胁而不是集成和手动步骤。 

此策略应包含以下元素的文档指南、策略和标准： 

- 安全操作 (SecOps) 组织的角色和职责 

- 明确定义的事件响应过程与 NIST 或其他行业框架协调 

- 日志捕获和保留期，可支持威胁检测、事件响应和合规性需求

- 使用 SIEM、本机 Azure 功能和其他源，集中查看和关联有关威胁的信息 

- 与客户、供应商和感兴趣的各方之间的通信和通知计划

- 使用 Azure 本机和第三方平台进行事件处理，如日志记录和威胁检测、辩论和攻击修正和根除

- 处理事件和事件后活动的过程，如经验教训和证据保留期

有关详细信息，请参阅以下资源：
- [Azure 安全基准-记录和威胁检测](security-controls-v2-logging-threat-detection.md)

- [Azure 安全基准-事件响应](security-controls-v2-incident-response.md)

- [Azure 安全最佳实践 4-过程。更新云的事件响应过程](https://aka.ms/AzSec4)

- [Azure 采用框架、日志记录和报表决策指南](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Azure 企业规模、管理和监视](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**责任**：客户

**客户安全利益干系人** ([详细](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [所有利益干系人](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-8-define-backup-and-recovery-strategy"></a>GS-8：定义备份和恢复策略

| Azure ID | CIS 控制 v2.0 ID (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| GS-8 | 10 | CP |

为组织建立 Azure 备份和恢复策略。 

此策略应包含以下元素的文档指南、策略和标准： 

- 恢复时间目标 (RTO) 和恢复点目标 (RPO) 定义符合你的业务恢复目标

- 应用程序和基础结构设置中的冗余设计

- 使用访问控制和数据加密保护备份

有关详细信息，请参阅以下资源：
- [Azure 安全基准-备份和恢复](security-controls-v2-backup-recovery.md)

- [Azure 良好体系结构框架-Azure 应用程序的备份和灾难恢复](/azure/architecture/framework/resiliency/backup-and-recovery)

- [Azure 采用框架-业务连续性和灾难恢复](/azure/cloud-adoption-framework/ready/enterprise-scale/business-continuity-and-disaster-recovery)

**责任**：客户

**客户安全利益干系人** ([详细](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [所有利益干系人](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

