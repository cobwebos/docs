---
title: Azure Service Fabric CLI - sfctl cluster | Microsoft Docs
description: 介绍 Service Fabric CLI sfctl cluster 命令。
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
ms.openlocfilehash: 60f3f74778f0fb32677c3b87b3140131ccd37bea
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763623"
---
# <a name="sfctl-cluster"></a>sfctl cluster
选择、管理和操作 Service Fabric 群集。

## <a name="commands"></a>命令

|命令|说明|
| --- | --- |
| code-versions | 获取 Service Fabric 群集中预配的结构代码版本的列表。 |
| config-versions | 获取 Service Fabric 群集中预配的结构配置版本的列表。 |
| health | 获取 Service Fabric 群集的运行状况。 |
| manifest | 获取 Service Fabric 群集清单。 |
| operation-cancel | 取消用户造成的错误操作。 |
| operation-list | 获取根据提供的输入筛选的、用户造成的错误操作列表。 |
| provision | 预配 Service Fabric 群集的代码包或配置包。 |
| recover-system | 向 Service Fabric 群集指出应该尝试恢复当前停滞在仲裁丢失状态的系统服务。 |
| report-health | 发送有关 Service Fabric 群集的运行状况报告。 |
| 选择 | 连接到 Service Fabric 群集终结点。 |
| unprovision | 取消预配 Service Fabric 群集的代码包或配置包。 |
| 升级 | 开始升级 Service Fabric 群集的代码或配置版本。 |
| upgrade-resume | 使群集升级转移到下一个升级域。 |
| upgrade-rollback | 回滚 Service Fabric 群集的升级。 |
| upgrade-status | 获取当前群集升级的进度。 |
| upgrade-update | 更新 Service Fabric 群集升级的升级参数。 |

## <a name="sfctl-cluster-code-versions"></a>sfctl cluster code-versions
获取 Service Fabric 群集中预配的结构代码版本的列表。

获取群集中预配的结构代码版本的信息列表。 可以使用参数 CodeVersion 有选择地将输出筛选为该特定版本。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --code-version | Service Fabric 的产品版本。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-cluster-config-versions"></a>sfctl cluster config-versions
获取 Service Fabric 群集中预配的结构配置版本的列表。

获取群集中预配的结构配置版本的信息列表。 可以使用参数 ConfigVersion 有选择地将输出筛选为该特定版本。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --config-version | Service Fabric 的配置版本。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-cluster-health"></a>sfctl cluster health
获取 Service Fabric 群集的运行状况。

