---
title: Azure Service Fabric CLI- sfctl application| Microsoft Docs
description: 介绍 Service Fabric CLI sfctl application 命令。
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
ms.openlocfilehash: 40ec204f105b32c8b7d9e2dda6f6f3c3023b2d44
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2018
ms.locfileid: "39495452"
---
# <a name="sfctl-application"></a>sfctl application
创建、删除和管理应用程序及应用程序类型。

## <a name="commands"></a>命令

|命令|Description|
| --- | --- |
| create | 使用指定说明创建 Service Fabric 应用程序。 |
| delete | 删除现有 Service Fabric 应用程序。 |
| deployed | 获取部署在 Service Fabric 节点上的应用程序的相关信息。 |
| deployed-health | 获取部署在 Service Fabric 节点上的应用程序的运行状况。 |
| deployed-list | 获取部署在 Service Fabric 节点上的应用程序的列表。 |
| health | 获取 Service Fabric 应用程序运行状况。 |
| info | 获取 Service Fabric 应用程序的相关信息。 |
| list | 获取在 Service Fabric 群集中创建且与指定的筛选器匹配的应用程序列表。 |
| load | 获取 Service Fabric 应用程序的相关加载信息。 |
| manifest | 获取描述应用程序类型的清单。 |
| provision | 使用外部存储中的 .sfpkg 包或使用映像存储中的应用程序包向群集预配或注册 Service Fabric 应用程序类型。 |
| report-health | 发送有关 Service Fabric 应用程序的运行状况报告。 |
| type | 获取 Service Fabric 群集中与指定名称完全匹配的应用程序类型的列表。 |
| type-list | 获取 Service Fabric 群集中应用程序类型的列表。 |
| unprovision | 从群集中删除或注销 Service Fabric 应用程序类型。 |
| 升级 | 开始升级 Service Fabric 群集中的应用程序。 |
| upgrade-resume | 继续升级 Service Fabric 群集中的应用程序。 |
| upgrade-rollback | 开始回滚 Service Fabric 群集中当前正在进行的应用程序升级。 |
| upgrade-status | 获取对此应用程序执行的最近一次升级的详细信息。 |
| upload | 将 Service Fabric 应用程序包复制到映像存储区。 |

## <a name="sfctl-application-create"></a>sfctl application create
使用指定说明创建 Service Fabric 应用程序。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --app-name [必需] | 应用程序名称，包括“fabric\:”URI 方案。 |
| --app-type [必需] | 在应用程序清单中找到的应用程序类型名称。 |
| --app-version [必需] | 应用程序清单中定义的应用程序类型的版本。 |
| --max-node-count | Service Fabric 为此应用程序保留的容量的最大节点数。 请注意，这并不表示此应用程序的服务放置在所有这些节点上。 |
| --metrics | 应用程序容量指标说明的 JSON 编码列表。 指标定义为一个名称，与应用程序所在的每个节点的一组容量关联。 |
| --min-node-count | Service Fabric 为此应用程序保留的容量的最小节点数。 请注意，这并不表示此应用程序的服务放置在所有这些节点上。 |
| --parameters | 创建应用程序时应用的应用程序参数替代的 JSON 编码列表。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-application-delete"></a>sfctl application delete
删除现有 Service Fabric 应用程序。

必须先创建应用程序才能进行删除。 删除应用程序会删除该应用程序中包含的所有服务。 默认情况下，Service Fabric 尝试正常关闭服务副本，然后删除服务。 但是，如果服务无法正常关闭，删除操作可能需要很长时间，也可能出现停滞。 使用可选 ForceRemove 标志跳过正常关闭序列，强制删除应用程序及其所有服务。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --application-id [必需] | 应用程序的标识。 这通常是不带“fabric\:”URI 方案的应用程序全名。 从版本 6.0 开始，分层名称以“\~”字符隔开。 例如，如果应用程序名称为“fabric\:/myapp/app1”，则 6.0 及更高版本中的应用程序标识为“myapp\~app1”，在以前的版本中为“myapp/app1”。 |
| --force-remove | 强制删除 Service Fabric 应用程序或服务，跳过正常关闭序列。 若因服务代码中的问题而无法正常关闭副本，导致删除应用程序或服务操作超时，可使用此参数强制删除该应用程序或服务。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-application-deployed"></a>sfctl application deployed
获取部署在 Service Fabric 节点上的应用程序的相关信息。

