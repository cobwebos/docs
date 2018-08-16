---
title: Azure Service Fabric CLI - sfctl partition | Microsoft Docs
description: 介绍 Service Fabric CLI sfctl partition 命令。
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 07/31/2018
ms.author: bikang
ms.openlocfilehash: 93478e5d13ef649b86ebc047f4e53f1486e2ff68
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2018
ms.locfileid: "39493947"
---
# <a name="sfctl-partition"></a>sfctl partition
查询和管理任何服务的分区。

## <a name="commands"></a>命令

|命令|Description|
| --- | --- |
| data-loss | 此 API 会造成指定的分区发生数据丢失。 |
| data-loss-status | 获取使用 StartDataLoss API 启动的分区数据丢失操作的进度。 |
| health | 获取指定 Service Fabric 分区的运行状况。 |
| info | 获取有关 Service Fabric 分区的信息。 |
| list | 获取 Service Fabric 服务的分区列表。 |
| load | 获取指定的 Service Fabric 分区的负载信息。 |
| load-reset | 重置 Service Fabric 分区的当前负载。 |
| quorum-loss | 造成给定的有状态服务分区发生仲裁丢失。 |
| quorum-loss-status | 获取使用 StartQuorumLoss API 在分区上启动的仲裁丢失操作的进度。 |
| recover | 向 Service Fabric 群集指出应该尝试恢复当前停滞在仲裁丢失状态的特定分区。 |
| recover-all | 向 Service Fabric 群集指出应该尝试恢复当前停滞在仲裁丢失状态的所有服务（包括系统服务）。 |
| report-health | 发送有关 Service Fabric 分区的运行状况报告。 |
| restart | 此 API 会重启指定分区的部分或所有副本或者实例。 |
| restart-status | 获取使用 StartPartitionRestart 启动的 PartitionRestart 操作的进度。 |
| svc-name | 获取分区的 Service Fabric 服务名称。 |

## <a name="sfctl-partition-data-loss"></a>sfctl partition data-loss
此 API 会造成指定的分区发生数据丢失。

它会触发对分区的 OnDataLossAsync API 的调用。  此 API 会造成指定的分区发生数据丢失。 它会触发对分区的 OnDataLoss API 的调用。 实际的数据丢失情况将取决于指定的 DataLossMode。 <br> PartialDataLoss - 仅删除副本仲裁，并且会为分区触发 OnDataLoss，但实际的数据丢失情况取决于是否存在正在进行的复制。 <br>FullDataLoss - 所有副本都会删除，因此会丢失所有数据并触发 OnDataLoss。 <br>调用此 API 时，只能将有状态服务作为目标。 建议不要在调用此 API 时将系统服务作为目标。 
> [!NOTE]
> 此 API 一旦被调用就无法撤消。 调用 CancelOperation 只会停止执行操作并清除内部系统状态。 如果命令的执行时间很长，已导致数据丢失，则不会还原数据。 使用同一 OperationId 调用 GetDataLossProgress API 会返回使用此 API 启动的操作的信息。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --data-loss-mode [必需] | 此枚举会传递到 StartDataLoss API，表示会导致哪种类型的数据丢失。 |
| --operation-id   [必需] | 用于标识此 API 的调用的 GUID。  需将此参数传入相应的 GetProgress API。 |
| --partition-id   [必需] | 分区的标识。 |
| --service-id     [必需] | 服务的标识。 此 ID 通常是不带“fabric\:”URI 方案的服务全名。 从版本 6.0 开始，分层名称以“\~”字符隔开。 例如，如果服务名称为“fabric\:/myapp/app1/svc1”，则 6.0 及更高版本中的服务标识为“myapp\~app1\~svc1”，在以前的版本中为“myapp/app1/svc1”。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-partition-data-loss-status"></a>sfctl partition data-loss-status
获取使用 StartDataLoss API 启动的分区数据丢失操作的进度。

获取使用 OperationId 通过 StartDataLoss 启动的数据丢失操作的进度。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --operation-id [必需] | 用于标识此 API 的调用的 GUID。  需将此参数传入相应的 GetProgress API。 |
| --partition-id [必需] | 分区的标识。 |
| --service-id   [必需] | 服务的标识。 此 ID 通常是不带“fabric\:”URI 方案的服务全名。 从版本 6.0 开始，分层名称以“\~”字符隔开。 例如，如果服务名称为“fabric\:/myapp/app1/svc1”，则 6.0 及更高版本中的服务标识为“myapp\~app1\~svc1”，在以前的版本中为“myapp/app1/svc1”。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-partition-health"></a>sfctl partition health
获取指定 Service Fabric 分区的运行状况。

