---
title: 监视 Azure 区块链服务（ABS）
description: 通过 Azure Monitor 监视 Azure 区块链服务
ms.date: 01/08/2020
ms.topic: article
ms.reviewer: v-umha
ms.openlocfilehash: 6f2a91a8ffce67d3c4008a7587f2787f6446c341
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2020
ms.locfileid: "76293243"
---
# <a name="monitor-azure-blockchain-service-through-azure-monitor"></a>通过 Azure Monitor 监视 Azure 区块链服务  

当客户在 Azure 区块链服务（ABS）上运行生产级区块链方案时，监视资源的可用性、性能和操作就变得至关重要。 本文介绍 Azure 区块链服务生成的监视数据，以及如何使用 Azure Monitor 的各种功能和集成来对生产级别环境进行分析和发出警报。  

## <a name="what-is-azure-monitor"></a>说明是 Azure Monitor？

Azure 区块链 Service 使用 Azure Monitor 创建监视数据，该服务是 Azure 中的一种完整的堆栈监视服务，可提供一组完整的功能来监视 Azure 资源。 有关 Azure Monitor 的详细信息，请参阅[通过 Azure Monitor 监视 Azure 资源](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource)。
 

以下各节将介绍从 Azure 区块链服务中收集的特定数据，并提供有关使用 Azure tools 配置数据收集和分析此数据的示例。

## <a name="monitor-data-collected-from-azure-blockchain-service"></a>监视从 Azure 区块链服务收集的数据  

