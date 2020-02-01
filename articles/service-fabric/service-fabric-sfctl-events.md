---
title: Azure Service Fabric sfctl 事件
description: 介绍 Service Fabric CLI sfctl events 命令。
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 635bddef1a98d6ed2b112662d8c9c44fe65e9e32
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906103"
---
# <a name="sfctl-events"></a>sfctl 事件
从事件存储中检索事件（如果已安装 EventStore 服务）。

EventStore 系统服务可以通过配置升级添加到运行 > = 6.4 的任何 SFRP 群集。 请检查以下 url\: https\://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-eventstore。

## <a name="commands"></a>命令

|命令|Description|
| --- | --- |
| 所有应用程序-列表 | 获取与应用程序相关的所有事件。 |
| 所有节点-列表 | 获取与节点相关的所有事件。 |
| 所有分区-列表 | 获取与分区相关的所有事件。 |
| 所有服务-列表 | 获取与服务相关的所有事件。 |
| 应用程序-列表 | 获取与应用程序相关的事件。 |
| 群集-列表 | 获取与群集相关的所有事件。 |
| 节点列表 | 获取与节点相关的事件。 |
| 分区-所有副本-列表 | 获取分区的所有副本相关事件。 |
| 分区列表 | 获取与分区相关的事件。 |
| 分区副本-列表 | 获取与分区副本相关的事件。 |
| 服务-列表 | 获取与服务相关的事件。 |

## <a name="sfctl-events-all-applications-list"></a>sfctl 事件-所有应用程序-列表
获取与应用程序相关的所有事件。

响应是 ApplicationEvent 对象的列表。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --结束时间-utc [必需] | 查找查询的结束时间，格式为 ISO UTC yyyy-mm-dd-Yyyy-mm-ddthh\:MM\:ssZ。 |
| --启动时间-utc [必需] | 查找查询的开始时间，采用 ISO UTC yyyy-MM-Yyyy-mm-ddthh\:MM\:ssZ。 |
| --事件类型-筛选器 | 这是一个逗号分隔的字符串，用于指定只应包括在响应中的 FabricEvents 类型。 |
| --exclude-事件 | 如果传递了 true，则此参数将禁用 AnalysisEvents 的检索。 |
| --skip-相关-查找 | 如果传递了 true，则此参数将禁用 CorrelatedEvents 信息的搜索。 否则，每个 FabricEvent 中的 CorrelationEvents "已处理" 和 "HasCorrelatedEvents" 字段。 |
| --timeout -t | 用于执行操作的服务器超时时间（秒）。 此超时值指定客户端愿意等待请求的操作完成的持续时间。 此参数的默认值为60秒。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 获取完整的调试日志。 |

## <a name="sfctl-events-all-nodes-list"></a>sfctl 事件所有节点-列表
获取与节点相关的所有事件。

响应是 NodeEvent 对象的列表。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --结束时间-utc [必需] | 查找查询的结束时间，格式为 ISO UTC yyyy-mm-dd-Yyyy-mm-ddthh\:MM\:ssZ。 |
| --启动时间-utc [必需] | 查找查询的开始时间，采用 ISO UTC yyyy-MM-Yyyy-mm-ddthh\:MM\:ssZ。 |
| --事件类型-筛选器 | 这是一个逗号分隔的字符串，用于指定只应包括在响应中的 FabricEvents 类型。 |
| --exclude-事件 | 如果传递了 true，则此参数将禁用 AnalysisEvents 的检索。 |
| --skip-相关-查找 | 如果传递了 true，则此参数将禁用 CorrelatedEvents 信息的搜索。 否则，每个 FabricEvent 中的 CorrelationEvents "已处理" 和 "HasCorrelatedEvents" 字段。 |
| --timeout -t | 用于执行操作的服务器超时时间（秒）。 此超时值指定客户端愿意等待请求的操作完成的持续时间。 此参数的默认值为60秒。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 获取完整的调试日志。 |

## <a name="sfctl-events-all-partitions-list"></a>sfctl 事件所有分区-列表
获取与分区相关的所有事件。

