---
title: Azure Service Fabric CLI - sfctl events
description: 介绍 Service Fabric CLI sfctl events 命令。
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 635bddef1a98d6ed2b112662d8c9c44fe65e9e32
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "76906103"
---
# <a name="sfctl-events"></a>sfctl events
从事件存储中检索事件（如果已安装 EventStore 服务）。

可以通过对运行 6.4 或更高版本的任何 SFRP 群集进行配置升级，来添加 EventStore 系统服务。 请访问如下 url\: https\://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-eventstore。

## <a name="commands"></a>命令

|Command|说明|
| --- | --- |
| all-applications-list | 获取应用程序相关的所有事件。 |
| all-nodes-list | 获取节点相关的所有事件。 |
| all-partitions-list | 获取分区相关的所有事件。 |
| all-services-list | 获取服务相关的所有事件。 |
| application-list | 获取应用程序相关的事件。 |
| cluster-list | 获取群集相关的所有事件。 |
| node-list | 获取节点相关的事件。 |
| partition-all-replicas-list | 获取分区的所有副本相关事件。 |
| partition-list | 获取分区相关的事件。 |
| partition-replica-list | 获取分区副本相关的事件。 |
| service-list | 获取服务相关的事件。 |

## <a name="sfctl-events-all-applications-list"></a>sfctl events all-applications-list
获取应用程序相关的所有事件。

响应是 ApplicationEvent 对象的列表。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --end-time-utc   [必需] | 查找查询的结束时间，采用 ISO UTC yyyy-MM-ddTHH\:mm\:ssZ 格式。 |
| --start-time-utc [必需] | 查找查询的开始时间，采用 ISO UTC yyyy-MM-ddTHH\:mm\:ssZ 格式。 |
| --events-types-filter | 这是一个逗号分隔的字符串，指定只应在响应中包含 FabricEvents 类型。 |
| --exclude-analysis-events | 如果传递了 true，此参数将禁用 AnalysisEvents 的检索。 |
| --skip-correlation-lookup | 如果传递了 true，此参数将禁用 CorrelatedEvents 信息的搜索。 否则，将处理 CorrelationEvents，并填充每个 FabricEvent 中的 HasCorrelatedEvents 字段。 |
| --timeout -t | 执行操作的服务器超时，以秒为单位。 此超时指定客户端可以等待请求的操作完成的持续时间。 此参数的默认值为 60 秒。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug | 提高日志记录详细程度以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 获取完整的调试日志。 |

## <a name="sfctl-events-all-nodes-list"></a>sfctl events all-nodes-list
获取节点相关的所有事件。

响应是 NodeEvent 对象的列表。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --end-time-utc   [必需] | 查找查询的结束时间，采用 ISO UTC yyyy-MM-ddTHH\:mm\:ssZ 格式。 |
| --start-time-utc [必需] | 查找查询的开始时间，采用 ISO UTC yyyy-MM-ddTHH\:mm\:ssZ 格式。 |
| --events-types-filter | 这是一个逗号分隔的字符串，指定只应在响应中包含 FabricEvents 类型。 |
| --exclude-analysis-events | 如果传递了 true，此参数将禁用 AnalysisEvents 的检索。 |
| --skip-correlation-lookup | 如果传递了 true，此参数将禁用 CorrelatedEvents 信息的搜索。 否则，将处理 CorrelationEvents，并填充每个 FabricEvent 中的 HasCorrelatedEvents 字段。 |
| --timeout -t | 执行操作的服务器超时，以秒为单位。 此超时指定客户端可以等待请求的操作完成的持续时间。 此参数的默认值为 60 秒。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug | 提高日志记录详细程度以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 获取完整的调试日志。 |

## <a name="sfctl-events-all-partitions-list"></a>sfctl events all-partitions-list
获取分区相关的所有事件。

