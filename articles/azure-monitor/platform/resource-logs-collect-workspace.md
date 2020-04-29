---
title: 在 Log Analytics 工作区中收集 Azure 资源日志
description: 了解如何将 Azure 资源日志流式传输到 Azure Monitor 中的 Log Analytics 工作区。
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 36bd464624118b7671a3879bcc1d34114bba9ce3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "79248588"
---
# <a name="collect-azure-platform-logs-in-log-analytics-workspace-in-azure-monitor"></a>在 Azure Monitor 的 Log Analytics 工作区中收集 Azure 平台日志
Azure 中的[平台日志](platform-logs-overview.md)（包括 Azure 活动日志和资源日志）提供 Azure 资源及其所依赖的 Azure 平台的详细诊断和审核信息。 本文介绍如何收集 Log Analytics 工作区中的资源日志，以便可以结合通过强大的日志查询在 Azure Monitor 日志中收集的其他监视数据对其进行分析，并利用其他 Azure Monitor 功能，例如警报和可视化。 


## <a name="what-you-can-do-with-platform-logs-in-a-workspace"></a>如何处理工作区中的平台日志
将平台日志收集到 Log Analytics 工作区中可以统一分析所有 Azure 资源的日志，并利用适用于 [Azure Monitor 日志](data-platform-logs.md)的所有功能，包括：

* **日志查询** - 使用强大的查询语言创建[日志查询](../log-query/log-query-overview.md)，以快速分析和洞察诊断数据，并结合从 Azure Monitor 中的其他源收集的数据对其进行分析。
* **警报** - 使用 [Azure Monitor 中的日志警报](alerts-log.md)获取在资源日志中识别到的关键状况和模式的主动通知。
* **可视化** - 将日志查询的结果固定到 Azure 仪表板，或将其作为交互式报告的一部分包含在工作簿中。

## <a name="prerequisites"></a>必备条件
如果没有工作区，需要[创建新的工作区](../learn/quick-create-workspace.md)。 只要配置设置的用户同时拥有两个订阅的相应 RBAC 访问权限，工作区就不必位于发送日志的资源所在的订阅中。

## <a name="create-a-diagnostic-setting"></a>创建诊断设置
通过创建 Azure 资源的诊断设置，将平台日志发送到 Log Analytics 工作区和其他目标。 有关详细信息，请参阅[创建诊断设置以收集 Azure 中的日志和指标](diagnostic-settings.md)。


## <a name="activity-log-collection"></a>活动日志集合
可以将任一订阅中的活动日志发送到最多五个 Log Analytics 工作区。 Log Analytics 工作区中收集的资源日志数据存储在 AzureActivity 表中  。 

## <a name="resource-log-collection-mode"></a>资源日志收集模式
如 [Azure Monitor 日志的结构](../log-query/logs-structure.md)中所述，在 Log Analytics 工作区中收集的资源日志存储在表中。 资源日志使用的表取决于资源使用的收集类型：

- Azure 诊断 - 所有数据将写入到 _AzureDiagnostics_ 表中。
- 特定于资源 - 每个类别的资源的数据将写入到单独的表中。

### <a name="azure-diagnostics-mode"></a>Azure 诊断模式 
在此模式下，任何[诊断设置](diagnostic-settings.md)中的所有数据将收集到 _AzureDiagnostics_ 表中。 这是当今大多数 Azure 服务使用的传统方法。

由于多个资源类型会将数据发送到同一个表，因此其架构是所收集的所有不同数据类型的架构的超集。

在以下示例中，以下数据类型的诊断设置将收集到同一个工作区中：

- 服务 1 的审核日志（架构由列 A、B 和 C 组成）  
- 服务 1 的错误日志（架构由列 D、E 和 F 组成）  
- 服务 2 的审核日志（架构由列 G、H 和 I 组成）  

AzureDiagnostics 表的外观如下所示：  

| ResourceProvider    | 类别     | A  | B  | C  | D  | E  | F  | G  | H  | I  |
| -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- |
| Microsoft.Service1 | AuditLogs    | x1 | y1 | z1 |    |    |    |    |    |    |
| Microsoft.Service1 | ErrorLogs    |    |    |    | q1 | w1 | e1 |    |    |    |
| Microsoft.Service2 | AuditLogs    |    |    |    |    |    |    | j1 | k1 | l1 |
| Microsoft.Service1 | ErrorLogs    |    |    |    | q2 | w2 | e2 |    |    |    |
| Microsoft.Service2 | AuditLogs    |    |    |    |    |    |    | j3 | k3 | l3 |
| Microsoft.Service1 | AuditLogs    | x5 | y5 | z5 |    |    |    |    |    |    |
| ... |