如果提供的应用程序 ID 是系统应用程序的，则此查询将返回系统应用程序信息。 结果包括处于活动、正在激活和正在下载状态的已部署应用程序。 此查询要求节点名称对应于群集上的某个节点。 如果提供的节点名称未指向群集上的任何活动 Service Fabric 节点，则查询将失败。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --application-id [必需] | 应用程序的标识。 这通常是不带“fabric\:”URI 方案的应用程序全名。 从版本 6.0 开始，分层名称以“\~”字符隔开。 例如，如果应用程序名称为“fabric\:/myapp/app1”，则 6.0 及更高版本中的应用程序标识为“myapp\~app1”，在以前的版本中为“myapp/app1”。 |
| --node-name [必需] | 节点的名称。 |
| --include-health-state | 包含实体的运行状况。 如果此参数为 false 或未指定，则返回的运行状况为“Unknown”。 设置为 true，查询将并行转到节点和运行状况系统服务，然后将结果合并。 因此，查询开销更高，并可能需要更长的时间。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-application-deployed-health"></a>sfctl application deployed-health
获取部署在 Service Fabric 节点上的应用程序的运行状况。

获取部署在 Service Fabric 节点上的应用程序的运行状况。 使用 EventsHealthStateFilter 可以选择性地根据运行状况筛选部署的应用程序上报告的 HealthEvent 对象的集合。 使用 DeployedServicePackagesHealthStateFilter 可以选择性地根据运行状况筛选 DeployedServicePackageHealth 子项。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --application-id [必需] | 应用程序的标识。 这通常是不带“fabric\:”URI 方案的应用程序全名。 从版本 6.0 开始，分层名称以“\~”字符隔开。 例如，如果应用程序名称为“fabric\:/myapp/app1”，则 6.0 及更高版本中的应用程序标识为“myapp\~app1”，在以前的版本中为“myapp/app1”。 |
| --node-name [必需] | 节点的名称。 |
| --deployed-service-packages-health-state-filter | 用于根据运行状况筛选服务包运行状况查询结果中返回的已部署服务包运行状况对象。 此参数的可能值包括以下运行状态之一的整数值。 仅返回与筛选器匹配的已部署服务包。 所有已部署服务包用于评估已部署应用程序的聚合运行状况。 如果未指定，则返回所有项。 状态值为基于标志的枚举，因此该值可以是使用按位“OR”运算符获取的值的组合。 例如，如果提供的值为 6，则返回 HealthState 值为 OK (2) 和 Warning (4) 的服务包的运行状态。  <br> - Default - 默认值。 匹配任何 HealthState。 值为 0。  <br> - None - 不与任何 HealthState 值匹配的筛选器。 未返回有关给定状态集合的结果时使用。 值为 1。  <br> - Ok - 与 HealthState 值为 OK 的输入匹配的筛选器。 值为 2。  <br> - Warning - 与 HealthState 值为 Warning 的输入匹配的筛选器。 值为 4。  <br> - Error - 与 HealthState 值为 Error 的输入匹配的筛选器。 值为 8。  <br> - All - 与具有任意 HealthState 值的输入匹配的筛选器。 值为 65535。 |
| --events-health-state-filter | 用于根据运行状况筛选返回的 HealthEvent 对象集合。 此参数的可能值包括以下运行状态之一的整数值。 仅返回与筛选器匹配的事件。 所有事件用于评估聚合运行状态。 如果未指定，则返回所有项。 状态值为基于标志的枚举，因此该值可以是使用按位“OR”运算符获取的值的组合。 例如，如果提供的值为 6，则返回 HealthState 值为 OK (2) 和 Warning (4) 的所有事件。  <br> - Default - 默认值。 匹配任何 HealthState。 值为 0。  <br> - None - 不与任何 HealthState 值匹配的筛选器。 未返回有关给定状态集合的结果时使用。 值为 1。  <br> - Ok - 与 HealthState 值为 OK 的输入匹配的筛选器。 值为 2。  <br> - Warning - 与 HealthState 值为 Warning 的输入匹配的筛选器。 值为 4。  <br> - Error - 与 HealthState 值为 Error 的输入匹配的筛选器。 值为 8。  <br> - All - 与具有任意 HealthState 值的输入匹配的筛选器。 值为 65535。 |
| --exclude-health-statistics | 指示运行状况统计数据是否应作为查询结果的一部分返回。 默认值为 False。 统计信息显示处于 Ok、Warning 和 Error 运行状况的子实体数。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-application-deployed-list"></a>sfctl application deployed-list
获取部署在 Service Fabric 节点上的应用程序的列表。