响应是 PartitionEvent 对象的列表。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --end-time-utc   [必需] | 查找查询的结束时间，采用 ISO UTC yyyy-MM-ddTHH\:mm\:ssZ 格式。 |
| --start-time-utc [必需] | 查找查询的开始时间，采用 ISO UTC yyyy-MM-ddTHH\:mm\:ssZ 格式。 |
| --events-types-filter | 这是一个逗号分隔的字符串，指定只应在响应中包含 FabricEvents 类型。 |
| --exclude-analysis-events | 如果传递了 true，此参数将禁用 AnalysisEvents 的检索。 |
| --skip-correlation-lookup | 如果传递了 true，此参数将禁用 CorrelatedEvents 信息的搜索。 否则，将处理 CorrelationEvents，并填充每个 FabricEvent 中的 HasCorrelatedEvents 字段。 |
| --timeout -t | 执行操作的服务器超时，以秒为单位。 此超时指定客户端可以等待请求的操作完成的持续时间。 此参数的默认值为 60 秒。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug | 提高日志记录详细程度以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 获取完整的调试日志。 |

## <a name="sfctl-events-all-services-list"></a>sfctl events all-services-list
获取服务相关的所有事件。

响应是 ServiceEvent 对象的列表。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --end-time-utc   [必需] | 查找查询的结束时间，采用 ISO UTC yyyy-MM-ddTHH\:mm\:ssZ 格式。 |
| --start-time-utc [必需] | 查找查询的开始时间，采用 ISO UTC yyyy-MM-ddTHH\:mm\:ssZ 格式。 |
| --events-types-filter | 这是一个逗号分隔的字符串，指定只应在响应中包含 FabricEvents 类型。 |
| --exclude-analysis-events | 如果传递了 true，此参数将禁用 AnalysisEvents 的检索。 |
| --skip-correlation-lookup | 如果传递了 true，此参数将禁用 CorrelatedEvents 信息的搜索。 否则，将处理 CorrelationEvents，并填充每个 FabricEvent 中的 HasCorrelatedEvents 字段。 |
| --timeout -t | 执行操作的服务器超时，以秒为单位。 此超时指定客户端可以等待请求的操作完成的持续时间。 此参数的默认值为 60 秒。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug | 提高日志记录详细程度以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 获取完整的调试日志。 |

## <a name="sfctl-events-application-list"></a>sfctl events application-list
获取应用程序相关的事件。

响应是 ApplicationEvent 对象的列表。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --application-id [必需] | 应用程序的标识。 这通常是不带“fabric\:”URI 方案的应用程序全名。 从版本 6.0 开始，分层名称以“\~”字符隔开。 例如，如果应用程序名称为“fabric\:/myapp/app1”，则 6.0 及更高版本中的应用程序标识为“myapp\~app1”，在以前的版本中为“myapp/app1”。 |
| --end-time-utc   [必需] | 查找查询的结束时间，采用 ISO UTC yyyy-MM-ddTHH\:mm\:ssZ 格式。 |
| --start-time-utc [必需] | 查找查询的开始时间，采用 ISO UTC yyyy-MM-ddTHH\:mm\:ssZ 格式。 |
| --events-types-filter | 这是一个逗号分隔的字符串，指定只应在响应中包含 FabricEvents 类型。 |
| --exclude-analysis-events | 如果传递了 true，此参数将禁用 AnalysisEvents 的检索。 |
| --skip-correlation-lookup | 如果传递了 true，此参数将禁用 CorrelatedEvents 信息的搜索。 否则，将处理 CorrelationEvents，并填充每个 FabricEvent 中的 HasCorrelatedEvents 字段。 |
| --timeout -t | 执行操作的服务器超时，以秒为单位。 此超时指定客户端可以等待请求的操作完成的持续时间。 此参数的默认值为 60 秒。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug | 提高日志记录详细程度以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 获取完整的调试日志。 |

## <a name="sfctl-events-cluster-list"></a>sfctl events cluster-list
获取群集相关的所有事件。