使用 EventsHealthStateFilter 可以根据运行状态筛选针对服务报告的运行状况事件的集合。 使用 ReplicasHealthStateFilter 可以筛选分区上 ReplicaHealthState 对象的集合。 如果指定运行状况存储中不存在的分区，此请求会返回错误。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --partition-id      [必需] | 分区的标识。 |
| --events-health-state-filter | 用于根据运行状况筛选返回的 HealthEvent 对象集合。 此参数的可能值包括以下运行状态之一的整数值。 仅返回与筛选器匹配的事件。 所有事件用于评估聚合运行状态。 如果未指定，则返回所有项。 状态值为基于标志的枚举，因此该值可以是使用按位“OR”运算符获取的值的组合。 例如，如果提供的值为 6，则返回 HealthState 值为 OK (2) 和 Warning (4) 的所有事件。  <br> - Default - 默认值。 匹配任何 HealthState。 值为 0。  <br> - None - 不与任何 HealthState 值匹配的筛选器。 未返回有关给定状态集合的结果时使用。 值为 1。  <br> - Ok - 与 HealthState 值为 OK 的输入匹配的筛选器。 值为 2。  <br> - Warning - 与 HealthState 值为 Warning 的输入匹配的筛选器。 值为 4。  <br> - Error - 与 HealthState 值为 Error 的输入匹配的筛选器。 值为 8。  <br> - All - 与具有任意 HealthState 值的输入匹配的筛选器。 值为 65535。 |
| --exclude-health-statistics | 指示运行状况统计数据是否应作为查询结果的一部分返回。 默认值为 False。 统计信息显示处于 Ok、Warning 和 Error 运行状况的子实体数。 |
| --replicas-health-state-filter | 用于筛选分区中 ReplicaHealthState 对象的集合。 可从位运算的成员或 HealthStateFilter 的成员获取该值。 只会返回与筛选器匹配的副本。 所有副本都会用于评估聚合运行状态。 如果未指定，将返回所有条目。状态值为基于标志的枚举，因此该值可以是使用按位“OR”运算符获取的值的组合。 例如，如果提供的值为 6，则会返回 HealthState 值为 OK (2) 和 Warning (4) 的所有事件。 此参数的可能值包括以下运行状态之一的整数值。  <br> - Default - 默认值。 匹配任何 HealthState。 值为 0。  <br> - None - 不与任何 HealthState 值匹配的筛选器。 未返回有关给定状态集合的结果时使用。 值为 1。  <br> - Ok - 与 HealthState 值为 OK 的输入匹配的筛选器。 值为 2。  <br> - Warning - 与 HealthState 值为 Warning 的输入匹配的筛选器。 值为 4。  <br> - Error - 与 HealthState 值为 Error 的输入匹配的筛选器。 值为 8。  <br> - All - 与具有任意 HealthState 值的输入匹配的筛选器。 值为 65535。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-partition-info"></a>sfctl partition info
获取有关 Service Fabric 分区的信息。

获取有关指定的分区的信息。 响应包括分区 ID、分区方案信息、分区支持的密钥、状态、运行状况和有关分区的其他详细信息。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --partition-id [必需] | 分区的标识。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-partition-list"></a>sfctl partition list
获取 Service Fabric 服务的分区列表。

响应包括分区 ID、分区方案信息、分区支持的密钥、状态、运行状况和有关分区的其他详细信息。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --service-id [必需] | 服务的标识。 此 ID 通常是不带“fabric\:”URI 方案的服务全名。 从版本 6.0 开始，分层名称以“\~”字符隔开。 例如，如果服务名称为“fabric\:/myapp/app1/svc1”，则 6.0 及更高版本中的服务标识为“myapp\~app1\~svc1”，在以前的版本中为“myapp/app1/svc1”。 |
| --continuation-token | 继续标记参数用于获取下一组结果。 如果单个响应无法容纳来自系统的结果，则 API 响应中包括含有非空值的继续标记。 当此值传递到下一个 API 调用时，API 返回下一组结果。 如果没有更多结果，则该继续标记不包含值。 不应将此参数的值进行 URL 编码。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-partition-load"></a>sfctl partition load
获取指定的 Service Fabric 分区的负载信息。

返回有关指定分区的负载的信息。 响应包括 Service Fabric 分区的负载报告的列表。 每项报告包括负载指标名称、值以及上次报告时间 (UTC)。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --partition-id [必需] | 分区的标识。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-partition-load-reset"></a>sfctl partition load-reset
重置 Service Fabric 分区的当前负载。

将 Service Fabric 分区的当前负载重置为服务的默认负载。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --partition-id [必需] | 分区的标识。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-partition-quorum-loss"></a>sfctl partition quorum-loss
造成给定的有状态服务分区发生仲裁丢失。

