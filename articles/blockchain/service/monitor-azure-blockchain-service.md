---
title: 监控 Azure 区块链服务 （ABS）
description: 通过 Azure 监视器监视 Azure 区块链服务
ms.date: 01/08/2020
ms.topic: article
ms.reviewer: v-umha
ms.openlocfilehash: 6f2a91a8ffce67d3c4008a7587f2787f6446c341
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76293243"
---
# <a name="monitor-azure-blockchain-service-through-azure-monitor"></a>通过 Azure 监视器监视 Azure 区块链服务  

当客户在 Azure 区块链服务 （ABS） 上运行生产级区块链方案时，监视资源的可用性、性能和操作就变得至关重要。 本文介绍了 Azure 区块链服务生成的监视数据，以及如何使用 Azure 监视器的各种功能和集成来分析和警报，以管理生产级环境。  

## <a name="what-is-azure-monitor"></a>说明是 Azure Monitor？

Azure 区块链服务使用 Azure 监视器创建监视数据，Azure 监视器是 Azure 中的完整堆栈监视服务，提供一整套功能来监视 Azure 资源。 有关 Azure 监视器的详细信息，请参阅[使用 Azure 监视器监视 Azure 资源](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource)。
 

以下各节通过描述从 Azure 区块链服务收集的特定数据，并提供用于使用 Azure 工具配置数据收集和分析此数据的示例，从而构建本文。

## <a name="monitor-data-collected-from-azure-blockchain-service"></a>监控从 Azure 区块链服务收集的数据  