获取部署在 Service Fabric 节点上的应用程序的列表。 除非显式按 ID 查询，否则结果不会包含有关已部署系统应用程序的信息 结果包括处于活动、正在激活和正在下载状态的已部署应用程序。 此查询要求节点名称对应于群集上的某个节点。 如果提供的节点名称未指向群集上的任何活动 Service Fabric 节点，则查询将失败。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --node-name [必需] | 节点的名称。 |
| --continuation-token | 继续标记参数用于获取下一组结果。 如果单个响应无法容纳来自系统的结果，则 API 响应中包括含有非空值的继续标记。 当此值传递到下一个 API 调用时，API 返回下一组结果。 如果没有更多结果，则该继续标记不包含值。 不应将此参数的值进行 URL 编码。 |
| --include-health-state | 包含实体的运行状况。 如果此参数为 false 或未指定，则返回的运行状况为“Unknown”。 设置为 true，查询将并行转到节点和运行状况系统服务，然后将结果合并。 因此，查询开销更高，并可能需要更长的时间。 |
| --max-results | 作为分页查询的一部分返回的最大结果数。 此参数定义返回结果数的上限。 如果根据配置中定义的最大消息大小限制，无法将这些结果容纳到消息中，则返回的结果数可能小于指定的最大结果数。 如果此参数为零或者未指定，则分页查询包含返回消息中最多可容纳的结果数。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-application-health"></a>sfctl application health
获取 Service Fabric 应用程序运行状况。