获取 Service Fabric 群集的运行状况。 使用 EventsHealthStateFilter 可以根据运行状态筛选针对群集报告的运行状况事件的集合。 同样，使用 NodesHealthStateFilter 和 ApplicationsHealthStateFilter 可以根据聚合运行状态筛选返回的节点和应用程序集合。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --applications-health-state-filter | 用于根据运行状态筛选群集运行状况查询结果中返回的应用程序运行状态对象。 此参数的可能值包括从成员获取的整数值或对 HealthStateFilter 枚举成员进行位运算获取的整数值。 仅返回与筛选器匹配的应用程序。 所有应用程序都用于评估聚合运行状态。 如果未指定，则返回所有项。 状态值为基于标志的枚举，因此该值可是使用按位“OR”运算符获取的值的组合。 例如，如果提供的值为 6，则返回 HealthState 值为“OK”(2) 和“Warning”(4) 的应用程序的运行状态。  <br> - Default - 默认值。 匹配任何 HealthState。 值为 0。  <br> - None - 不与任何 HealthState 值匹配的筛选器。 未返回有关给定状态集合的结果时使用。 值为 1。  <br> - Ok - 与 HealthState 值为 OK 的输入匹配的筛选器。 值为 2。  <br> - Warning - 与 HealthState 值为 Warning 的输入匹配的筛选器。 值为 4。  <br> - Error - 与 HealthState 值为 Error 的输入匹配的筛选器。 值为 8。  <br> - All - 与具有任意 HealthState 值的输入匹配的筛选器。 值为 65535。 |
| --events-health-state-filter | 用于根据运行状况筛选返回的 HealthEvent 对象集合。 此参数的可能值包括以下运行状态之一的整数值。 仅返回与筛选器匹配的事件。 所有事件用于评估聚合运行状态。 如果未指定，则返回所有项。 状态值为基于标志的枚举，因此该值可是使用按位“OR”运算符获取的值的组合。 例如，如果提供的值为 6，则返回 HealthState 值为 OK (2) 和 Warning (4) 的所有事件。  <br> - Default - 默认值。 匹配任何 HealthState。 值为 0。  <br> - None - 不与任何 HealthState 值匹配的筛选器。 未返回有关给定状态集合的结果时使用。 值为 1。  <br> - Ok - 与 HealthState 值为 OK 的输入匹配的筛选器。 值为 2。  <br> - Warning - 与 HealthState 值为 Warning 的输入匹配的筛选器。 值为 4。  <br> - Error - 与 HealthState 值为 Error 的输入匹配的筛选器。 值为 8。  <br> - All - 与具有任意 HealthState 值的输入匹配的筛选器。 值为 65535。 |
| --exclude-health-statistics | 指示运行状况统计数据是否应作为查询结果的一部分返回。 默认值为 False。 统计信息显示处于 Ok、Warning 和 Error 运行状况的子实体数。 |
| --include-system-application-health-statistics | 指示运行状况统计信息是否应包括 fabric\:/System 应用程序的运行状况统计信息。 默认值为 False。 如果 IncludeSystemApplicationHealthStatistics 设置为 true，则运行状况统计信息包含属于 fabric\:/System 应用程序的实体。 否则，查询结果仅包含用户应用程序的运行状况统计信息。 应用此参数后，查询结果中必须包含运行状况统计信息。 |
| --nodes-health-state-filter | 用于根据运行状态筛选群集运行状况查询结果中返回的节点运行状态对象。 此参数的可能值包括以下运行状态之一的整数值。 仅返回与筛选器匹配的节点。 所有节点用于评估聚合运行状态。 如果未指定，则返回所有项。 状态值为基于标志的枚举，因此该值可是使用按位“OR”运算符获取的值的组合。 例如，如果提供的值为 6，则返回 HealthState 值为 OK (2) 和 Warning (4) 的节点的运行状态。  <br> - Default - 默认值。 匹配任何 HealthState。 值为 0。  <br> - None - 不与任何 HealthState 值匹配的筛选器。 未返回有关给定状态集合的结果时使用。 值为 1。  <br> - Ok - 与 HealthState 值为 OK 的输入匹配的筛选器。 值为 2。  <br> - Warning - 与 HealthState 值为 Warning 的输入匹配的筛选器。 值为 4。  <br> - Error - 与 HealthState 值为 Error 的输入匹配的筛选器。 值为 8。  <br> - All - 与具有任意 HealthState 值的输入匹配的筛选器。 值为 65535。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-cluster-manifest"></a>sfctl cluster manifest
获取 Service Fabric 群集清单。

获取 Service Fabric 群集清单。 群集清单包含群集的属性，包括群集上的不同节点类型、安全配置、错误和升级域拓扑，等等。这些属性是在部署独立群集时作为 ClusterConfig.JSON 文件的一部分指定的。 但是，群集清单中的大部分信息是在其他部署方案中部署群集期间由 Service Fabric 在内部生成的（例如，使用 Azure 门户时）。 群集清单的内容仅供参考，用户不应依赖于文件内容的格式或其解释。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-cluster-operation-cancel"></a>sfctl cluster operation-cancel
取消用户造成的错误操作。

以下 API 启动可以使用 CancelOperation 取消的错误操作：StartDataLoss、StartQuorumLoss、StartPartitionRestart、StartNodeTransition。 如果 force 为 false，则会正常停止并清理用户造成的指定操作。  如果 force 为 true，则会中止命令，并可能留下一些内部状态。  请谨慎将 force 指定为 true。 除非先在 force 设置为 false 的情况下对相同的 test 命令调用此 API，或者 test 命令已包含值为 OperationState.RollingBack 的 OperationState，否则，不允许在 force 设置为 true 的情况下调用此 API。 