Azure 区块链服务收集与其他 Azure 资源相同的监视数据，这在监视 Azure 资源[中](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource#monitoring-data)描述。 有关 Azure 区块链服务创建的日志和指标的详细信息，请参阅[监视 Azure 区块链服务数据参考](#monitor-azure-blockchain-service-data-reference)。

每个 Azure 区块链服务成员资源的 Azure 门户中的概述页包括事务的简短视图，包括处理和处理的请求块。 其中一些数据会自动收集，并在创建 Azure 区块链服务成员资源后可供分析，同时可以通过其他配置启用其他数据收集。

## <a name="diagnostic-settings"></a>诊断设置  

平台指标和活动日志会自动收集，但必须创建诊断设置以收集资源日志或在 Azure 监视器之外转发它们。 有关使用 Azure 门户、CLI 或 PowerShell 创建诊断设置的详细过程，请参阅[创建诊断设置以在 Azure 中收集平台日志和指标](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings)。

创建诊断设置时，可以指定要收集的日志类别。 Azure 区块链服务的类别如下。

**区块链代理日志**– 如果要监视 NGNIX 代理日志，请选择类别。 所有客户交易记录详细信息都可用于审核和调试目的。  

**区块链应用程序日志**– 选择类别来获取托管服务托管的区块链应用程序的日志。 例如，对于 ABS-Quorum 成员，这些日志将是来自 Quorum 本身的日志。  

**指标请求**：选择从 Azure Cosmos DB 到诊断设置中的目标的指标数据的选项，该选项在 Azure 指标中自动收集。 同时收集指标数据和资源日志可将这两种类型的数据一起分析，并在 Azure Monitor 外部发送指标数据。

## <a name="analyze-metric-data"></a>分析指标数据  

您可以使用指标资源管理器分析 Azure 区块链服务的指标，在 ABS 资源边栏选项卡中的"监视"部分下导航到指标选项卡。 有关使用该工具的详细信息[，请参阅使用 Azure 指标资源管理器入门](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started)。 Azure 区块链服务的完整指标位于命名空间 Azure 区块链服务标准指标中。

在添加筛选器或拆分指标时，可以使用**节点**维度，指标基本上提供每个事务节点和 ABS 成员的验证器节点的指标值。

## <a name="analyze-log-data"></a>分析日志数据

以下是一些查询，您可以在日志搜索栏中输入这些查询，以帮助您监视 Azure 区块链服务成员。 这些查询使用[新语言](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)。

要查询区块链应用程序日志中的错误条件，请使用以下查询：

```
BlockchainApplicationLog | where BlockchainMessage contains "ERROR" or BlockchainMessage contains "fatal"

```

要查询区块链代理日志中的错误条件，请使用以下查询  


```
BlockchainProxyLog
| filter Code != 200
| limit 500

```
可以使用 Azure 日志中可用的时间筛选器来筛选特定时间范围的查询。

## <a name="monitor-azure-blockchain-service-data-reference"></a>监控 Azure 区块链服务数据引用  

本文提供了为分析 Azure 区块链服务的性能和可用性而收集的日志和指标数据的参考。  

### <a name="resource-logs"></a>资源日志

所有资源日志共享一个顶级通用架构，其中很少有特定于区块链服务的唯一属性。 您可以参考文章[顶级资源日志架构](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-schema#top-level-resource-logs-schema)，下面介绍 Azure 区块链服务特定属性的详细信息  

下表列出了 Azure 区块链代理日志在 Azure 监视器日志或 Azure 存储中收集时的属性。  


| 属性名称  | 描述 |
|:---|:---|
| time | 操作发生时的日期和时间 (UTC)。 |
| 资源 ID  | 为其启用日志的 Azure 区块链服务资源。  |
| category  |对于 Azure 区块链服务，可能的值是**代理日志**和**应用程序日志**。 |
| operationName  | 此事件表示的操作的名称。   |
| 日志级别  | 默认情况下，Azure 区块链服务启用**信息日志**级别。   |
| 节点位置  | 部署区块链成员的 Azure 区域。  |
| 区块链节点名称  | 执行操作的 Azure 区块链服务成员节点的名称。   |
| EthMethod  | 该方法，由底层区块链协议调用，在Quorum中，它可以eth_sendTransactions，eth_getBlockByNumber等。  |
| 代理  | 代表用户代理的用户代理，例如 Web 浏览器 Mozilla、Edge 等。值的示例包括："Mozilla/5.0 （Linux x64） 节点.js/8.16.0 v8/6.2.414.77"  |
| 代码   | HTTP 错误代码。 通常 4XX 和 5XX 是错误条件。  |
| 节点主机  | 节点的 DNS 名称。   |
| 请求方法名称 | 称为 HTTP 方法，此处的可能值为"为创建成员的 PUT"，用于获取现有成员详细信息的 GET，删除用于删除成员的删除，用于更新成员的 PATCH。   |
| 区块链会员名称  | 用户提供的 Azure 区块链服务成员名称。  |
| 联盟 | 用户提供的联合体的名称。   |
| Remote  | 请求即将到的客户端的 IP。  |
| RequestSize  | 以字节为单位发出的请求的大小。  |
| 请求时间  | 请求的持续时间（以毫秒为单位）。|




下表列出了 Azure 区块链应用程序日志的属性。


| 属性名称  | 描述 |
|:---|:---|
| time | 操作发生时的日期和时间 (UTC)。 |
| 资源 ID  | 为其启用日志的 Azure 区块链服务资源。|
| category  |对于 Azure 区块链服务，可能的值是**代理日志**和**应用程序日志**。  |
| operationName  | 此事件表示的操作的名称。   |
| 日志级别  | 默认情况下，Azure 区块链服务启用**信息日志**级别。   |
| 节点位置  | 部署区块链成员的 Azure 区域。  |
| 区块链节点名称  | 执行操作的 Azure 区块链服务成员节点的名称。   |
| 区块链消息    | 此字段将包含数据普通日志的区块链应用程序日志。 对于 ABS-Quorum，这将具有仲裁日志。 它包含有关日志条目类型的信息，它是信息性、错误、警告和提供有关所执行操作的详细信息的字符串。   |
| TenantID    | Azure 区块链服务的特定于区域的租户。 此字段的格式为https://westlake-rp-prod。<region>.cloudapp.azure.com区域指定已部署的成员的 Azure 区域。       |
| SourceSystem   | 系统填充日志，在这种情况下，它是**Azure**。    |



### <a name="metrics"></a>指标

下表列出了为 Azure 区块链服务收集的平台指标。 所有指标都存储在命名空间 Azure**区块链服务**标准指标中。

有关所有 Azure 监视器支持指标的列表（包括 Azure 区块链服务），请参阅[Azure 监视器支持的指标](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported)。

### <a name="blockchain-metrics"></a>区块链指标

下表指定为 Azure 区块链服务成员资源收集的区块链指标列表。


| 指标名称 | 单位  |  聚合类型| 描述   |
|---|---|---|---|
| 挂起的事务数   | Count  |  平均值 | 等待开采的事务数。   |
| 已处理的块数   | Count  | SUM  |  在每个时间间隔内处理的块数。 目前块大小为 5 秒，因此在一分钟内每个节点将在 5 分钟内处理 12 个块和 60 个块。   |
|已处理的事务数    | Count  | SUM  | 块中处理的事务数。    |
|已排队的事务数    |  Count | 平均值  | 无法立即挖掘的交易记录数。 这可能是因为他们到达顺序，未来一个正在等待以前的事务到达。 或者，它可以是两个事务具有相同的数字，只使用一次 （nonce） 和相同的气体值，因此第二个事务不能开采。   |

### <a name="connection-metrics"></a>连接指标  

下表列出了为 Azure 区块链服务成员资源收集的不同连接指标。 这些是 NGINX 代理指标。


| 指标名称 | 单位  |  聚合类型| 描述 |
|---|---|---|---|
| 已接受的连接数   | Count  |  SUM | 接受的客户端连接的总数。   |
| 活动连接数  | Count  | 平均值  |  当前活动客户端连接数，包括等待连接。    |
|已处理的连接数    | Count  | SUM  | 已处理的连接总数。 通常，除非达到某些资源限制，否则参数值与接受的连接相同。     |
|已处理的请求数     |  Count | SUM  | 客户端请求的总数。  |


### <a name="performance-metrics"></a>性能指标

下表列出了为 Azure 区块链成员资源的每个节点收集的性能指标。  


| 指标名称 | 单位  |  聚合类型| 描述   |
|---|---|---|---|
| CPU 使用率百分比   | 百分比  |  Max | CPU 使用率的百分比。     |
| IO 读取字节数   | 千字节   | SUM  |  跨区块链成员资源所有节点的 IO 读取字节的总和。      |
|IO 写入字节数     | 千字节   | SUM  | 跨区块链成员资源的所有节点的 IO 写入字节的总和。     |
|内存限制       |  G   | 平均值    | 每个节点的区块链进程的最大内存。 |
|内存用量     | G  |  平均值 | 所有节点的平均使用的内存量。  |
| 内存使用率百分比     | 百分比   | 平均值  |  在所有节点上平均使用的内存百分比。       |
|存储使用率      | G   | 平均值  | 在所有节点上平均使用的存储 GB。       |


## <a name="next-steps"></a>后续步骤

详细了解[区块链数据管理器](https://docs.microsoft.com/azure/blockchain/service/data-manager)，以捕获区块链数据并将其转换为 Azure 事件网格。