响应是 PartitionEvent 对象的列表。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --结束时间-utc [必需] | 查找查询的结束时间，格式为 ISO UTC yyyy-mm-dd-Yyyy-mm-ddthh\:MM\:ssZ。 |
| --启动时间-utc [必需] | 查找查询的开始时间，采用 ISO UTC yyyy-MM-Yyyy-mm-ddthh\:MM\:ssZ。 |
| --事件类型-筛选器 | 这是一个逗号分隔的字符串，用于指定只应包括在响应中的 FabricEvents 类型。 |
| --exclude-事件 | 如果传递了 true，则此参数将禁用 AnalysisEvents 的检索。 |
| --skip-相关-查找 | 如果传递了 true，则此参数将禁用 CorrelatedEvents 信息的搜索。 否则，每个 FabricEvent 中的 CorrelationEvents "已处理" 和 "HasCorrelatedEvents" 字段。 |
| --timeout -t | 用于执行操作的服务器超时时间（秒）。 此超时值指定客户端愿意等待请求的操作完成的持续时间。 此参数的默认值为60秒。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 获取完整的调试日志。 |

## <a name="sfctl-events-all-services-list"></a>sfctl 事件所有服务-列表
获取与服务相关的所有事件。

响应是 ServiceEvent 对象的列表。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --结束时间-utc [必需] | 查找查询的结束时间，格式为 ISO UTC yyyy-mm-dd-Yyyy-mm-ddthh\:MM\:ssZ。 |
| --启动时间-utc [必需] | 查找查询的开始时间，采用 ISO UTC yyyy-MM-Yyyy-mm-ddthh\:MM\:ssZ。 |
| --事件类型-筛选器 | 这是一个逗号分隔的字符串，用于指定只应包括在响应中的 FabricEvents 类型。 |
| --exclude-事件 | 如果传递了 true，则此参数将禁用 AnalysisEvents 的检索。 |
| --skip-相关-查找 | 如果传递了 true，则此参数将禁用 CorrelatedEvents 信息的搜索。 否则，每个 FabricEvent 中的 CorrelationEvents "已处理" 和 "HasCorrelatedEvents" 字段。 |
| --timeout -t | 用于执行操作的服务器超时时间（秒）。 此超时值指定客户端愿意等待请求的操作完成的持续时间。 此参数的默认值为60秒。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 获取完整的调试日志。 |

## <a name="sfctl-events-application-list"></a>sfctl events 应用程序-列表
获取与应用程序相关的事件。

响应是 ApplicationEvent 对象的列表。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --application-id [必需] | 应用程序的标识。 这通常是不带“fabric\:”URI 方案的应用程序全名。 从版本 6.0 开始，分层名称以“\~”字符隔开。 例如，如果应用程序名称为“fabric\:/myapp/app1”，则 6.0 及更高版本中的应用程序标识为“myapp\~app1”，在以前的版本中为“myapp/app1”。 |
| --结束时间-utc [必需] | 查找查询的结束时间，格式为 ISO UTC yyyy-mm-dd-Yyyy-mm-ddthh\:MM\:ssZ。 |
| --启动时间-utc [必需] | 查找查询的开始时间，采用 ISO UTC yyyy-MM-Yyyy-mm-ddthh\:MM\:ssZ。 |
| --事件类型-筛选器 | 这是一个逗号分隔的字符串，用于指定只应包括在响应中的 FabricEvents 类型。 |
| --exclude-事件 | 如果传递了 true，则此参数将禁用 AnalysisEvents 的检索。 |
| --skip-相关-查找 | 如果传递了 true，则此参数将禁用 CorrelatedEvents 信息的搜索。 否则，每个 FabricEvent 中的 CorrelationEvents "已处理" 和 "HasCorrelatedEvents" 字段。 |
| --timeout -t | 用于执行操作的服务器超时时间（秒）。 此超时值指定客户端愿意等待请求的操作完成的持续时间。 此参数的默认值为60秒。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 获取完整的调试日志。 |