返回 Service Fabric 应用程序的运行状态。 响应报告“Ok”、“Error”或“Warning”运行状态。 如果未在运行状况存储中找到实体，则返回 Error。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --application-id [必需] | 应用程序的标识。 这通常是不带“fabric\:”URI 方案的应用程序全名。 从版本 6.0 开始，分层名称以“\~”字符隔开。 例如，如果应用程序名称为“fabric\:/myapp/app1”，则 6.0 及更高版本中的应用程序标识为“myapp\~app1”，在以前的版本中为“myapp/app1”。 |
| --deployed-applications-health-state-filter | 用于根据运行状况筛选应用程序运行状况查询结果中返回的已部署应用程序运行状况对象。 此参数的可能值包括以下运行状态之一的整数值。 仅返回与筛选器匹配的已部署应用程序。 所有已部署应用程序都用于评估聚合运行状况。 如果未指定，则返回所有项。 状态值为基于标志的枚举，因此该值可以是使用按位“OR”运算符获取的值的组合。 例如，如果提供的值为 6，则返回 HealthState 值为 OK (2) 和 Warning (4) 的已部署应用程序的运行状况。  <br> - Default - 默认值。 匹配任何 HealthState。 值为 0。  <br> - None - 不与任何 HealthState 值匹配的筛选器。 未返回有关给定状态集合的结果时使用。 值为 1。  <br> - Ok - 与 HealthState 值为 OK 的输入匹配的筛选器。 值为 2。  <br> - Warning - 与 HealthState 值为 Warning 的输入匹配的筛选器。 值为 4。  <br> - Error - 与 HealthState 值为 Error 的输入匹配的筛选器。 值为 8。  <br> - All - 与具有任意 HealthState 值的输入匹配的筛选器。 值为 65535。 |
| --events-health-state-filter | 用于根据运行状况筛选返回的 HealthEvent 对象集合。 此参数的可能值包括以下运行状态之一的整数值。 仅返回与筛选器匹配的事件。 所有事件用于评估聚合运行状态。 如果未指定，则返回所有项。 状态值为基于标志的枚举，因此该值可以是使用按位“OR”运算符获取的值的组合。 例如，如果提供的值为 6，则返回 HealthState 值为 OK (2) 和 Warning (4) 的所有事件。  <br> - Default - 默认值。 匹配任何 HealthState。 值为 0。  <br> - None - 不与任何 HealthState 值匹配的筛选器。 未返回有关给定状态集合的结果时使用。 值为 1。  <br> - Ok - 与 HealthState 值为 OK 的输入匹配的筛选器。 值为 2。  <br> - Warning - 与 HealthState 值为 Warning 的输入匹配的筛选器。 值为 4。  <br> - Error - 与 HealthState 值为 Error 的输入匹配的筛选器。 值为 8。  <br> - All - 与具有任意 HealthState 值的输入匹配的筛选器。 值为 65535。 |
| --exclude-health-statistics | 指示运行状况统计数据是否应作为查询结果的一部分返回。 默认值为 False。 统计信息显示处于 Ok、Warning 和 Error 运行状况的子实体数。 |
| --services-health-state-filter | 用于根据运行状况筛选服务运行状况查询结果中返回的服务运行状况对象。 此参数的可能值包括以下运行状态之一的整数值。 仅返回与筛选器匹配的服务。 所有服务都用于评估聚合运行状况。 如果未指定，则返回所有项。 状态值为基于标志的枚举，因此该值可以是使用按位“OR”运算符获取的值的组合。 例如，如果提供的值为“6”，则返回 HealthState 值为 OK (2) 和 Warning (4) 的服务的运行状况。  <br> - Default - 默认值。 匹配任何 HealthState。 值为 0。  <br> - None - 不与任何 HealthState 值匹配的筛选器。 未返回有关给定状态集合的结果时使用。 值为 1。  <br> - Ok - 与 HealthState 值为 OK 的输入匹配的筛选器。 值为 2。  <br> - Warning - 与 HealthState 值为 Warning 的输入匹配的筛选器。 值为 4。  <br> - Error - 与 HealthState 值为 Error 的输入匹配的筛选器。 值为 8。  <br> - All - 与具有任意 HealthState 值的输入匹配的筛选器。 值为 65535。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-application-info"></a>sfctl application info
获取 Service Fabric 应用程序的相关信息。

返回 Service Fabric 群集中已创建或正在创建且名称与指定为参数的应用程序匹配的应用程序相关信息。 响应包括名称、类型、状态、参数以及应用程序的其他相关详细信息。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --application-id [必需] | 应用程序的标识。 这通常是不带“fabric\:”URI 方案的应用程序全名。 从版本 6.0 开始，分层名称以“\~”字符隔开。 例如，如果应用程序名称为“fabric\:/myapp/app1”，则 6.0 及更高版本中的应用程序标识为“myapp\~app1”，在以前的版本中为“myapp/app1”。 |
| --exclude-application-parameters | 该标志指定应用程序参数是否排除在结果之外。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-application-list"></a>sfctl application list
获取在 Service Fabric 群集中创建且与指定的筛选器匹配的应用程序列表。