响应是 ClusterEvent 对象的列表。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --end-time-utc   [必需] | 查找查询的结束时间，采用 ISO UTC yyyy-MM-ddTHH\:mm\:ssZ 格式。 |
| --start-time-utc [必需] | 查找查询的开始时间，采用 ISO UTC yyyy-MM-ddTHH\:mm\:ssZ 格式。 |
| --events-types-filter | 这是一个逗号分隔的字符串，指定只应在响应中包含 FabricEvents 类型。 |
| --exclude-analysis-events | 如果传递了 true，此参数将禁用 AnalysisEvents 的检索。 |
| --skip-correlation-lookup | 如果传递了 true，此参数将禁用 CorrelatedEvents 信息的搜索。 否则，将处理 CorrelationEvents，并填充每个 FabricEvent 中的 HasCorrelatedEvents 字段。 |
| --timeout -t | 执行操作的服务器超时，以秒为单位。 此超时指定客户端可以等待请求的操作完成的持续时间。 此参数的默认值为 60 秒。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug | 提高日志记录详细程度以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 获取完整的调试日志。 |

## <a name="sfctl-events-node-list"></a>sfctl events node-list
获取节点相关的事件。

响应是 NodeEvent 对象的列表。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --end-time-utc   [必需] | 查找查询的结束时间，采用 ISO UTC yyyy-MM-ddTHH\:mm\:ssZ 格式。 |
| --node-name [必需] | 节点的名称。 |
| --start-time-utc [必需] | 查找查询的开始时间，采用 ISO UTC yyyy-MM-ddTHH\:mm\:ssZ 格式。 |
| --events-types-filter | 这是一个逗号分隔的字符串，指定只应在响应中包含 FabricEvents 类型。 |
| --exclude-analysis-events | 如果传递了 true，此参数将禁用 AnalysisEvents 的检索。 |
| --skip-correlation-lookup | 如果传递了 true，此参数将禁用 CorrelatedEvents 信息的搜索。 否则，将处理 CorrelationEvents，并填充每个 FabricEvent 中的 HasCorrelatedEvents 字段。 |
| --timeout -t | 执行操作的服务器超时，以秒为单位。 此超时指定客户端可以等待请求的操作完成的持续时间。 此参数的默认值为 60 秒。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug | 提高日志记录详细程度以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 获取完整的调试日志。 |

## <a name="sfctl-events-partition-all-replicas-list"></a>sfctl events partition-all-replicas-list
获取分区的所有副本相关事件。

响应是 ReplicaEvent 对象的列表。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --end-time-utc   [必需] | 查找查询的结束时间，采用 ISO UTC yyyy-MM-ddTHH\:mm\:ssZ 格式。 |
| --partition-id   [必需] | 分区的标识。 |
| --start-time-utc [必需] | 查找查询的开始时间，采用 ISO UTC yyyy-MM-ddTHH\:mm\:ssZ 格式。 |
| --events-types-filter | 这是一个逗号分隔的字符串，指定只应在响应中包含 FabricEvents 类型。 |
| --exclude-analysis-events | 如果传递了 true，此参数将禁用 AnalysisEvents 的检索。 |
| --skip-correlation-lookup | 如果传递了 true，此参数将禁用 CorrelatedEvents 信息的搜索。 否则，将处理 CorrelationEvents，并填充每个 FabricEvent 中的 HasCorrelatedEvents 字段。 |
| --timeout -t | 执行操作的服务器超时，以秒为单位。 此超时指定客户端可以等待请求的操作完成的持续时间。 此参数的默认值为 60 秒。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug | 提高日志记录详细程度以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 获取完整的调试日志。 |

## <a name="sfctl-events-partition-list"></a>sfctl events partition-list
获取分区相关的事件。