## <a name="sfctl-events-cluster-list"></a>sfctl 事件群集-列表
获取与群集相关的所有事件。

响应是 ClusterEvent 对象的列表。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --结束时间-utc [必需] | 查找查询的结束时间，格式为 ISO UTC yyyy-mm-dd-Yyyy-mm-ddthh\:MM\:ssZ。 |
| --启动时间-utc [必需] | 查找查询的开始时间，采用 ISO UTC yyyy-MM-Yyyy-mm-ddthh\:MM\:ssZ。 |
| --事件类型-筛选器 | 这是一个逗号分隔的字符串，用于指定只应包括在响应中的 FabricEvents 类型。 |
| --exclude-事件 | 如果传递了 true，则此参数将禁用 AnalysisEvents 的检索。 |
| --skip-相关-查找 | 如果传递了 true，则此参数将禁用 CorrelatedEvents 信息的搜索。 否则，每个 FabricEvent 中的 CorrelationEvents "已处理" 和 "HasCorrelatedEvents" 字段。 |
| --timeout -t | 用于执行操作的服务器超时时间（秒）。 此超时值指定客户端愿意等待请求的操作完成的持续时间。 此参数的默认值为60秒。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 获取完整的调试日志。 |

## <a name="sfctl-events-node-list"></a>sfctl 事件节点-列表
获取与节点相关的事件。

响应是 NodeEvent 对象的列表。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --结束时间-utc [必需] | 查找查询的结束时间，格式为 ISO UTC yyyy-mm-dd-Yyyy-mm-ddthh\:MM\:ssZ。 |
| --node-name [必需] | 节点的名称。 |
| --启动时间-utc [必需] | 查找查询的开始时间，采用 ISO UTC yyyy-MM-Yyyy-mm-ddthh\:MM\:ssZ。 |
| --事件类型-筛选器 | 这是一个逗号分隔的字符串，用于指定只应包括在响应中的 FabricEvents 类型。 |
| --exclude-事件 | 如果传递了 true，则此参数将禁用 AnalysisEvents 的检索。 |
| --skip-相关-查找 | 如果传递了 true，则此参数将禁用 CorrelatedEvents 信息的搜索。 否则，每个 FabricEvent 中的 CorrelationEvents "已处理" 和 "HasCorrelatedEvents" 字段。 |
| --timeout -t | 用于执行操作的服务器超时时间（秒）。 此超时值指定客户端愿意等待请求的操作完成的持续时间。 此参数的默认值为60秒。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 获取完整的调试日志。 |

## <a name="sfctl-events-partition-all-replicas-list"></a>sfctl 事件分区-所有副本-列表
获取分区的所有副本相关事件。

响应是 ReplicaEvent 对象的列表。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --结束时间-utc [必需] | 查找查询的结束时间，格式为 ISO UTC yyyy-mm-dd-Yyyy-mm-ddthh\:MM\:ssZ。 |
| --partition-id   [必需] | 分区的标识。 |
| --启动时间-utc [必需] | 查找查询的开始时间，采用 ISO UTC yyyy-MM-Yyyy-mm-ddthh\:MM\:ssZ。 |
| --事件类型-筛选器 | 这是一个逗号分隔的字符串，用于指定只应包括在响应中的 FabricEvents 类型。 |
| --exclude-事件 | 如果传递了 true，则此参数将禁用 AnalysisEvents 的检索。 |
| --skip-相关-查找 | 如果传递了 true，则此参数将禁用 CorrelatedEvents 信息的搜索。 否则，每个 FabricEvent 中的 CorrelationEvents "已处理" 和 "HasCorrelatedEvents" 字段。 |
| --timeout -t | 用于执行操作的服务器超时时间（秒）。 此超时值指定客户端愿意等待请求的操作完成的持续时间。 此参数的默认值为60秒。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 获取完整的调试日志。 |

## <a name="sfctl-events-partition-list"></a>sfctl 事件分区-列表
获取与分区相关的事件。