获取 Service Fabric 群集中已创建或正在创建且与指定的筛选器匹配的应用程序相关信息。 响应包括名称、类型、状态、参数以及应用程序的其他相关详细信息。 如果一页无法容纳这些应用程序，则返回一页结果及一个继续标记，该标记可用于获取下一页。 不能同时指定筛选器 ApplicationTypeName 和 ApplicationDefinitionKindFilter。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --application-definition-kind-filter | 用来筛选 ApplicationDefinitionKind，它是用来定义 Service Fabric 应用程序的机制。  <br> - Default - 默认值，它执行与选择“所有”时相同的功能。 值为 0。  <br> - All - 与任何 ApplicationDefinitionKind 值输入匹配的筛选器。 值为 65535。  <br> - ServiceFabricApplicationDescription - 与 ApplicationDefinitionKind 值 ServiceFabricApplicationDescription 输入匹配的筛选器。 值为 1。  <br> - Compose - 与 ApplicationDefinitionKind 值 Compose 输入匹配的筛选器。 值为 2。 |
| --application-type-name | 用于筛选要查询的应用程序的应用程序类型名称。 此值不应包含应用程序类型版本。 |
| --continuation-token | 继续标记参数用于获取下一组结果。 如果单个响应无法容纳来自系统的结果，则 API 响应中包括含有非空值的继续标记。 当此值传递到下一个 API 调用时，API 返回下一组结果。 如果没有更多结果，则该继续标记不包含值。 不应将此参数的值进行 URL 编码。 |
| --exclude-application-parameters | 该标志指定应用程序参数是否排除在结果之外。 |
| --max-results | 作为分页查询的一部分返回的最大结果数。 此参数定义返回结果数的上限。 如果根据配置中定义的最大消息大小限制，无法将这些结果容纳到消息中，则返回的结果数可能小于指定的最大结果数。 如果此参数为零或者未指定，则分页查询包含返回消息中最多可容纳的结果数。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-application-load"></a>sfctl application load
获取 Service Fabric 应用程序的相关加载信息。

返回 Service Fabric 群集中已创建或正在创建且名称与指定为参数的应用程序匹配的应用程序加载信息。 响应包括名称、最小节点数、最大节点数、应用程序目前占用的节点数以及有关应用程序的应用程序加载指标信息。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --application-id [必需] | 应用程序的标识。 这通常是不带“fabric\:”URI 方案的应用程序全名。 从版本 6.0 开始，分层名称以“\~”字符隔开。 例如，如果应用程序名称为“fabric\:/myapp/app1”，则 6.0 及更高版本中的应用程序标识为“myapp\~app1”，在以前的版本中为“myapp/app1”。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-application-manifest"></a>sfctl application manifest
获取描述应用程序类型的清单。

响应包含字符串形式的应用程序清单 XML。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --application-type-name    [必需] | 应用程序类型的名称。 |
| --application-type-version [必需] | 应用程序类型的版本。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-application-provision"></a>sfctl application provision
使用外部存储中的 .sfpkg 包或使用映像存储中的应用程序包向群集预配或注册 Service Fabric 应用程序类型。

向群集预配 Service Fabric 应用程序类型。 必须完成此操作才能实例化任意新应用程序。 可以在 relativePathInImageStore 指定的应用程序包上或者使用外部 .sfpkg 的 URI 执行预配操作。 除非设置了 --external-provision，否则此命令需要映像存储预配。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --application-package-download-uri | “.sfpkg”应用程序包的路径，可使用 HTTP 或 HTTPS 协议从该处下载应用程序包。 <br><br> 仅适用于预配种类的外部存储。 应用程序包可以存储在外部存储中，该存储提供 GET 操作来下载文件。 支持的协议为 HTTP 和 HTTPS，并且路径必须允许读取访问权限。 |
| --application-type-build-path | 仅适用于预配种类的映像存储。 应用程序包在先前的上传操作中指定的映像存储中的相对路径。 |
| --application-type-name | 仅适用于预配种类的外部存储。 应用程序类型名称表示在应用程序清单中找到的应用程序类型的名称。 |
| --application-type-version | 仅适用于预配种类的外部存储。 应用程序类型版本表示在应用程序清单中找到的应用程序类型的版本。 |
| --external-provision | 可以从其中注册或预配应用程序包的位置。 指示预配针对之前上传到外部存储的应用程序包。 应用程序包以扩展名 *.sfpkg 结尾。 |
| --no-wait | 指示预配是否应当以异步方式进行。 <br><br> 当设置为 true 时，预配操作将在请求被系统接受时返回，并且预配操作继续进行，没有任何超时限制。 默认值为 false。 对于大型应用程序包，建议将值设置为 true。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-application-report-health"></a>sfctl application report-health
发送有关 Service Fabric 应用程序的运行状况报告。