此 API 适用于服务出现临时仲裁丢失的情况。 使用同一 OperationId 调用 GetQuorumLossProgress API 会返回使用此 API 启动的操作的信息。 只能在有状态持久 (HasPersistedState==true) 服务上调用此项。  请勿在无状态服务或有状态仅内存中服务上使用此 API。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --operation-id         [必需] | 用于标识此 API 的调用的 GUID。  需将此参数传入相应的 GetProgress API。 |
| --partition-id         [必需] | 分区的标识。 |
| --quorum-loss-duration [必需] | 分区将处于仲裁丢失状态的时间。  必须以秒为单位指定此项。 |
| --quorum-loss-mode     [必需] | 此枚举会传递到 StartQuorumLoss API，表示会导致哪种类型的仲裁丢失。 |
| --service-id           [必需] | 服务的标识。 此 ID 通常是不带“fabric\:”URI 方案的服务全名。 从版本 6.0 开始，分层名称以“\~”字符隔开。 例如，如果服务名称为“fabric\:/myapp/app1/svc1”，则 6.0 及更高版本中的服务标识为“myapp\~app1\~svc1”，在以前的版本中为“myapp/app1/svc1”。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-partition-quorum-loss-status"></a>sfctl partition quorum-loss-status
获取使用 StartQuorumLoss API 在分区上启动的仲裁丢失操作的进度。

获取使用提供的 OperationId 通过 StartQuorumLoss 启动的仲裁丢失操作的进度。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --operation-id [必需] | 用于标识此 API 的调用的 GUID。  需将此参数传入相应的 GetProgress API。 |
| --partition-id [必需] | 分区的标识。 |
| --service-id   [必需] | 服务的标识。 此 ID 通常是不带“fabric\:”URI 方案的服务全名。 从版本 6.0 开始，分层名称以“\~”字符隔开。 例如，如果服务名称为“fabric\:/myapp/app1/svc1”，则 6.0 及更高版本中的服务标识为“myapp\~app1\~svc1”，在以前的版本中为“myapp/app1/svc1”。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-partition-recover"></a>sfctl partition recover
向 Service Fabric 群集指出应该尝试恢复当前停滞在仲裁丢失状态的特定分区。

仅当确定已关闭的副本无法恢复时，才执行此操作。 不当地使用此 API 可能导致潜在的数据丢失。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --partition-id [必需] | 分区的标识。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-partition-recover-all"></a>sfctl partition recover-all
向 Service Fabric 群集指出应该尝试恢复当前停滞在仲裁丢失状态的所有服务（包括系统服务）。

仅当确定已关闭的副本无法恢复时，才执行此操作。 不当地使用此 API 可能导致潜在的数据丢失。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-partition-report-health"></a>sfctl partition report-health
发送有关 Service Fabric 分区的运行状况报告。

