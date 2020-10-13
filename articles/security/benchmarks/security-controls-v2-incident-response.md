---
title: Azure 安全基准 V2-事件响应
description: Azure 安全基准 V2 事件响应
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 172607a7f8f036bbfb68e8d15e77b2a3e3fb5377
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91326382"
---
# <a name="security-control-v2-incident-response"></a>安全控制 V2：事件响应

事件响应涵盖事件响应生命周期-准备、检测和分析、包含以及事件事件之后的控制。 这包括使用 azure 服务（如 Azure 安全中心和 Sentinel）自动执行事件响应过程。

## <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1：准备–更新 Azure 的事件响应过程

| Azure ID | CIS 控制 v2.0 ID (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| IR-1 | 19 | IR-4，IR-8 |

确保你的组织具有响应安全事件的流程，已为 Azure 更新这些流程，并定期运用这些流程来确保准备情况。

- [跨企业环境实施安全性](https://aka.ms/AzSec4)

- [事件响应参考指南](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**责任**：客户

**客户安全利益干系人** ([详细](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [安全操作](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [事件准备](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [威胁情报](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-2-preparation--setup-incident-notification"></a>IR-2：准备–安装事件通知

| Azure ID | CIS 控制 v2.0 ID (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| IR-2 | 19.5 | IR-4、IR 5、IR 6、IR-8 |

在 Azure 安全中心中设置安全事件联系人信息。 如果 Microsoft 安全响应中心 (MSRC) 发现你的数据被非法或未授权的一方访问，Microsoft 将使用此联系信息与你联系。 你还可以选择根据你的事件响应需求，在不同的 Azure 服务中自定义事件警报和通知。 

- [如何设置 Azure 安全中心安全联系人](../../security-center/security-center-provide-security-contact-details.md)

**责任**：客户

**客户安全利益干系人** ([详细](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [安全操作](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [事件准备](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

## <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3：检测和分析-基于高质量警报创建事件

| Azure ID | CIS 控制 v2.0 ID (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| IR-3 | 19.6 | IR-4、IR 5 |

确保你有创建高质量警报的过程，并衡量警报的质量。 这样，你就可以了解过去事件的经验并为分析师的警报设置优先级，因此它们不会浪费时间来处理误报。 

高质量的警报可以基于过去的事件经验、经验证的社区源以及旨在通过融合和关联各种信号源来生成和清理警报的工具。 

Azure 安全中心可跨多个 Azure 资产提供高质量的警报。 可以使用 ASC 数据连接器将警报流式传输到 Azure Sentinel。 Azure Sentinel 使你可以创建高级警报规则，以便自动生成事件以进行调查。 

使用导出功能导出 Azure 安全中心警报和建议，以帮助确定 Azure 资源的风险。 手动或以持续的连续方式导出警报和建议。

- [如何配置导出](../../security-center/continuous-export.md)

- [如何将警报流式传输到 Azure Sentinel](../../sentinel/connect-azure-security-center.md)

**责任**：客户

**客户安全利益干系人** ([详细](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [安全操作](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [事件准备](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [威胁情报](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4：检测和分析-调查事件

| Azure ID | CIS 控制 v2.0 ID (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| IR-4 | 19 | IR-4 |

请确保分析师可以在调查潜在事件时查询和使用不同的数据源，从而生成所发生问题的完整视图。 应收集不同的日志来跟踪跨 kill 链的潜在攻击者的活动，以避免出现盲点。  还应确保为其他分析师捕获见解和知识，并为将来的历史参考提供这些信息。  

用于调查的数据源包括已从作用域内服务和运行系统收集的集中式日志记录源，但也可以包括：

- 网络数据-使用网络安全组的流日志、Azure 网络观察程序和 Azure Monitor 来捕获网络流日志和其他分析信息。 

- 正在运行的系统的快照： 

    - 使用 Azure 虚拟机的快照功能创建正在运行的系统磁盘的快照。 

    - 使用操作系统的本机内存转储功能来创建运行系统内存的快照。

    - 使用 Azure 服务的快照功能或软件自己的功能来创建正在运行的系统的快照。

Azure Sentinel 跨几乎任何日志源和事例管理门户提供大量的数据分析，以管理事件的整个生命周期。 调查过程中的智能信息可与事件相关联，以便进行跟踪和报告。 

- [快照 Windows 计算机的磁盘](../../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [快照 Linux 计算机的磁盘](../../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure 支持诊断信息和内存转储收集](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [用 Azure Sentinel 调查事件](../../sentinel/tutorial-investigate-cases.md)

**责任**：客户

**客户安全利益干系人** ([详细](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [安全操作](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [事件准备](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [威胁情报](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR 5：检测和分析–优先级事件

| Azure ID | CIS 控制 v2.0 ID (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| IR-5 | 19.8 | CA-2、IR-4 |

根据警报严重性和资产敏感度为分析人员提供要重点关注的事件的上下文。 

Azure 安全中心为每个警报分配一个严重性，以帮助你确定应该首先调查的警报的优先级。 严重性取决于安全中心在查找或用于发出警报的分析中的置信度，以及导致警报的活动的恶意意图的置信度。

此外，使用标记标记资源并创建命名系统来标识和分类 Azure 资源，尤其是处理敏感数据的资源。  你的责任是根据发生事件的 Azure 资源和环境的关键性确定修正警报的优先级。

- [Azure 安全中心中的安全警报](../../security-center/security-center-alerts-overview.md)

- [使用标记整理 Azure 资源](/azure/azure-resource-manager/resource-group-using-tags)

**责任**：客户

**客户安全利益干系人** ([详细](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [安全操作](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [事件准备](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [威胁情报](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6：包含、根除和恢复-自动化事件处理

| Azure ID | CIS 控制 v2.0 ID (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| IR-6 | 19 | IR-4、IR 5、IR-6 |

自动执行手动重复性任务来加快响应时间并减轻分析人员的负担。 执行手动任务需要更长时间，从而降低每个事件的速度，减少分析师可以处理的事件数。 手动任务还会增加分析师疲劳，这会增加导致延迟的人为错误风险，并降低分析师有效地关注复杂任务的能力。 使用 Azure 安全中心和 Azure Sentinel 中的工作流自动化功能来自动触发操作或运行操作手册来响应传入安全警报。 操作手册执行一些操作，如发送通知、禁用帐户和隔离有问题的网络。 

- [在安全中心配置工作流自动化](../../security-center/workflow-automation.md)

- [在 Azure 安全中心设置自动威胁响应](../../security-center/tutorial-security-incident.md#triage-security-alerts)

- [在 Azure Sentinel 中设置自动威胁响应](../../sentinel/tutorial-respond-threats-playbook.md)

**责任**：客户

**客户安全利益干系人** ([详细](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [安全操作](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [事件准备](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [威胁情报](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