Azure 区块链服务会收集与其他 Azure 资源相同的监视数据，如监视 Azure 资源的[数据](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource#monitoring-data)中所述。 请参阅[监视 Azure 区块链 service 数据参考](#monitor-azure-blockchain-service-data-reference)，了解 Azure 区块链服务创建的日志和指标的详细参考。

每个 Azure 区块链服务成员资源的 Azure 门户中的 "概述" 页包括事务的简要视图，包括已处理的请求和处理的块。 在创建 Azure 区块链服务成员资源后，会自动收集这些数据，并可进行分析，同时可以使用其他配置启用其他数据收集。

## <a name="diagnostic-settings"></a>诊断设置  

平台指标和活动日志会自动收集，但你必须创建诊断设置以收集资源日志，或将其转发到 Azure Monitor 之外。 有关使用 Azure 门户、CLI 或 PowerShell 创建诊断设置的详细过程，请参阅[创建诊断设置以在 Azure 中收集平台日志和指标](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings)。

创建诊断设置时，可指定要收集的日志类别。 下面列出了 Azure 区块链服务的类别。

**区块链代理日志**–如果要监视 NGNIX 代理日志，请选择类别。 所有客户事务详细信息都可用于审核和调试目的。  

**区块链应用程序日志**–选择类别以获取托管服务托管的区块链应用程序的日志。 例如，对于 ABS 仲裁成员，这些日志将是来自仲裁本身的日志。  

"**指标请求**"：选择此选项可将指标数据从 Azure Cosmos DB 收集到诊断设置中的目标，该设置是在 Azure 指标中自动收集的。 收集包含资源日志的指标数据，将这两种类型的数据组合在一起，并发送 Azure Monitor 之外的指标数据。

## <a name="analyze-metric-data"></a>分析指标数据  

可以通过指标资源管理器分析 Azure 区块链服务的指标，导航到 "ABS 资源" 边栏选项卡中 "监视" 部分下的 "指标" 选项卡。 有关使用该工具的详细信息，请参阅[Azure 指标资源管理器](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started)入门。 Azure 区块链服务的完整指标位于命名空间 Azure 区块链 Service 标准指标中。

添加筛选器或拆分度量值时，可以使用 "**节点**维度"，它基本上提供每个事务节点的指标值和 ABS 成员的验证器节点。

## <a name="analyze-log-data"></a>分析日志数据

你可以在日志搜索栏中输入一些查询，以帮助你监视 Azure 区块链服务成员。 这些查询使用[新语言](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)。

若要查询区块链应用程序日志中的错误条件，请使用以下查询：

```
BlockchainApplicationLog | where BlockchainMessage contains "ERROR" or BlockchainMessage contains "fatal"

```

若要查询区块链代理日志中的错误条件，请使用以下查询  


```
BlockchainProxyLog
| filter Code != 200
| limit 500

```
可以使用 Azure 日志中提供的时间筛选器来筛选特定时间范围内的查询。

## <a name="monitor-azure-blockchain-service-data-reference"></a>监视 Azure 区块链服务数据引用  

本文提供了为分析 Azure 区块链服务的性能和可用性而收集的日志和指标数据的参考。  

### <a name="resource-logs"></a>资源日志

所有资源日志共享具有几个特定于区块链服务的唯一属性的顶级公共架构。 可以参阅[顶级资源日志架构](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-schema#top-level-resource-logs-schema)一文，下面涵盖了 Azure 区块链服务特定属性的详细信息  

下表列出了在 Azure Monitor 日志或 Azure 存储中收集 Azure 区块链代理日志时，这些日志的属性。  


| 属性名称  | Description |
|:---|:---|
| time | 操作发生时的日期和时间 (UTC)。 |
| resourceID  | 为其启用了日志的 Azure 区块链服务资源。  |
| category  |对于 Azure 区块链服务，可能的值为**Proxylogs**和**Applicationlogs**。 |
| operationName  | 此事件表示的操作的名称。   |
| 日志级别  | 默认情况下，Azure 区块链服务启用**信息**日志级别。   |
| NodeLocation  | 部署区块链成员的 Azure 区域。  |
| BlockchainNodeName  | 在其上执行操作的 Azure 区块链服务成员的节点名称。   |
| EthMethod  | 方法（由基础区块链协议调用）在仲裁中，可以 eth_sendTransactions、eth_getBlockByNumber 等。  |
| 代理  | 代表用户（如 web 浏览器 Mozilla、边缘等）的用户代理。值的示例包括： "Mozilla/5.0 （Linux x64） node.js/8.16.0 v8/6.2.414.77"  |
| 代码   | HTTP 错误代码。 通常，4XX 和5XX 是错误条件。  |
| NodeHost  | 节点的 DNS 名称。   |
| RequestMethodName | 调用 HTTP 方法时，此处的可能值为 "创建成员"、"获取现有成员的详细信息"、"删除成员"、"删除成员" 和 "更新成员的修补程序"。   |
| BlockchainMemberName  | 用户提供的 Azure 区块链服务成员名称。  |
| 联盟 | 用户提供的联合会的名称。   |
| Remote  | 发出请求的客户端的 IP。  |
| RequestSize  | 发出的请求大小（以字节为单位）。  |
| RequestTime  | 请求的持续时间（以毫秒为单位）。|




下表列出了 Azure 区块链应用程序日志的属性。


| 属性名称  | Description |
|:---|:---|
| time | 操作发生时的日期和时间 (UTC)。 |
| resourceID  | 为其启用了日志的 Azure 区块链服务资源。|
| category  |对于 Azure 区块链服务，可能的值为**Proxylogs**和**Applicationlogs**。  |
| operationName  | 此事件表示的操作的名称。   |
| 日志级别  | 默认情况下，Azure 区块链服务启用**信息**日志级别。   |
| NodeLocation  | 部署区块链成员的 Azure 区域。  |
| BlockchainNodeName  | 在其上执行操作的 Azure 区块链服务成员的节点名称。   |
| BlockchainMessage    | 此字段将包含作为数据普通日志的区块链应用程序日志。 对于 ABS 仲裁，这会有仲裁日志。 它包含有关 "信息"、"错误"、"警告" 和 "字符串" 的信息，其中提供了有关所执行操作的详细信息。   |
| TenantID    | Azure 区块链服务的特定于区域的租户。 此字段的格式为 https://westlake-rp-prod 。<region> ，其中区域指定部署的成员的 Azure 区域。       |
| SourceSystem   | 系统将填充日志，在这种情况下，它是**Azure**。    |



### <a name="metrics"></a>指标

下表列出了为 Azure 区块链服务收集的平台指标。 所有指标都存储在命名空间**Azure 区块链 Service**标准指标中。

有关所有 Azure Monitor 支持的指标（包括 Azure 区块链 Service）的列表，请参阅[Azure Monitor 支持的指标](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported)。

### <a name="blockchain-metrics"></a>区块链指标

下表指定为 Azure 区块链服务成员资源收集的区块链度量值的列表。


| 指标名称 | 单位  |  聚合类型| Description   |
|---|---|---|---|
| 挂起的事务   | 计数  |  平均值 | 正在等待挖掘的事务数。   |
| 处理的块   | 计数  | SUM  |  每个时间间隔内处理的块数。 块大小当前为5秒，因此，每个节点将在5分钟内处理12个块和60块。   |
|处理的事务    | 计数  | SUM  | 块中处理的事务数。    |
|排队事务    |  计数 | 平均值  | 无法立即挖掘的事务数。 这可能是因为它们未按顺序到达，而未来的事务正在等待前一个事务到达。 也可以是两个事务，其数字只使用一次（nonce），同一气体值为，因此第二个事务无法挖掘。   |

### <a name="connection-metrics"></a>连接指标  

下表列出了为 Azure 区块链服务成员资源收集的不同连接指标。 这些是 NGINX 的代理指标。


| 指标名称 | 单位  |  聚合类型| Description |
|---|---|---|---|
| 接受的连接   | 计数  |  SUM | 接受的客户端连接总数。   |
| 活动连接数  | 计数  | 平均值  |  当前活动客户端连接数，包括等待连接数。    |
|处理的连接    | 计数  | SUM  | 已处理连接的总数。 通常，参数值与接受的连接相同，除非已达到某些资源的限制。     |
|处理的请求     |  计数 | SUM  | 客户端请求总数。  |


### <a name="performance-metrics"></a>性能指标

下表列出了为 Azure 区块链成员资源的每个节点收集的性能度量值。  


| 指标名称 | 单位  |  聚合类型| Description   |
|---|---|---|---|
| CPU 使用率百分比   | 百分比  |  最多 | CPU 使用率的百分比。     |
| IO 读取字节数   | 千字节   | SUM  |  区块链成员资源的所有节点之间 IO 读取字节数之和。      |
|IO 写入字节数     | 千字节   | SUM  | IO 在区块链成员资源的所有节点之间写入字节数之和。     |
|内存限制       |  字节   | 平均值    | 可用于每个节点的区块链进程的最大内存。 |
|内存用量     | 字节  |  平均值 | 在所有节点上平均使用的内存量。  |
| 内存使用率百分比     | 百分比   | 平均值  |  在所有节点上平均使用的内存的百分比。       |
|存储使用情况      | 字节   | 平均值  | 在所有节点上平均使用的存储空间（GB）。       |


## <a name="next-steps"></a>后续步骤

了解有关[区块链数据管理器](https://docs.microsoft.com/azure/blockchain/service/data-manager)的详细信息，以便捕获区块链数据并将其转换为 Azure 事件网格。