澄清\: OperationState.RollingBack 表示系统将会/正在清理由于执行该命令而导致的内部系统状态。 如果 test 命令导致数据丢失，则系统不会还原数据。  例如，如果先调用 StartDataLoss，再调用此 API，则系统只会清理由于运行该命令而导致的内部状态。 如果命令的执行时间很长，导致数据丢失，则系统不会还原目标分区的数据。 

> [!NOTE]
> 如果在 force==true 的情况下调用此 API，则可能会留下内部状态。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --operation-id [必需] | 用于标识此 API 的调用的 GUID。  需将此参数传入相应的 GetProgress API。 |
| --force | 指示是否要正常回滚和清理执行用户造成的操作后修改的内部系统状态。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-cluster-operation-list"></a>sfctl cluster operation-list
获取根据提供的输入筛选的、用户造成的错误操作列表。

获取根据提供的输入筛选的、用户造成的错误操作列表。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --state-filter | 用于根据用户造成的操作的 OperationState 进行筛选。 <br> 65535 - 选择 All <br> 1 - 选择 Running <br> 2 - 选择 RollingBack <br>8 - 选择 Completed <br>16 - 选择 Faulted <br>32 - 选择 Cancelled <br>64 - 选择 ForceCancelled  <br>默认值\: 65535。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |
| --type-filter | 用于根据用户造成的操作的 OperationType 进行筛选。 <br> 65535 - 选择 All <br> 1 - 选择 PartitionDataLoss。 <br> 2 - 选择 PartitionQuorumLoss。 <br> 4 - 选择 PartitionRestart。 <br> 8 - 选择 NodeTransition。  <br> 默认值\: 65535。 |

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-cluster-provision"></a>sfctl cluster provision
预配 Service Fabric 群集的代码包或配置包。

验证和预配 Service Fabric 群集的代码包或配置包。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --cluster-manifest-file-path | 群集清单文件路径。 |
| --code-file-path | 群集代码包文件路径。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-cluster-recover-system"></a>sfctl cluster recover-system
向 Service Fabric 群集指出应该尝试恢复当前停滞在仲裁丢失状态的系统服务。

向 Service Fabric 群集指出应该尝试恢复当前停滞在仲裁丢失状态的系统服务。 仅当确定已关闭的副本无法恢复时，才执行此操作。 不当地使用此 API 可能导致潜在的数据丢失。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-cluster-report-health"></a>sfctl cluster report-health
发送有关 Service Fabric 群集的运行状况报告。

