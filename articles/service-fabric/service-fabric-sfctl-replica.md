---
title: Azure Service Fabric CLI - sfctl replica | Microsoft Docs
description: 介绍 Service Fabric CLI sfctl replica 命令。
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
ms.date: 05/23/2018
ms.author: bikang
ms.openlocfilehash: cd09fe906f77bb06f0ac7afaa6c6cce326dbfa5c
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763572"
---
# <a name="sfctl-replica"></a>sfctl replica
管理属于服务分区的副本。

## <a name="commands"></a>命令

|命令|说明|
| --- | --- |
| deployed | 获取部署在 Service Fabric 节点上的副本的详细信息。 |
| deployed-list | 获取部署在 Service Fabric 节点上的副本的列表。 |
| health | 获取 Service Fabric 有状态服务副本或无状态服务实例的运行状况。 |
| info | 获取有关 Service Fabric 分区的副本的信息。 |
| list | 获取有关 Service Fabric 服务分区的副本的信息。 |
| remove | 删除节点上运行的服务副本。 |
| report-health | 发送有关 Service Fabric 副本的运行状况报告。 |
| restart | 重启节点上运行的持久性服务的服务副本。 |

## <a name="sfctl-replica-deployed"></a>sfctl replica deployed
获取部署在 Service Fabric 节点上的副本的详细信息。

获取部署在 Service Fabric 节点上的副本的详细信息。 信息包括服务类型、服务名称、当前服务操作、当前服务操作开始日期时间、分区 ID、副本/实例 ID、报告的负载和其他信息。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --node-name    [必需] | 节点的名称。 |
| --partition-id [必需] | 分区的标识。 |
| --replica-id   [必需] | 副本的标识符。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值：60。 |

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值：json、jsonc、table、tsv。  默认值：json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-replica-deployed-list"></a>sfctl replica deployed-list
获取部署在 Service Fabric 节点上的副本的列表。

获取一个列表，其中包含有关 Service Fabric 节点上部署的副本的信息。 这些信息包括分区 ID、副本 ID、副本状态、服务名称、服务类型名称和其他信息。 使用 PartitionId 或 ServiceManifestName 查询参数可返回有关与这些参数的指定值匹配的已部署副本的信息。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --application-id [必需] | 应用程序的标识。 这通常是不带“fabric\:”URI 方案的应用程序全名。 从版本 6.0 开始，分层名称以“\~”字符隔开。 例如，如果应用程序名称为“fabric\:/myapp/app1”，则 6.0 及更高版本中的应用程序标识为“myapp\~app1”，在以前的版本中为“myapp/app1”。 |
| --node-name [必需] | 节点的名称。 |
| --partition-id | 分区的标识。 |
| --service-manifest-name | 在 Service Fabric 群集中注册为应用程序类型一部分的服务清单的名称。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值：60。 |

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值：json、jsonc、table、tsv。  默认值：json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-replica-health"></a>sfctl replica health
获取 Service Fabric 有状态服务副本或无状态服务实例的运行状况。

获取 Service Fabric 副本的运行状况。 使用 EventsHealthStateFilter 可以根据运行状态筛选针对副本报告的运行状况事件的集合。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --partition-id    [必需] | 分区的标识。 |
| --replica-id      [必需] | 副本的标识符。 |
| --events-health-state-filter | 用于根据运行状况筛选返回的 HealthEvent 对象集合。 此参数的可能值包括以下运行状态之一的整数值。 仅返回与筛选器匹配的事件。 所有事件用于评估聚合运行状态。 如果未指定，则返回所有项。 状态值为基于标志的枚举，因此该值可是使用按位“OR”运算符获取的值的组合。 例如，如果提供的值为 6，则返回 HealthState 值为 OK (2) 和 Warning (4) 的所有事件。  <br> - Default - 默认值。 匹配任何 HealthState。 值为 0。  <br> - None - 不与任何 HealthState 值匹配的筛选器。 未返回有关给定状态集合的结果时使用。 值为 1。  <br> - Ok - 与 HealthState 值为 OK 的输入匹配的筛选器。 值为 2。  <br> - Warning - 与 HealthState 值为 Warning 的输入匹配的筛选器。 值为 4。  <br> - Error - 与 HealthState 值为 Error 的输入匹配的筛选器。 值为 8。  <br> - All - 与具有任意 HealthState 值的输入匹配的筛选器。 值为 65535。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值：60。 |

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值：json、jsonc、table、tsv。  默认值：json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-replica-info"></a>sfctl replica info
获取有关 Service Fabric 分区的副本的信息。