报告指定的 Service Fabric 应用程序的运行状况。 该报告必须包含有关运行状况报告及其所报告属性的源的信息。 报告将发送到 Service Fabric 网关应用程序，后者会将其转发到运行状况存储。 该报告可能被网关接受但被运行状况存储在执行额外的验证后拒绝。 例如，运行状况存储可能会由于无效的参数（如过时的序列号）而拒绝该报告。 若要了解该报告是否已在运行状况存储中应用，请获取应用程序运行状况并检查该报告是否显示。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --application-id [必需] | 应用程序的标识。 <br><br> 这通常是不带“fabric\:”URI 方案的应用程序全名。 从版本 6.0 开始，分层名称以“\~”字符隔开。 例如，如果应用程序名称为“fabric\:/myapp/app1”，则 6.0 及更高版本中的应用程序标识为“myapp\~app1”，在以前的版本中为“myapp/app1”。 |
| --health-property [必需] | 运行状况信息的属性。 <br><br> 一个实体可以有不同属性的运行状况报告。 该属性是一个字符串，不是固定的枚举，因此可使报告器灵活地对触发报告的状态条件进行分类。 例如，SourceId 为“LocalWatchdog”的报告器可以监视节点上的可用磁盘的状态，因此它可以报告该节点的“AvailableDisk”属性。 同一报告器可以监视节点连接，因此它可以报告同一节点的“Connectivity”属性。 在运行状况存储中，这些报告均被视为指定节点的单独运行状况事件。 与 SourceId 一起，该属性唯一地标识运行状况信息。 |
| --health-state    [必需] | 可能的值包括\:“Invalid”、“Ok”、“Warning”、“Error”、“Unknown”。 |
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

## <a name="sfctl-application-type"></a>sfctl application type
获取 Service Fabric 群集中与指定名称完全匹配的应用程序类型的列表。

返回 Service Fabric 群集中已预配或正在预配的应用程序类型的相关信息。 这些结果为名称与指定为参数的应用程序类型完全匹配，并且符合给定查询参数的应用程序类型。 返回与应用程序类型名称匹配的所有应用程序类型版本，每个版本作为一个应用程序类型返回。 响应包括名称、版本、状态以及有关应用程序类型的其他详细信息。 这是分页查询，如果一页无法容纳所有应用程序类型，则返回一页结果及一个继续标记，该标记可用于获取下一页。 例如，如果有 10 个应用程序类型，但一页仅能容纳前 3 个应用程序类型，或者最大结果数设置为 3，则返回 3 个结果。 要访问其他结果，请使用下一查询中返回的继续标记来检索后续页面。 如果没有后续页面，则返回空继续标记。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --application-type-name [必需] | 应用程序类型的名称。 |
| --application-type-version | 应用程序类型的版本。 |
| --continuation-token | 继续标记参数用于获取下一组结果。 如果单个响应无法容纳来自系统的结果，则 API 响应中包括含有非空值的继续标记。 当此值传递到下一个 API 调用时，API 返回下一组结果。 如果没有更多结果，则该继续标记不包含值。 不应将此参数的值进行 URL 编码。 |
| --exclude-application-parameters | 该标志指定应用程序参数是否排除在结果之外。 |
| --max-results | 作为分页查询的一部分返回的最大结果数。 此参数定义返回结果数的上限。 如果根据配置中定义的最大消息大小限制，无法将这些结果容纳到消息中，则返回的结果数可能小于指定的最大结果数。 如果此参数为零或者未指定，则分页查询包含返回消息中最多可容纳的结果数。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-application-type-list"></a>sfctl application type-list
获取 Service Fabric 群集中应用程序类型的列表。