该报告必须包含有关运行状况报告及其所报告属性的源的信息。 该报告将发送到 Service Fabric 网关节点，后者会将其转发到运行状况存储。 该报告可能被网关接受但被运行状况存储在执行额外的验证后拒绝。 例如，运行状况存储可能会由于无效的参数（如过时的序列号）而拒绝该报告。 若要了解该报告是否已应用于运行状况存储中，请检查该报告是否显示在群集的 HealthEvents 中。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --health-property [必需] | 运行状况信息的属性。 <br><br> 一个实体可以有不同属性的运行状况报告。 该属性是一个字符串，不是固定的枚举，因此可使报告器灵活地对触发报告的状态条件进行分类。 例如，SourceId 为“LocalWatchdog”的报告器可以监视节点上的可用磁盘的状态，因此它可以报告该节点的“AvailableDisk”属性。 同一报告器可以监视节点连接，因此它可以报告同一节点的“Connectivity”属性。 在运行状况存储中，这些报告均被视为指定节点的单独运行状况事件。 与 SourceId 一起，该属性唯一地标识运行状况信息。 |
| --health-state    [必需] | 可能的值包括\:“Invalid”、“Ok”、“Warning”、“Error”、“Unknown”。 |
| --source-id       [必需] | 标识已生成运行状况信息的客户端/监视程序/系统组件的源名称。 |
| --description | 运行状况信息的说明。 <br><br> 它表示用于添加有关该报告的用户可读信息的自定义文本。 该说明的最大字符串长度为 4096 个字符。 如果所提供字符串的长度大于该值，它将被自动截断。 截断时，该说明的末尾字符包含一个标记“[Truncated]”，并且总字符串大小为 4096 个字符。 该标记的存在向用户指示截断已发生。 请注意，当截断时，该说明包含来自原始字符串的 4096 个以内的字符。 |
| --immediate | 一个用于指示是否应立即发送报告的标志。 <br><br> 运行状况报告将发送到 Service Fabric 网关应用程序，后者会将其转发到运行状况存储。 如果 Immediate 设置为 true，则报告将立即从 HTTP 网关发送至运行状况存储，而无论 HTTP 网关应用程序使用的 Fabric 客户端设置如何。 这对于应尽快发送的关键报告十分有用。 由于计时和其他情况，发送报告可能仍会失败，例如，在 HTTP 网关已关闭或消息无法到达网关的情况下。 如果 Immediate 设置为 false，则报告将基于来自 HTTP 网关的运行状况客户端设置发送。 因此，系统将根据 HealthReportSendInterval 配置对其进行批处理。 这是建议的设置，因为它可让运行状况客户端优化发往运行状况存储的运行状况报告消息以及运行状况报告处理。 默认情况下，报告不立即发送。 |
| --remove-when-expired | 该值指示是否在报告过期时从运行状况存储删除该报告。 <br><br> 如果设置为 true，报告在过期后将从运行状况存储中删除。 如果设置为 false，报告在过期时将被视为错误。 此属性的值在默认情况下为 false。 当客户端定期报告时，它们应将 RemoveWhenExpired 设置为 false（默认值）。 这样，如果报告器有问题（例如死锁）并且无法报告，那么在运行状况报告过期时该实体就会被评估为处于错误状态。 这会将该实体标记为处于“Error”运行状况状态。 |
| --sequence-number | 此运行状况报告的序列号（采用数字字符串形式）。 <br><br> 报告序列号由运行状况存储用来检测过时的报告。 如果未指定，序列号将在报告被添加时由运行状况客户端自动生成。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |
| --ttl | 此运行状况报告保持有效的持续时间。 此字段将 ISO8601 格式用于指定该持续时间。 <br><br> 当客户端定期报告时，它们应以高于生存时间的频率发送报告。 如果客户端以非定期的方式报告，它们可以将生存时间设置为无限。 生存时间过期时，包含运行状况信息的运行状况事件将从运行状况存储中删除（如果 RemoveWhenExpired 为 true），或者将会评估为处于错误状态（如果 RemoveWhenExpired 为 false）。 如果未指定，生存时间将默认为无限值。 |

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-cluster-select"></a>sfctl cluster select
连接到 Service Fabric 群集终结点。

如果连接到安全群集，请指定证书 (.crt) 和密钥文件 (.key) 的绝对路径，或指定包含此两者的单个文件 (.pem)。 不要同时指定上述两项。 （可选）如果连接到安全群集，则还要指定 CA 捆绑文件的绝对路径，或受信任 CA 证书的目录。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --endpoint [必需] | 群集终结点 URL，包括端口和 HTTP 或 HTTPS 前缀。 |
| --aad | 使用 Azure Active Directory 进行身份验证。 |
| --ca | 视为有效的 CA 证书目录的绝对路径，或 CA 捆绑文件的路径。 |
| --cert | 客户端证书文件的绝对路径。 |
| --key | 客户端证书密钥文件的绝对路径。 |
| --no-verify | 使用 HTTPS 时禁用证书验证。注意\: 这是一个不安全的选项，不应该用于生产环境。 |
| --pem | 客户端证书（.pem 文件）的绝对路径。 |

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-cluster-unprovision"></a>sfctl cluster unprovision
取消预配 Service Fabric 群集的代码包或配置包。

取消预配 Service Fabric 群集的代码包或配置包。 支持分别取消预配代码和配置。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --code-version | 群集代码包版本。 |
| --config-version | 群集清单版本。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-cluster-upgrade"></a>sfctl cluster upgrade
开始升级 Service Fabric 群集的代码或配置版本。