响应是 PartitionEvent 对象的列表。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --结束时间-utc [必需] | 查找查询的结束时间，格式为 ISO UTC yyyy-mm-dd-Yyyy-mm-ddthh\:MM\:ssZ。 |
| --partition-id   [必需] | 分区的标识。 |
| --启动时间-utc [必需] | 查找查询的开始时间，采用 ISO UTC yyyy-MM-Yyyy-mm-ddthh\:MM\:ssZ。 |
| --事件类型-筛选器 | 这是一个逗号分隔的字符串，用于指定只应包括在响应中的 FabricEvents 类型。 |
| --exclude-事件 | 如果传递了 true，则此参数将禁用 AnalysisEvents 的检索。 |
| --skip-相关-查找 | 如果传递了 true，则此参数将禁用 CorrelatedEvents 信息的搜索。 否则，每个 FabricEvent 中的 CorrelationEvents "已处理" 和 "HasCorrelatedEvents" 字段。 |
| --timeout -t | 用于执行操作的服务器超时时间（秒）。 此超时值指定客户端愿意等待请求的操作完成的持续时间。 此参数的默认值为60秒。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 获取完整的调试日志。 |

## <a name="sfctl-events-partition-replica-list"></a>sfctl 事件分区-副本-列表
获取与分区副本相关的事件。

响应是 ReplicaEvent 对象的列表。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --结束时间-utc [必需] | 查找查询的结束时间，格式为 ISO UTC yyyy-mm-dd-Yyyy-mm-ddthh\:MM\:ssZ。 |
| --partition-id   [必需] | 分区的标识。 |
| --replica-id [必需] | 副本的标识符。 |
| --启动时间-utc [必需] | 查找查询的开始时间，采用 ISO UTC yyyy-MM-Yyyy-mm-ddthh\:MM\:ssZ。 |
| --事件类型-筛选器 | 这是一个逗号分隔的字符串，用于指定只应包括在响应中的 FabricEvents 类型。 |
| --exclude-事件 | 如果传递了 true，则此参数将禁用 AnalysisEvents 的检索。 |
| --skip-相关-查找 | 如果传递了 true，则此参数将禁用 CorrelatedEvents 信息的搜索。 否则，每个 FabricEvent 中的 CorrelationEvents "已处理" 和 "HasCorrelatedEvents" 字段。 |
| --timeout -t | 用于执行操作的服务器超时时间（秒）。 此超时值指定客户端愿意等待请求的操作完成的持续时间。 此参数的默认值为60秒。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 获取完整的调试日志。 |

## <a name="sfctl-events-service-list"></a>sfctl 事件服务-列表
获取与服务相关的事件。

响应是 ServiceEvent 对象的列表。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --结束时间-utc [必需] | 查找查询的结束时间，格式为 ISO UTC yyyy-mm-dd-Yyyy-mm-ddthh\:MM\:ssZ。 |
| --service-id     [必需] | 服务的标识。 此 ID 通常是不带“fabric\:”URI 方案的服务全名。 从版本 6.0 开始，分层名称以“\~”字符隔开。 例如，如果服务名称为“fabric\:/myapp/app1/svc1”，则 6.0 及更高版本中的服务标识为“myapp\~app1\~svc1”，在以前的版本中为“myapp/app1/svc1”。 |
| --启动时间-utc [必需] | 查找查询的开始时间，采用 ISO UTC yyyy-MM-Yyyy-mm-ddthh\:MM\:ssZ。 |
| --事件类型-筛选器 | 这是一个逗号分隔的字符串，用于指定只应包括在响应中的 FabricEvents 类型。 |
| --exclude-事件 | 如果传递了 true，则此参数将禁用 AnalysisEvents 的检索。 |
| --skip-相关-查找 | 如果传递了 true，则此参数将禁用 CorrelatedEvents 信息的搜索。 否则，每个 FabricEvent 中的 CorrelationEvents "已处理" 和 "HasCorrelatedEvents" 字段。 |
| --timeout -t | 用于执行操作的服务器超时时间（秒）。 此超时值指定客户端愿意等待请求的操作完成的持续时间。 此参数的默认值为60秒。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 获取完整的调试日志。 |