响应包括 ID、角色、状态、运行状况、节点名称、运行时间和有关副本的其他详细信息。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --partition-id [必需] | 分区的标识。 |
| --replica-id   [必需] | 副本的标识符。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值：60。 |

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值：json、jsonc、table、tsv。  默认值：json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-replica-list"></a>sfctl replica list
获取有关 Service Fabric 服务分区的副本的信息。

GetReplicas 终结点返回有关指定分区的副本的信息。 响应包括 ID、角色、状态、运行状况、节点名称、运行时间和有关副本的其他详细信息。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --partition-id [必需] | 分区的标识。 |
| --continuation-token | 继续标记参数用于获取下一组结果。 如果单个响应无法容纳来自系统的结果，则 API 响应中包括含有非空值的继续标记。 当此值传递到下一个 API 调用时，API 返回下一组结果。 如果没有更多结果，则该继续标记不包含值。 不应将此参数的值进行 URL 编码。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值：60。 |

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值：json、jsonc、table、tsv。  默认值：json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-replica-remove"></a>sfctl replica remove
删除节点上运行的服务副本。

此 API 通过从 Service Fabric 群集中删除副本来模拟 Service Fabric 副本故障。 删除操作会关闭副本，将副本转换为“无”角色，然后从群集中删除副本的所有状态信息。 此 API 测试副本状态删除路径，并通过客户端 API 模拟报告错误永久路径。 警告 - 使用此 API 时不会执行任何安全检查。 不当地使用此 API 可能导致有状态服务的数据丢失。 此外，forceRemove 标志会影响同一进程中承载的所有其他副本。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --node-name    [必需] | 节点的名称。 |
| --partition-id [必需] | 分区的标识。 |
| --replica-id   [必需] | 副本的标识符。 |
| --force-remove | 强制删除 Service Fabric 应用程序或服务，跳过正常关闭序列。 若因服务代码中的问题而无法正常关闭副本，导致删除应用程序或服务操作超时，可使用此参数强制删除该应用程序或服务。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值：60。 |

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值：json、jsonc、table、tsv。  默认值：json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-replica-report-health"></a>sfctl replica report-health
发送有关 Service Fabric 副本的运行状况报告。

