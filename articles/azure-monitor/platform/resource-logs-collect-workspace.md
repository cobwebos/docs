---
title: Log Analytics 工作区收集 Azure 资源日志
description: 了解如何将 Azure 资源日志流式传输到 Azure Monitor 中的 Log Analytics 工作区。
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 83b91be52694076373d950e0ad785ef22671ef4f
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2019
ms.locfileid: "74894516"
---
# <a name="collect-azure-resource-logs-in-log-analytics-workspace-in-azure-monitor"></a>在 Azure Monitor 中 Log Analytics 工作区收集 Azure 资源日志
Azure 中的[资源日志](resource-logs-overview.md)提供有关 Azure 资源内部操作的丰富、频繁的数据。 本文介绍如何在 Log Analytics 工作区中收集资源日志，以便使用功能强大的日志查询在 Azure Monitor 日志中收集的其他监视数据对其进行分析，还可以 Azure Monitor 利用警报和可视化效果. 


## <a name="what-you-can-do-with-resource-logs-in-a-workspace"></a>你可以对工作区中的资源日志执行的操作
通过将资源日志收集到 Log Analytics 工作区中，可以分析所有 Azure 资源的日志，并利用可用于[Azure Monitor 日志](data-platform-logs.md)的所有功能，包括以下内容：

* **日志查询**-使用强大的查询语言创建[日志查询](../log-query/log-query-overview.md)，以快速分析和获取诊断数据的见解，并使用从 Azure Monitor 中的其他源收集的数据对其进行分析。
* **警报**-使用[Azure Monitor 中的日志警报](alerts-log.md)，获取资源日志中标识的关键条件和模式的主动通知。
* **可视化效果**-将日志查询的结果固定到 Azure 仪表板，或将其作为交互式报表的一部分包含在工作簿中。

## <a name="prerequisites"></a>必备组件
如果尚未[创建新的工作区](../learn/quick-create-workspace.md)，则需要创建一个。 只要配置设置的用户具有对这两个订阅的相应 RBAC 访问权限，工作区就不必与资源发送日志位于同一订阅中。

## <a name="create-a-diagnostic-setting"></a>创建诊断设置
默认情况下，不收集资源日志。 通过创建 Azure 资源的诊断设置，在 Log Analytics 工作区和其他目标中收集它们。 有关详细信息，请参阅[创建诊断设置以收集 Azure 中的日志和指标](diagnostic-settings.md)。

## <a name="collection-mode"></a>收集模式
Log Analytics 工作区中收集的数据存储在表中，如[Azure Monitor 日志的结构](../log-query/logs-structure.md)所述。 资源日志使用的表取决于资源使用的集合类型：

- Azure 诊断-写入的所有数据都在_AzureDiagnostics_表中。
- 特定于资源的数据将写入每个资源类别的单独表中。

### <a name="azure-diagnostics-mode"></a>Azure 诊断模式 
在此模式下，将在_AzureDiagnostics_表中收集所有[诊断设置](diagnostic-settings.md)中的所有数据。 这是当今大多数 Azure 服务使用的传统方法。

由于多个资源类型将数据发送到同一个表，因此其架构是所收集的所有不同数据类型的架构的超集。

请考虑以下示例，其中在以下数据类型的相同工作区中收集了诊断设置：

- 服务1的审核日志（架构中包含列 A、B 和 C）  
- 服务1的错误日志（架构中包含列 D、E 和 F）  
- 服务2（具有由 G、H 和 I 列组成的架构）的审核日志  

AzureDiagnostics 表将如下所示：  

| ResourceProvider    | 类别     | A  | B  | C  | D  | E  | F  | G  | H  | I  |
| -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- |
| Service1 | AuditLogs    | x1 | y1 | z1 |    |    |    |    |    |    |
| Service1 | ErrorLogs    |    |    |    | q1 | w1 | e1 |    |    |    |
| Service2 | AuditLogs    |    |    |    |    |    |    | j1 | k1 | l1 |
| Service1 | ErrorLogs    |    |    |    | q2 | w2 | e2 |    |    |    |
| Service2 | AuditLogs    |    |    |    |    |    |    | j3 | k3 | l3 |
| Service1 | AuditLogs    | x5 | y5 | z5 |    |    |    |    |    |    |
| ... |