如果参数有效，验证提供的升级参数并对 Service Fabric 群集的代码或配置版本启动升级。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --app-health-map | 应用程序名称对的 JSON 编码字典以及引发错误之前的最大不正常百分比。 |
| --app-type-health-map | 应用程序类型名称对的 JSON 编码字典以及引发错误之前的最大不正常百分比。 |
| --code-version | 群集代码版本。 |
| --config-version | 群集配置版本。 |
| --delta-health-evaluation | 每个升级域完成后，启用增量运行状况评估，而不是绝对运行状况评估。 |
| --delta-unhealthy-nodes | 群集升级过程中允许的节点运行状况降级最大百分比。  默认值\: 10。 <br><br> 在开始升级时的节点状态与运行状况评估时的节点状态之间测得的增量值。 每个升级域升级完成后执行检查，确保群集的全局状态在允许的限制内。 |
| --failure-action | 可能的值包括\:“Invalid”、“Rollback”和“Manual”。 |
| --force-restart | 强制重启。 |
| --health-check-retry | 运行状况检查重试超时时间，以毫秒为单位。 |
| --health-check-stable | 运行状况检查稳定持续时间，以毫秒为单位。 |
| --health-check-wait | 运行状况检查等待持续时间，以毫秒为单位。 |
| --replica-set-check-timeout | 升级副本集检查超时时间，以秒为单位。 |
| --rolling-upgrade-mode | 可能的值包括\:“Invalid”、“UnmonitoredAuto”、“UnmonitoredManual”和“Monitored”。  默认值\: UnmonitoredAuto。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |
| --unhealthy-applications | 报告错误之前允许的最大不正常应用程序百分比。 <br><br> 例如，若要允许 10% 的应用程序处于不正常状态，此值为 10。 该百分比表示在将群集视为出错之前可处于不正常状态的应用程序的最大容许百分比。 如果未超过该百分比，但至少存在一个不正常的应用程序，则将运行状况评估为 Warning。 该百分比的计算方式是将不正常的应用程序数除以群集中的应用程序实例总数，不包括 ApplicationTypeHealthPolicyMap 中包含的应用程序类型的应用程序。 计算结果调高为整数，以便容忍少量应用程序出现一次失败。 |
| --unhealthy-nodes | 报告错误之前允许的最大不正常节点百分比。 <br><br> 例如，若要允许 10% 的节点处于不正常状态，此值为 10。 该百分比表示在将群集视为出错之前可处于不正常状态的节点的最大容许百分比。 如果未超过该百分比，但至少存在一个不正常的节点，则将运行状况评估为警告。 该百分比的计算方式是将不正常的节点数除以群集中的节点总数。 计算结果调高为整数，以便容忍少量节点上出现一次失败。 在大型群集中，始终会有一些要关闭或需要修复的节点，因此应配置此百分比以便容忍这种情况。 |
| --upgrade-domain-delta-unhealthy-nodes | 群集升级过程中允许的升级域节点运行状况降级最大百分比。  默认值\: 15。 <br><br> 在开始升级时的升级域节点状态与运行状况评估时的升级域节点状态之间测得的增量值。 每个升级域升级完成后对所有已完成的升级域执行检查，以便确保升级域的状态在允许的限制内。 |
| --upgrade-domain-timeout | 升级域超时时间，以毫秒为单位。 |
| --upgrade-timeout | 升级超时时间，以毫秒为单位。 |
| --warning-as-error | 将警告的严重性视为与错误相同。 |

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-cluster-upgrade-resume"></a>sfctl cluster upgrade-resume
使群集升级转移到下一个升级域。

在适用的情况下，使群集代码或配置升级转移到下一个升级域。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --upgrade-domain [必需] | 此群集升级的下一个升级域。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-cluster-upgrade-rollback"></a>sfctl cluster upgrade-rollback
回滚 Service Fabric 群集的升级。