报告指定的 Service Fabric 分区的运行状况状态。 该报告必须包含有关运行状况报告及其所报告属性的源的信息。 该报告将发送到 Service Fabric 网关分区，后者会将其转发到运行状况存储。 该报告可能被网关接受但被运行状况存储在执行额外的验证后拒绝。 例如，运行状况存储可能会由于无效的参数（如过时的序列号）而拒绝该报告。 若要了解该报告是否已应用于运行状况存储中，请检查该报告是否显示在事件部分中。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --health-property [必需] | 运行状况信息的属性。 <br><br> 一个实体可以有不同属性的运行状况报告。 该属性是一个字符串，不是固定的枚举，因此可使报告器灵活地对触发报告的状态条件进行分类。 例如，SourceId 为“LocalWatchdog”的报告器可以监视节点上的可用磁盘的状态，因此它可以报告该节点的“AvailableDisk”属性。 同一报告器可以监视节点连接，因此它可以报告同一节点的“Connectivity”属性。 在运行状况存储中，这些报告均被视为指定节点的单独运行状况事件。 与 SourceId 一起，该属性唯一地标识运行状况信息。 |
| --health-state    [必需] | 可能的值包括\:“Invalid”、“Ok”、“Warning”、“Error”、“Unknown”。 |
| --partition-id    [必需] | 分区的标识。 |
| --source-id       [必需] | 用于标识生成了运行状况信息的客户端/监视程序/系统组件的源名称。 |
| --description | 运行状况信息的说明。 <br><br> 它表示用于添加有关该报告的用户可读信息的自定义文本。 该说明的最大字符串长度为 4096 个字符。 如果所提供字符串的长度大于该值，它将被自动截断。 截断时，该说明的末尾字符包含一个标记“[Truncated]”，并且总字符串大小为 4096 个字符。 该标记的存在向用户指示截断已发生。 请注意，当截断时，该说明包含来自原始字符串的 4096 个以内的字符。 |
| --immediate | 用于指示是否应立即发送报告的标志。 <br><br> 运行状况报告将发送到 Service Fabric 网关应用程序，后者会将其转发到运行状况存储。 如果 Immediate 设置为 true，则报告将立即从 HTTP 网关发送至运行状况存储，而无论 HTTP 网关应用程序使用的 Fabric 客户端设置如何。 这对于应尽快发送的关键报告十分有用。 由于计时和其他情况，发送报告可能仍会失败，例如，在 HTTP 网关已关闭或消息无法到达网关的情况下。 如果 Immediate 设置为 false，则报告将基于来自 HTTP 网关的运行状况客户端设置发送。 因此，系统将根据 HealthReportSendInterval 配置对其进行批处理。 这是建议的设置，因为它可让运行状况客户端优化发往运行状况存储的运行状况报告消息以及运行状况报告处理。 默认情况下，报告不立即发送。 |
| --remove-when-expired | 该值指示是否在报告过期时从运行状况存储删除该报告。 <br><br> 如果设置为 true，报告在过期后将从运行状况存储中删除。 如果设置为 false，报告在过期时将被视为错误。 此属性的值在默认情况下为 false。 当客户端定期报告时，它们应将 RemoveWhenExpired 设置为 false（默认值）。 这样，如果报告器有问题（例如死锁）并且无法报告，那么在运行状况报告过期时该实体就会被评估为处于错误状态。 这会将该实体标记为处于“Error”运行状况状态。 |
| --sequence-number | 此运行状况报告的序列号（采用数字字符串形式）。 <br><br> 报告序列号由运行状况存储用来检测过时的报告。 如果未指定，序列号将在报告被添加时由运行状况客户端自动生成。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |
| --ttl | 此运行状况报告保持有效的持续时间。 此字段将 ISO8601 格式用于指定该持续时间。 <br><br> 当客户端定期报告时，它们应以高于生存时间的频率发送报告。 如果客户端以非定期的方式报告，它们可以将生存时间设置为无限。 生存时间过期时，包含运行状况信息的运行状况事件将从运行状况存储中删除（如果 RemoveWhenExpired 为 true），或者将会评估为处于错误状态（如果 RemoveWhenExpired 为 false）。 如果未指定，生存时间将默认为无限值。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-partition-restart"></a>sfctl partition restart
此 API 会重启指定分区的部分或所有副本或者实例。

此 API 可用于测试故障转移。 如果用于针对无状态服务分区，RestartPartitionMode 必须是 AllReplicasOrInstances。 使用相同的 OperationId 调用 GetPartitionRestartProgress API 可获取进度。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --operation-id           [必需] | 用于标识此 API 的调用的 GUID。  需将此参数传入相应的 GetProgress API。 |
| --partition-id           [必需] | 分区的标识。 |
| --restart-partition-mode [必需] | 描述要重新启动哪些分区。 |
| --service-id             [必需] | 服务的标识。 此 ID 通常是不带“fabric\:”URI 方案的服务全名。 从版本 6.0 开始，分层名称以“\~”字符隔开。 例如，如果服务名称为“fabric\:/myapp/app1/svc1”，则 6.0 及更高版本中的服务标识为“myapp\~app1\~svc1”，在以前的版本中为“myapp/app1/svc1”。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-partition-restart-status"></a>sfctl partition restart-status
获取使用 StartPartitionRestart 启动的 PartitionRestart 操作的进度。

获取使用提供的 OperationId 通过 StartPartitionRestart 启动的 PartitionRestart 操作的进度。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --operation-id [必需] | 用于标识此 API 的调用的 GUID。  需将此参数传入相应的 GetProgress API。 |
| --partition-id [必需] | 分区的标识。 |
| --service-id   [必需] | 服务的标识。 此 ID 通常是不带“fabric\:”URI 方案的服务全名。 从版本 6.0 开始，分层名称以“\~”字符隔开。 例如，如果服务名称为“fabric\:/myapp/app1/svc1”，则 6.0 及更高版本中的服务标识为“myapp\~app1\~svc1”，在以前的版本中为“myapp/app1/svc1”。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-partition-svc-name"></a>sfctl partition svc-name
获取分区的 Service Fabric 服务名称。

获取指定分区的服务的名称。 如果分区 ID 在群集中不存在，则会返回 404 错误。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --partition-id [必需] | 分区的标识。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |


## <a name="next-steps"></a>后续步骤
- [安装](service-fabric-cli.md) Service Fabric CLI。
- 了解如何通过[示例脚本](/azure/service-fabric/scripts/sfctl-upgrade-application)使用 Service Fabric CLI。