返回 Service Fabric 群集中已预配或正在预配的应用程序类型的相关信息。 应用程序类型的每个版本作为一个应用程序类型返回。 响应包括名称、版本、状态以及有关应用程序类型的其他详细信息。 这是分页查询，如果一页无法容纳所有应用程序类型，则返回一页结果及一个继续标记，该标记可用于获取下一页。 例如，如果有 10 个应用程序类型，但一页仅能容纳前 3 个应用程序类型，或者最大结果数设置为 3，则返回 3 个结果。 要访问其他结果，请使用下一查询中返回的继续标记来检索后续页面。 如果没有后续页面，则返回空继续标记。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --application-type-definition-kind-filter | 用来筛选 ApplicationTypeDefinitionKind，它是用来定义 Service Fabric 应用程序类型的机制。  <br> - Default - 默认值，它执行与选择“所有”时相同的功能。 值为 0。  <br> - All - 与任何 ApplicationTypeDefinitionKind 值输入匹配的筛选器。 值为 65535。  <br> - ServiceFabricApplicationPackage - 将输入与 ApplicationTypeDefinitionKind 值 ServiceFabricApplicationPackage 进行匹配的筛选器。 值为 1。  <br> - Compose - 与 ApplicationTypeDefinitionKind 值 Compose 输入匹配的筛选器。 值为 2。 |
| --continuation-token | 继续标记参数用于获取下一组结果。 如果单个响应无法容纳来自系统的结果，则 API 响应中包括含有非空值的继续标记。 当此值传递到下一个 API 调用时，API 返回下一组结果。 如果没有更多结果，则该继续标记不包含值。 不应将此参数的值进行 URL 编码。 |
| --exclude-application-parameters | 该标志指定应用程序参数是否排除在结果之外。 |
| --max-results | 作为分页查询的一部分返回的最大结果数。 此参数定义返回结果数的上限。 如果根据配置中定义的最大消息大小限制，无法将这些结果容纳到消息中，则返回的结果数可能小于指定的最大结果数。 如果此参数为零或者未指定，则分页查询包含返回消息中最多可容纳的结果数。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-application-unprovision"></a>sfctl application unprovision
从群集中删除或注销 Service Fabric 应用程序类型。

仅当已删除应用程序类型的所有应用程序实例时，才可执行此操作。 注销应用程序类型后，无法为此特定应用程序类型创建新的应用程序实例。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --application-type-name    [必需] | 应用程序类型的名称。 |
| --application-type-version [必需] | 应用程序清单中定义的应用程序类型的版本。 |
| --async-parameter | 此标志指示取消预配是否应当以异步方式进行。 当设置为 true 时，取消预配操作将在请求被系统接受时返回，并且取消预配操作继续进行，没有任何超时限制。 默认值为 false。 但是，对于已预配的大型应用程序包，建议将其设置为 true。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-application-upgrade"></a>sfctl application upgrade
开始升级 Service Fabric 群集中的应用程序。