回滚 Service Fabric 群集的代码或配置升级。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-cluster-upgrade-status"></a>sfctl cluster upgrade-status
获取当前群集升级的进度。

获取正在进行的群集升级的当前进度。 如果当前没有任何升级正在进行，则获取上次群集升级的最后一个状态。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-cluster-upgrade-update"></a>sfctl cluster upgrade-update
更新 Service Fabric 群集升级的升级参数。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --app-health-map | 应用程序名称对的 JSON 编码字典以及引发错误之前的最大不正常百分比。 |
| --app-type-health-map | 应用程序类型名称对的 JSON 编码字典以及引发错误之前的最大不正常百分比。 |
| --delta-health-evaluation | 每个升级域完成后，启用增量运行状况评估，而不是绝对运行状况评估。 |
| --delta-unhealthy-nodes | 群集升级过程中允许的节点运行状况降级最大百分比。  默认值\: 10。 <br><br> 在开始升级时的节点状态与运行状况评估时的节点状态之间测得的增量值。 每个升级域升级完成后执行检查，确保群集的全局状态在允许的限制内。 |
| --failure-action | 可能的值包括\:“Invalid”、“Rollback”和“Manual”。 |
| --force-restart | 强制重启。 |
| --health-check-retry | 运行状况检查重试超时时间，以毫秒为单位。 |
| --health-check-stable | 运行状况检查稳定持续时间，以毫秒为单位。 |
| --health-check-wait | 运行状况检查等待持续时间，以毫秒为单位。 |
| --replica-set-check-timeout | 升级副本集检查超时时间，以秒为单位。 |
| --rolling-upgrade-mode | 可能的值包括\:“Invalid”、“UnmonitoredAuto”、“UnmonitoredManual”和“Monitored”。  默认值\: UnmonitoredAuto。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |
| --unhealthy-applications | 报告错误之前允许的最大不正常应用程序百分比。 <br><br> 例如，若要允许 10% 的应用程序处于不正常状态，此值为 10。 该百分比表示在将群集视为出错之前可处于不正常状态的应用程序的最大容许百分比。 如果未超过该百分比，但至少存在一个不正常的应用程序，则将运行状况评估为 Warning。 该百分比的计算方式是将不正常的应用程序数除以群集中的应用程序实例总数，不包括 ApplicationTypeHealthPolicyMap 中包含的应用程序类型的应用程序。 计算结果调高为整数，以便容忍少量应用程序出现一次失败。 |
| --unhealthy-nodes | 报告错误之前允许的最大不正常节点百分比。 <br><br> 例如，若要允许 10% 的节点处于不正常状态，此值为 10。 该百分比表示在将群集视为出错之前可处于不正常状态的节点的最大容许百分比。 如果未超过该百分比，但至少存在一个不正常的节点，则将运行状况评估为警告。 该百分比的计算方式是将不正常的节点数除以群集中的节点总数。 计算结果调高为整数，以便容忍少量节点上出现一次失败。 在大型群集中，始终会有一些要关闭或需要修复的节点，因此应配置此百分比以便容忍这种情况。 |
| --upgrade-domain-delta-unhealthy-nodes | 群集升级过程中允许的升级域节点运行状况降级最大百分比。  默认值\: 15。 <br><br> 在开始升级时的升级域节点状态与运行状况评估时的升级域节点状态之间测得的增量值。 每个升级域升级完成后对所有已完成的升级域执行检查，以便确保升级域的状态在允许的限制内。 |
| --upgrade-domain-timeout | 升级域超时时间，以毫秒为单位。 |
| --upgrade-kind | 可能的值包括\:“Invalid”、“Rolling”、“Rolling_ForceRestart”。  默认值\: Rolling。 |
| --upgrade-timeout | 升级超时时间，以毫秒为单位。 |
| --warning-as-error | 将警告的严重性视为与错误相同。 |

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |



## <a name="next-steps"></a>后续步骤
- [安装](service-fabric-cli.md) Service Fabric CLI。
- 了解如何通过[示例脚本](/azure/service-fabric/scripts/sfctl-upgrade-application)使用 Service Fabric CLI。