响应是 PartitionEvent 对象的列表。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --end-time-utc   [必需] | 查找查询的结束时间，采用 ISO UTC yyyy-MM-ddTHH\:mm\:ssZ 格式。 |
| --partition-id   [必需] | 分区的标识。 |
| --start-time-utc [必需] | 查找查询的开始时间，采用 ISO UTC yyyy-MM-ddTHH\:mm\:ssZ 格式。 |
| --events-types-filter | 这是一个逗号分隔的字符串，指定只应在响应中包含 FabricEvents 类型。 |
| --exclude-analysis-events | 如果传递了 true，此参数将禁用 AnalysisEvents 的检索。 |
| --skip-correlation-lookup | 如果传递了 true，此参数将禁用 CorrelatedEvents 信息的搜索。 否则，将处理 CorrelationEvents，并填充每个 FabricEvent 中的 HasCorrelatedEvents 字段。 |
| --timeout -t | 执行操作的服务器超时，以秒为单位。 此超时指定客户端可以等待请求的操作完成的持续时间。 此参数的默认值为 60 秒。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug | 提高日志记录详细程度以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 获取完整的调试日志。 |

## <a name="sfctl-events-partition-replica-list"></a>sfctl events partition-replica-list
获取分区副本相关的事件。

响应是 ReplicaEvent 对象的列表。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --end-time-utc   [必需] | 查找查询的结束时间，采用 ISO UTC yyyy-MM-ddTHH\:mm\:ssZ 格式。 |
| --partition-id   [必需] | 分区的标识。 |
| --replica-id     [必需] | 副本的标识符。 |
| --start-time-utc [必需] | 查找查询的开始时间，采用 ISO UTC yyyy-MM-ddTHH\:mm\:ssZ 格式。 |
| --events-types-filter | 这是一个逗号分隔的字符串，指定只应在响应中包含 FabricEvents 类型。 |
| --exclude-analysis-events | 如果传递了 true，此参数将禁用 AnalysisEvents 的检索。 |
| --skip-correlation-lookup | 如果传递了 true，此参数将禁用 CorrelatedEvents 信息的搜索。 否则，将处理 CorrelationEvents，并填充每个 FabricEvent 中的 HasCorrelatedEvents 字段。 |
| --timeout -t | 执行操作的服务器超时，以秒为单位。 此超时指定客户端可以等待请求的操作完成的持续时间。 此参数的默认值为 60 秒。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug | 提高日志记录详细程度以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 获取完整的调试日志。 |

## <a name="sfctl-events-service-list"></a>sfctl events service-list
获取服务相关的事件。

响应是 ServiceEvent 对象的列表。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --end-time-utc   [必需] | 查找查询的结束时间，采用 ISO UTC yyyy-MM-ddTHH\:mm\:ssZ 格式。 |
| --service-id     [必需] | 服务的标识。 此 ID 通常是不带“fabric\:”URI 方案的服务全名。 从版本 6.0 开始，分层名称以“\~”字符隔开。 例如，如果服务名称为“fabric\:/myapp/app1/svc1”，则 6.0 及更高版本中的服务标识为“myapp\~app1\~svc1”，在以前的版本中为“myapp/app1/svc1”。 |
| --start-time-utc [必需] | 查找查询的开始时间，采用 ISO UTC yyyy-MM-ddTHH\:mm\:ssZ 格式。 |
| --events-types-filter | 这是一个逗号分隔的字符串，指定只应在响应中包含 FabricEvents 类型。 |
| --exclude-analysis-events | 如果传递了 true，此参数将禁用 AnalysisEvents 的检索。 |
| --skip-correlation-lookup | 如果传递了 true，此参数将禁用 CorrelatedEvents 信息的搜索。 否则，将处理 CorrelationEvents，并填充每个 FabricEvent 中的 HasCorrelatedEvents 字段。 |
| --timeout -t | 执行操作的服务器超时，以秒为单位。 此超时指定客户端可以等待请求的操作完成的持续时间。 此参数的默认值为 60 秒。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug | 提高日志记录详细程度以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 获取完整的调试日志。 |

