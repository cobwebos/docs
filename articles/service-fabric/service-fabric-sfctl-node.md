---
title: Azure Service Fabric CLI- sfctl node | Microsoft Docs
description: 介绍 Service Fabric CLI sfctl node 命令。
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
ms.openlocfilehash: e68a258c8e323b62f85219648c011ce1e661ee0d
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2018
ms.locfileid: "39494532"
---
# <a name="sfctl-node"></a>sfctl node
管理构成群集的节点。

## <a name="commands"></a>命令

|命令|Description|
| --- | --- |
| disable | 根据指定的停用意图停用 Service Fabric 群集节点。 |
| enable | 激活当前已停用的 Service Fabric 群集节点。 |
| health | 获取 Service Fabric 节点的运行状况。 |
| info | 获取有关 Service Fabric 群集中特定节点的信息。 |
| list | 获取 Service Fabric 群集中的节点的列表。 |
| load | 获取 Service Fabric 节点的负载信息。 |
| remove-state | 告知 Service Fabric，节点上的保留状态已被永久删除或丢失。 |
| report-health | 发送有关 Service Fabric 节点的运行状况报告。 |
| restart | 重启 Service Fabric 群集节点。 |
| transition | 启动或停止群集节点。 |
| transition-status | 获取使用 StartNodeTransition 启动的操作的进度。 |

## <a name="sfctl-node-disable"></a>sfctl node disable
根据指定的停用意图停用 Service Fabric 群集节点。

根据指定的停用意图停用 Service Fabric 群集节点。 在停用的过程中，可以提高而不能降低停用意图（例如，根据 Pause 意图停用的节点可以进一步根据 Restart 停用，但反过来不行）。 停用节点后，随时可以使用“激活节点”操作将其重新激活。 如果停用操作未完成，则会取消停用。 在已关闭并在停用状态下恢复启动的节点上放置服务之前，仍需重新激活该节点，

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --node-name [必需] | 节点的名称。 |
| --deactivation-intent | 描述停用节点的意图或原因。 可能的值如下。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-node-enable"></a>sfctl node enable
激活当前已停用的 Service Fabric 群集节点。

激活当前已停用的 Service Fabric 群集节点。 激活后，该节点再次成为可放置新副本的有效目标，该节点上剩余的所有已停用副本会重新激活。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --node-name [必需] | 节点的名称。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-node-health"></a>sfctl node health
获取 Service Fabric 节点的运行状况。

获取 Service Fabric 节点的运行状况。 使用 EventsHealthStateFilter 可以根据运行状态筛选针对节点报告的运行状况事件的集合。 如果按名称指定的节点不在运行状况存储中，则会返回错误。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --node-name       [必需] | 节点的名称。 |
| --events-health-state-filter | 用于根据运行状况筛选返回的 HealthEvent 对象集合。 此参数的可能值包括以下运行状态之一的整数值。 仅返回与筛选器匹配的事件。 所有事件用于评估聚合运行状态。 如果未指定，则返回所有项。 状态值为基于标志的枚举，因此该值可以是使用按位“OR”运算符获取的值的组合。 例如，如果提供的值为 6，则返回 HealthState 值为 OK (2) 和 Warning (4) 的所有事件。  <br> - Default - 默认值。 匹配任何 HealthState。 值为 0。  <br> - None - 不与任何 HealthState 值匹配的筛选器。 未返回有关给定状态集合的结果时使用。 值为 1。  <br> - Ok - 与 HealthState 值为 OK 的输入匹配的筛选器。 值为 2。  <br> - Warning - 与 HealthState 值为 Warning 的输入匹配的筛选器。 值为 4。  <br> - Error - 与 HealthState 值为 Error 的输入匹配的筛选器。 值为 8。  <br> - All - 与具有任意 HealthState 值的输入匹配的筛选器。 值为 65535。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-node-info"></a>sfctl node info
获取有关 Service Fabric 群集中特定节点的信息。

响应包括有关节点的名称、状态、ID、运行状况、运行时间和其他详细信息。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --node-name [必需] | 节点的名称。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-node-list"></a>sfctl node list
获取 Service Fabric 群集中的节点的列表。

响应包括有关节点的名称、状态、ID、运行状况、运行时间和其他详细信息。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --continuation-token | 继续标记参数用于获取下一组结果。 如果单个响应无法容纳来自系统的结果，则 API 响应中包括含有非空值的继续标记。 当此值传递到下一个 API 调用时，API 返回下一组结果。 如果没有更多结果，则该继续标记不包含值。 不应将此参数的值进行 URL 编码。 |
| --max-results | 作为分页查询的一部分返回的最大结果数。 此参数定义返回结果数的上限。 如果根据配置中定义的最大消息大小限制，无法将这些结果容纳到消息中，则返回的结果数可能小于指定的最大结果数。 如果此参数为零或者未指定，则分页查询包含返回消息中最多可容纳的结果数。 |
| --node-status-filter | 用于根据 NodeStatus 筛选节点。 仅返回与指定的筛选器值匹配的节点。 筛选器值可以是下列项之一。  默认值\: default。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-node-load"></a>sfctl node load
获取 Service Fabric 节点的负载信息。