### <a name="resource-specific"></a>特定于资源
在此模式下，将为在 "诊断" 设置中选择的每个类别创建所选工作区中的各个表。 建议使用此方法，因为这样可以更轻松地处理日志查询中的数据、提供架构及其结构的更好发现，提高引入延迟和查询时间的性能，并能够在特定表。 所有 Azure 服务最终将迁移到特定于资源的模式。 

上面的示例将生成三个表：
 
- 表*Service1AuditLogs*如下所示：

    | 资源提供程序 | 类别 | A | B | C |
    | -- | -- | -- | -- | -- |
    | 服务 1 | AuditLogs | x1 | y1 | z1 |
    | 服务 1 | AuditLogs | x5 | y5 | z5 |
    | ... |

- 表*Service1ErrorLogs*如下所示：  

    | 资源提供程序 | 类别 | D | E | F |
    | -- | -- | -- | -- | -- | 
    | 服务 1 | ErrorLogs |  q1 | w1 | e1 |
    | 服务 1 | ErrorLogs |  q2 | w2 | e2 |
    | ... |

- 表*Service2AuditLogs*如下所示：  

    | 资源提供程序 | 类别 | G | H | I |
    | -- | -- | -- | -- | -- |
    | 服务2 | AuditLogs | j1 | k1 | l1|
    | 服务2 | AuditLogs | j3 | k3 | l3|
    | ... |



### <a name="select-the-collection-mode"></a>选择收集模式
大多数 Azure 资源会将数据写入**Azure 诊断**或**特定于资源的模式下**的工作区，而无需作出选择。 请参阅[每个服务的文档](diagnostic-logs-schema.md)以了解其使用的模式的详细信息。 所有 Azure 服务最终将使用特定于资源的模式。 在此转换过程中，某些资源将允许您在 "诊断" 设置中选择一种模式。 你应为任何新的诊断设置指定特定于资源的模式，因为这样可以更轻松地管理数据，并可帮助你在以后避免复杂的迁移。
  
   ![诊断设置模式选择器](media/resource-logs-collect-workspace/diagnostic-settings-mode-selector.png)




> [!NOTE]
> 目前，在 Azure 门户中配置诊断设置时，只能选择**Azure 诊断**和**特定于资源**的模式。 如果使用 CLI、PowerShell 或 Rest API 配置设置，则默认为**Azure 诊断**。

可以将现有诊断设置修改为特定于资源的模式。 在这种情况下，已收集的数据将保留在_AzureDiagnostics_表中，直到根据工作区的保留设置将其删除。 新数据将收集到专用表中。 使用[union](https://docs.microsoft.com/azure/kusto/query/unionoperator)运算符跨两个表查询数据。

继续观看[Azure 更新](https://azure.microsoft.com/updates/)博客，了解有关支持特定于资源模式的 azure 服务的公告。

### <a name="column-limit-in-azurediagnostics"></a>AzureDiagnostics 中的列限制
对于 Azure Monitor 日志中的任何表，都存在500的属性限制。 达到此限制后，将在引入时删除任何包含第一个500之外的属性的数据的行。 *AzureDiagnostics*表特别受此限制的限制，因为它包含向其写入的所有 Azure 服务的属性。

如果要从多个服务收集资源日志， _AzureDiagnostics_可能超过此限制，将丢失数据。 在所有 Azure 服务都支持特定于资源的模式之前，应将资源配置为写入多个工作区，以降低达到500列限制的可能性。

### <a name="azure-data-factory"></a>Azure 数据工厂
Azure 数据工厂是一组非常详细的日志，它是已知写入大量列并可能导致_AzureDiagnostics_超出其限制的一种服务。 对于在启用特定于资源的模式之前配置的任何诊断设置，将为任何活动的每个唯一命名的 user 参数创建一个新列。 由于活动输入和输出的详细特性，将创建更多列。
 
应尽可能快地迁移日志，以使用特定于资源的模式。 如果无法立即执行此操作，则一种临时替代方法是将 Azure 数据工厂日志隔离到自己的工作区中，以最大程度地减少这些日志影响在工作区中收集的其他日志类型的几率。


## <a name="next-steps"></a>后续步骤

* 若要了解有关 Azure 资源日志的信息，请参阅[Azure 资源日志概述](resource-logs-overview.md)。
* 若要创建诊断设置以将资源日志收集到 Log Analytics 工作区，请参阅[创建诊断设置以在 Azure 中收集日志和指标](diagnostic-settings.md)。