报告指定的 Service Fabric 副本的运行状况状态。 该报告必须包含有关运行状况报告及其所报告属性的源的信息。 该报告将发送到 Service Fabric 网关副本，后者会将其转发到运行状况存储。 该报告可能被网关接受但被运行状况存储在执行额外的验证后拒绝。 例如，运行状况存储可能会由于无效的参数（如过时的序列号）而拒绝该报告。 若要了解该报告是否已应用于运行状况存储中，请检查该报告是否显示在事件部分中。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --health-property [必需] | 运行状况信息的属性。 <br><br> 一个实体可以有不同属性的运行状况报告。 该属性是一个字符串，不是固定的枚举，因此可使报告器灵活地对触发报告的状态条件进行分类。 例如，SourceId 为“LocalWatchdog”的报告器可以监视节点上的可用磁盘的状态，因此它可以报告该节点的“AvailableDisk”属性。 同一报告器可以监视节点连接，因此它可以报告同一节点的“Connectivity”属性。 在运行状况存储中，这些报告均被视为指定节点的单独运行状况事件。 与 SourceId 一起，该属性唯一地标识运行状况信息。 |
| --health-state    [必需] | 可能的值包括：“Invalid”、“Ok”、“Warning”、“Error”、“Unknown”。 |
| --partition-id    [必需] | 分区的标识。 |
| --replica-id      [必需] | 分区的标识。 |
| --source-id       [必需] | 标识已生成运行状况信息的客户端/监视程序/系统组件的源名称。 |
| --description | 运行状况信息的说明。 <br><br> 它表示用于添加有关该报告的用户可读信息的自定义文本。 该说明的最大字符串长度为 4096 个字符。 如果所提供字符串的长度大于该值，它将被自动截断。 截断时，该说明的末尾字符包含一个标记“[Truncated]”，并且总字符串大小为 4096 个字符。 该标记的存在向用户指示截断已发生。 请注意，当截断时，该说明包含来自原始字符串的 4096 个以内的字符。 |
| --immediate | 一个用于指示是否应立即发送报告的标志。 <br><br> 运行状况报告将发送到 Service Fabric 网关应用程序，后者会将其转发到运行状况存储。 如果 Immediate 设置为 true，则报告将立即从 HTTP 网关发送至运行状况存储，而无论 HTTP 网关应用程序使用的 Fabric 客户端设置如何。 这对于应尽快发送的关键报告十分有用。 由于计时和其他情况，发送报告可能仍会失败，例如，在 HTTP 网关已关闭或消息无法到达网关的情况下。 如果 Immediate 设置为 false，则报告将基于来自 HTTP 网关的运行状况客户端设置发送。 因此，系统将根据 HealthReportSendInterval 配置对其进行批处理。 这是建议的设置，因为它可让运行状况客户端优化发往运行状况存储的运行状况报告消息以及运行状况报告处理。 默认情况下，报告不立即发送。 |
| --remove-when-expired | 该值指示是否在报告过期时从运行状况存储删除该报告。 <br><br> 如果设置为 true，报告在过期后将从运行状况存储中删除。 如果设置为 false，报告在过期时将被视为错误。 此属性的值在默认情况下为 false。 当客户端定期报告时，它们应将 RemoveWhenExpired 设置为 false（默认值）。 这样，如果报告器有问题（例如死锁）并且无法报告，那么在运行状况报告过期时该实体就会被评估为处于错误状态。 这会将该实体标记为处于“Error”运行状况状态。 |
| --sequence-number | 此运行状况报告的序列号（采用数字字符串形式）。 <br><br> 报告序列号由运行状况存储用来检测过时的报告。 如果未指定，序列号将在报告被添加时由运行状况客户端自动生成。 |
| --service-kind | 为其报告运行状况的服务副本的类型（无状态或有状态）。 以下是可能的值：“Stateless”、“Stateful”。  默认值：Stateful。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值：60。 |
| --ttl | 此运行状况报告保持有效的持续时间。 此字段将 ISO8601 格式用于指定该持续时间。 <br><br> 当客户端定期报告时，它们应以高于生存时间的频率发送报告。 如果客户端以非定期的方式报告，它们可以将生存时间设置为无限。 生存时间过期时，包含运行状况信息的运行状况事件将从运行状况存储中删除（如果 RemoveWhenExpired 为 true），或者将会评估为处于错误状态（如果 RemoveWhenExpired 为 false）。 如果未指定，生存时间将默认为无限值。 |

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值：json、jsonc、table、tsv。  默认值：json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-replica-restart"></a>sfctl replica restart
重启节点上运行的持久性服务的服务副本。

重启节点上运行的持久性服务的服务副本。 警告 - 使用此 API 时不会执行任何安全检查。 不当地使用此 API 可能导致有状态服务失去可用性。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --node-name    [必需] | 节点的名称。 |
| --partition-id [必需] | 分区的标识。 |
| --replica-id   [必需] | 副本的标识符。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值：60。 |

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值：json、jsonc、table、tsv。  默认值：json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="next-steps"></a>后续步骤
- [安装](service-fabric-cli.md) Service Fabric CLI。
- 了解如何通过[示例脚本](/azure/service-fabric/scripts/sfctl-upgrade-application)使用 Service Fabric CLI。