验证提供的应用程序升级参数，如果参数有效，则开始升级应用程序。 请注意，升级说明将替换现有应用程序说明。 这意味着，如果未指定参数，应用程序的现有参数将替换为空的参数列表。 这会导致应用程序使用应用程序清单中的默认参数值。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --application-id [必需] | 应用程序的标识。 <br><br> 这通常是不带“fabric\:”URI 方案的应用程序全名。 从版本 6.0 开始，分层名称以“\~”字符隔开。 例如，如果应用程序名称为“fabric\:/myapp/app1”，则 6.0 及更高版本中的应用程序标识为“myapp\~app1”，在以前的版本中为“myapp/app1”。 |
| --application-version [必需] | 目标应用程序版本。 |
| --parameters [必需] | 升级应用程序时应用的应用程序参数替代的 JSON 编码列表。 |
| --default-service-health-policy | 默认使用的健康策略的 JSON 编码规范，用于评估服务类型的运行状况。 |
| --failure-action | 监视的升级违反监视策略或健康策略时要执行的操作。 |
| --force-restart | 即使代码版本没有改变，也在升级过程中强制重新启动进程。 |
| --health-check-retry-timeout | 执行失败操作前，当应用程序或群集不正常时，重试运行状况评估所需的时间。 以毫秒为单位。  默认值\: PT0H10M0S。 |
| --health-check-stable-duration | 升级继续到下一升级域之前，应用程序或群集必须保持正常的时长。 以毫秒为单位。  默认值\: PT0H2M0S。 |
| --health-check-wait-duration | 应用运行状况策略之前，完成升级域后等待的时间长度。 以毫秒为单位。  默认值\: 0。 |
| --max-unhealthy-apps | 允许的已部署的不正常应用程序的最大百分比。 由介于 0 到 100 间的数字表示。 |
| --mode | 在滚动升级期间用于监视运行状况的模式。  默认值\: UnmonitoredAuto。 |
| --replica-set-check-timeout | 出现意外问题时，阻止处理升级域并防止可用性丢失的最大时长。 以秒为度量单位。 |
| --service-health-policy | 包含每个服务类型名称的服务类型健康策略的 JSON 编码映射。 映射默认为空。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |
| --upgrade-domain-timeout | 执行 FailureAction 前，每个升级域需等待的时长。 以毫秒为单位。  默认值\: P10675199DT02H48M05.4775807S。 |
| --upgrade-timeout | 执行 FailureAction 前，完成整个升级需等待的时长。 以毫秒为单位。  默认值\: P10675199DT02H48M05.4775807S。 |
| --warning-as-error | 将运行状况评估警告的严重级别视为与与错误相同。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-application-upgrade-resume"></a>sfctl application upgrade-resume
继续升级 Service Fabric 群集中的应用程序。

恢复不受监控的手动 Service Fabric 应用程序升级。 Service Fabric 每次升级一个升级域。 对于不受监控的手动升级，Service Fabric 完成升级域后，会等待调用此 API，然后再继续升级下一个升级域。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --application-id [必需] | 应用程序的标识。 这通常是不带“fabric\:”URI 方案的应用程序全名。 从版本 6.0 开始，分层名称以“\~”字符隔开。 例如，如果应用程序名称为“fabric\:/myapp/app1”，则 6.0 及更高版本中的应用程序标识为“myapp\~app1”，在以前的版本中为“myapp/app1”。 |
| --upgrade-domain-name [必需] | 要恢复升级的升级域的名称。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-application-upgrade-rollback"></a>sfctl application upgrade-rollback
开始回滚 Service Fabric 群集中当前正在进行的应用程序升级。

开始将当前应用程序升级回滚到以前的版本。 此 API 只可用于回滚当前正在进行的、前滚到新版本的升级。 如果应用程序当前未在升级，请使用 StartApplicationUpgrade API 将其升级到所需版本，包括回滚到以前的版本。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --application-id [必需] | 应用程序的标识。 这通常是不带“fabric\:”URI 方案的应用程序全名。 从版本 6.0 开始，分层名称以“\~”字符隔开。 例如，如果应用程序名称为“fabric\:/myapp/app1”，则 6.0 及更高版本中的应用程序标识为“myapp\~app1”，在以前的版本中为“myapp/app1”。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-application-upgrade-status"></a>sfctl application upgrade-status
获取对此应用程序执行的最近一次升级的详细信息。

返回有关最新应用程序升级状态的信息，以及可帮助调试应用程序运行状况问题的详细信息。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --application-id [必需] | 应用程序的标识。 这通常是不带“fabric\:”URI 方案的应用程序全名。 从版本 6.0 开始，分层名称以“\~”字符隔开。 例如，如果应用程序名称为“fabric\:/myapp/app1”，则 6.0 及更高版本中的应用程序标识为“myapp\~app1”，在以前的版本中为“myapp/app1”。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-application-upload"></a>sfctl application upload
将 Service Fabric 应用程序包复制到映像存储区。

（可选）显示包中每个文件的上传进度。 上传进度发送到 `stderr`。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --path [必需] | 本地应用程序包的路径。 |
| --imagestore-string | 应用程序包上传到的目标映像存储区。  默认值\: fabric\:ImageStore。 |
| --show-progress | 显示大型包的文件上传进度。 |

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