### <a name="resource-specific"></a>特定于资源
在此模式下，将会根据在诊断设置中选择的每个类别，在所选工作区中创建各个表。 建议使用此方法，因为它可以大幅简化日志查询中数据的处理、更好地发现架构及其结构、改善引入延迟和查询时间方面的性能、可以授予针对特定表的 RBAC 权限，等等。 所有 Azure 服务最终都会迁移到特定于资源的模式。 

以上示例会创建三个表：
 
- 如下所示的表 *Service1AuditLogs*：

    | 资源提供程序 | 类别 | A | B | C |
    | -- | -- | -- | -- | -- |
    | Service1 | AuditLogs | x1 | y1 | z1 |
    | Service1 | AuditLogs | x5 | y5 | z5 |
    | ... |

- 如下所示的表 *Service1ErrorLogs*：  

    | 资源提供程序 | 类别 | D | E | F |
    | -- | -- | -- | -- | -- | 
    | Service1 | ErrorLogs |  q1 | w1 | e1 |
    | Service1 | ErrorLogs |  q2 | w2 | e2 |
    | ... |

- 如下所示的表 *Service2AuditLogs*：  

    | 资源提供程序 | 类别 | G | H | I |
    | -- | -- | -- | -- | -- |
    | Service2 | AuditLogs | j1 | k1 | l1|
    | Service2 | AuditLogs | j3 | k3 | l3|
    | ... |



### <a name="select-the-collection-mode"></a>选择收集模式
大多数 Azure 资源在“Azure 诊断”或“特定于资源”模式下将数据写入工作区，而不允许用户选择模式。   有关使用哪种模式的详细信息，请参阅[每个服务的文档](diagnostic-logs-schema.md)。 所有 Azure 服务最终都会使用特定于资源的模式。 在进行这种过渡期间，某些资源允许在诊断设置中选择模式。 应该为任何新的诊断设置指定特定于资源的模式，因为这可以更轻松地管理数据，并可帮助你在今后避免复杂的迁移。
  
   ![诊断设置模式选择器](media/resource-logs-collect-workspace/diagnostic-settings-mode-selector.png)




> [!NOTE]
> 目前，只能在 Azure 门户中配置诊断设置时选择“Azure 诊断”和“特定于资源”模式。   如果使用 CLI、PowerShell 或 REST API 配置设置，则模式默认为“Azure 诊断”。 

可将现有的诊断设置修改为特定于资源的模式。 在这种情况下，已收集的数据将保留在 _AzureDiagnostics_ 表中，直到根据工作区的保留设置删除了这些数据。 新数据将收集到专用表中。 可以使用 [union](https://docs.microsoft.com/azure/kusto/query/unionoperator) 运算符跨两个表查询数据。

有关支持特定于资源模式的 Azure 服务的公告，请继续阅读 [Azure 更新](https://azure.microsoft.com/updates/)博客。

### <a name="column-limit-in-azurediagnostics"></a>AzureDiagnostics 中的列限制
Azure Monitor 日志中的任何一个表限制为 500 个属性。 一旦达到该限制，在引入时，包含不属于前 500 个属性的数据的所有行将被删除。 *AzureDiagnostics* 表特别容易超过此限制，因为它包含写入到其中的所有 Azure 服务的属性。

如果从多个服务收集资源日志，  AzureDiagnostics 可能会超过此限制，因此数据将会丢失。 在所有 Azure 服务都支持特定于资源的模式之前，应将资源配置为写入到多个工作区，以减少达到 500 列限制的可能性。

### <a name="azure-data-factory"></a>Azure 数据工厂
Azure 数据工厂包含非常详细的日志集，已知它是一个写入大量列的服务，可能会导致 _AzureDiagnostics_ 超过其限制。 对于在支持特定于资源的模式之前配置的任何诊断设置，将会针对任一活动，为每个唯一命名的用户参数创建一个新列。 由于活动输入和输出的详细特性，将会创建更多列。
 
应尽快迁移日志，以使用特定于资源的模式。 如果无法立即做到这一点，一种临时的替代方案是将 Azure 数据工厂日志隔离到其自身的工作区，以尽量减少这些日志影响工作区中收集的其他日志类型的可能性。


## <a name="next-steps"></a>后续步骤

* [详细阅读资源日志](platform-logs-overview.md)。
* [创建诊断设置以收集 Azure 中的日志和指标](diagnostic-settings.md)。