在 Service Fabric 节点的负载信息中检索已定义负载或容量的所有指标。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --node-name [必需] | 节点的名称。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-node-remove-state"></a>sfctl node remove-state
告知 Service Fabric，节点上的保留状态已被永久删除或丢失。

这意味着无法恢复该节点的保留状态。 如果硬盘已擦除干净或者硬盘崩溃，通常会出现这种情况。 节点必须已关闭，此操作才能成功。 此操作让 Service Fabric 知道该节点上的副本不再存在，并且 Service Fabric 应停止等待这些副本恢复。 如果未删除节点上的状态并且节点能够以原状态恢复，则不要运行此 cmdlet。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --node-name [必需] | 节点的名称。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-node-report-health"></a>sfctl node report-health
发送有关 Service Fabric 节点的运行状况报告。

报告指定的 Service Fabric 节点的运行状况状态。 该报告必须包含有关运行状况报告及其所报告属性的源的信息。 该报告将发送到 Service Fabric 网关节点，后者会将其转发到运行状况存储。 该报告可能被网关接受但被运行状况存储在执行额外的验证后拒绝。 例如，运行状况存储可能会由于无效的参数（如过时的序列号）而拒绝该报告。 若要了解该报告是否已应用于运行状况存储中，请检查该报告是否显示在 HealthEvents 部分中。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --health-property [必需] | 运行状况信息的属性。 <br><br> 一个实体可以有不同属性的运行状况报告。 该属性是一个字符串，不是固定的枚举，因此可使报告器灵活地对触发报告的状态条件进行分类。 例如，SourceId 为“LocalWatchdog”的报告器可以监视节点上的可用磁盘的状态，因此它可以报告该节点的“AvailableDisk”属性。 同一报告器可以监视节点连接，因此它可以报告同一节点的“Connectivity”属性。 在运行状况存储中，这些报告均被视为指定节点的单独运行状况事件。 与 SourceId 一起，该属性唯一地标识运行状况信息。 |
| --health-state    [必需] | 可能的值包括\:“Invalid”、“Ok”、“Warning”、“Error”、“Unknown”。 |
| --node-name       [必需] | 要报告的节点名称。 |
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

## <a name="sfctl-node-restart"></a>sfctl node restart
重启 Service Fabric 群集节点。

重启已启动的 Service Fabric 群集节点。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --node-name [必需] | 节点的名称。 |
| --create-fabric-dump | 指定 True 会创建结构节点进程的转储。 此参数区分大小写。  默认值\: false |
| --node-instance-id | 目标节点的实例 ID。 如果指定了实例 ID，则仅当该 ID 与节点的当前实例匹配时，才重启该节点。 默认值“0”会匹配任何实例 ID。 可以使用 get node 查询获取实例 ID。  默认值\: 0。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-node-transition"></a>sfctl node transition
启动或停止群集节点。

启动或停止群集节点。  群集节点是一个进程，而不是 OS 实例本身。  若要启动节点，请为 NodeTransitionType 参数传入“Start”。 若要停止节点，请为 NodeTransitionType 参数传入“Stop”。 此 API 启动操作 - API 返回时，节点可能尚未完成转换。 结合相同的 OperationId 调用 GetNodeTransitionProgress 可获取操作进度。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --node-instance-id         [必需] | 目标节点的节点实例 ID。 可通过 GetNodeInfo API 确定此信息。 |
| --node-name                [必需] | 节点的名称。 |
| --node-transition-type     [必需] | 指示要执行的转换类型。  NodeTransitionType.Start 将启动已停止的节点。 NodeTransitionType.Stop 将停止已启动的节点。 |
| --operation-id             [必需] | 用于标识此 API 的调用的 GUID。  需将此参数传入相应的 GetProgress API。 |
| --stop-duration-in-seconds [必需] | 使节点保持停止状态的持续时间，以秒为单位。  最小值为 600，最大值为 14400。  此时间过后，节点将自动恢复启动状态。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-node-transition-status"></a>sfctl node transition-status
获取使用 StartNodeTransition 启动的操作的进度。

使用提供的 OperationId 获取通过 StartNodeTransition 启动的操作的进度。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --node-name    [必需] | 节点的名称。 |
| --operation-id [必需] | 用于标识此 API 的调用的 GUID。  需将此参数传入相应的 GetProgress API。 |
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