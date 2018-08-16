---
title: Azure Service Fabric CLI - sfctl service | Microsoft Docs
description: 介绍 Service Fabric CLI sfctl service 命令。
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
ms.openlocfilehash: 84c2faaf137e19d78e7e17527feb50baebf8041b
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2018
ms.locfileid: "39494568"
---
# <a name="sfctl-service"></a>sfctl service
创建、删除和管理服务、服务类型与服务包。

## <a name="commands"></a>命令

|命令|Description|
| --- | --- |
| app-name | 获取服务的 Service Fabric 应用程序名称。 |
| code-package-list | 获取部署在 Service Fabric 节点上的代码包的列表。 |
| create | 创建指定的 Service Fabric 服务。 |
| delete | 删除现有的 Service Fabric 服务。 |
| deployed-type | 获取有关 Service Fabric 群集中节点上部署的应用程序的指定服务类型的信息。 |
| deployed-type-list | 获取列表，其中包含有关 Service Fabric 群集中节点上部署的应用程序中的服务类型的信息。 |
| description | 获取现有 Service Fabric 服务的说明。 |
| get-container-logs | 获取 Service Fabric 节点上部署的容器的容器日志。 |
| health | 获取指定 Service Fabric 服务的运行状况。 |
| info | 获取有关属于 Service Fabric 应用程序的特定服务的信息。 |
| list | 获取属于根据应用程序 ID 指定的应用程序的所有服务的相关信息 |
| manifest | 获取描述服务类型的清单。 |
| package-deploy | 将与指定服务清单关联的包下载到指定节点上的映像缓存。 |
| package-health | 获取针对 Service Fabric 节点和应用程序部署的特定应用程序的服务包运行状况的相关信息。 |
| package-info | 获取完全与指定的名称匹配的 Service Fabric 节点上部署的服务包列表。 |
| package-list | 获取部署在 Service Fabric 节点上的服务包的列表。 |
| recover | 向 Service Fabric 群集指出应该尝试恢复当前停滞在仲裁丢失状态的指定服务。 |
| report-health | 发送有关 Service Fabric 服务的运行状况报告。 |
| resolve | 解析 Service Fabric 分区。 |
| type-list | 获取列表，其中包含有关 Service Fabric 群集中预配的应用程序类型支持的服务类型的信息。 |
| update | 使用给定的更新说明更新指定的服务。 |

## <a name="sfctl-service-app-name"></a>sfctl service app-name
获取服务的 Service Fabric 应用程序名称。

获取指定服务的应用程序名称。 如果具有提供的服务 ID 的服务不存在，则返回 404 FABRIC_E_SERVICE_DOES_NOT_EXIST 错误。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --service-id [必需] | 服务的标识。 此 ID 通常是不带“fabric\:”URI 方案的服务全名。 从版本 6.0 开始，分层名称以“\~”字符隔开。 例如，如果服务名称为“fabric\:/myapp/app1/svc1”，则 6.0 及更高版本中的服务标识为“myapp\~app1\~svc1”，在以前的版本中为“myapp/app1/svc1”。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-service-code-package-list"></a>sfctl service code-package-list
获取部署在 Service Fabric 节点上的代码包的列表。

获取部署在给定应用程序的 Service Fabric 节点上的代码包的列表。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --application-id [必需] | 应用程序的标识。 这通常是不带“fabric\:”URI 方案的应用程序全名。 从版本 6.0 开始，分层名称以“\~”字符隔开。 例如，如果应用程序名称为“fabric\:/myapp/app1”，则 6.0 及更高版本中的应用程序标识为“myapp\~app1”，在以前的版本中为“myapp/app1”。 |
| --node-name [必需] | 节点的名称。 |
| --code-package-name | 在 Service Fabric 群集中注册为应用程序类型一部分的服务清单中指定的代码包的名称。 |
| --service-manifest-name | 在 Service Fabric 群集中注册为应用程序类型一部分的服务清单的名称。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-service-create"></a>sfctl service create
创建指定的 Service Fabric 服务。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --app-id       [必需] | 应用程序的标识。 这通常是不带“fabric\:”URI 方案的应用程序全名。 从版本 6.0 开始，分层名称以“\~”字符隔开。 例如，如果应用程序名称为“fabric\:/myapp/app1”，则 6.0 及更高版本中的应用程序标识为“myapp\~app1”，在以前的版本中为“myapp/app1”。 |
| --name         [必需] | 服务的名称。 应是应用程序 ID 的子级。这是包括 `fabric\:` URI 的完整名称。 例如，服务 `fabric\:/A/B` 是应用程序 `fabric\:/A` 的子级。 |
| --service-type [必需] | 服务类型的名称。 |
| --activation-mode | 服务包的激活模式。 |
| --constraints | 字符串形式的放置约束。 放置约束是节点属性中的布尔表达式，用于根据服务要求将服务限制到特定的节点。 例如，若要在 NodeType 为 blue 的节点上放置服务，请指定以下语句\: "NodeColor == blue"。 |
| --correlated-service | 要关联的目标服务的名称。 |
| --correlation | 使用对齐关联将服务与现有服务相关联。 |
| --dns-name | 要创建的服务的 DNS 名称。 必须为此设置启用 Service Fabric DNS 系统服务。 |
| --instance-count | 实例计数。 仅适用于无状态服务。 |
| --int-scheme | 指示应在一系列无符号整数之间将服务统一分区。 |
| --int-scheme-count | 整数键范围内要创建的分区数（适用于统一整数分区方案）。 |
| --int-scheme-high | 键整数范围的下限（适用于统一整数分区方案）。 |
| --int-scheme-low | 键整数范围的上限（适用于统一整数分区方案）。 |
| --load-metrics | 对各节点上的服务进行负载均衡时使用的指标的 JSON 编码列表。 |
| --min-replica-set-size | 数字形式的最小副本集大小。 仅适用于有状态服务。 |
| --move-cost | 指定服务的移动成本。 可能的值为\:“Zero”、“Low”、“Medium”和“High”。 |
| --named-scheme | 指示服务应包含多个命名分区。 |
| --named-scheme-list | 要在其中将服务分区的名称的 JSON 编码列表（如果使用命名分区方案）。 |
| --no-persisted-state | 如果为 true，则表示服务不会在本地磁盘上存储持久状态，或者只在内存中存储状态。 |
| --placement-policy-list | 服务的放置策略以及任何关联域名的 JSON 编码列表。 策略可以是下列其中一项或多项\: `NonPartiallyPlaceService`、`PreferPrimaryDomain`、`RequireDomain`、`RequireDomainDistribution`。 |
| --quorum-loss-wait | 允许分区处于仲裁丢失状态的最大持续时间，以秒为单位。 仅适用于有状态服务。 |
| --replica-restart-wait | 副本关闭之后、创建新副本之前的持续时间，以秒为单位。 仅适用于有状态服务。 |
| --scaling-policies | 此服务的缩放策略的 JSON 编码列表。 |
| --singleton-scheme | 指示服务应包含单个分区，或者为非分区服务。 |
| --stand-by-replica-keep | 删除备用副本之前将其保留的最大持续时间，以秒为单位。 仅适用于有状态服务。 |
| --stateful | 指示服务是有状态服务。 |
| --stateless | 指示服务是无状态服务。 |
| --target-replica-set-size | 数字形式的目标副本集大小。 仅适用于有状态服务。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-service-delete"></a>sfctl service delete
删除现有的 Service Fabric 服务。

只能删除已创建的服务。 默认情况下，Service Fabric 尝试正常关闭服务副本，然后删除服务。 但是，如果服务无法正常关闭，删除操作可能需要很长时间，也可能出现停滞。 使用可选的 ForceRemove 标志可跳过正常关闭序列，并强制删除服务。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --service-id [必需] | 服务的标识。 此 ID 通常是不带“fabric\:”URI 方案的服务全名。 从版本 6.0 开始，分层名称以“\~”字符隔开。 例如，如果服务名称为“fabric\:/myapp/app1/svc1”，则 6.0 及更高版本中的服务标识为“myapp\~app1\~svc1”，在以前的版本中为“myapp/app1/svc1”。 |
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

## <a name="sfctl-service-deployed-type"></a>sfctl service deployed-type
获取有关 Service Fabric 群集中节点上部署的应用程序的指定服务类型的信息。

获取列表，其中包含有关 Service Fabric 群集中节点上部署的应用程序中的特定服务类型的信息。 响应包括服务类型的名称、其注册状态、注册它的服务包，以及代码包的激活 ID。 每个条目表示服务类型的一个激活，以激活 ID 区分。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --application-id [必需] | 应用程序的标识。 这通常是不带“fabric\:”URI 方案的应用程序全名。 从版本 6.0 开始，分层名称以“\~”字符隔开。 例如，如果应用程序名称为“fabric\:/myapp/app1”，则 6.0 及更高版本中的应用程序标识为“myapp\~app1”，在以前的版本中为“myapp/app1”。 |
| --node-name [必需] | 节点的名称。 |
| --service-type-name [必需] | 指定 Service Fabric 服务类型的名称。 |
| --service-manifest-name | 服务清单的名称，用于筛选部署的服务类型信息的列表。 如果已指定，则响应只包含有关此服务清单中定义的服务类型的信息。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-service-deployed-type-list"></a>sfctl service deployed-type-list
获取列表，其中包含有关 Service Fabric 群集中节点上部署的应用程序中的服务类型的信息。

获取列表，其中包含有关 Service Fabric 群集中节点上部署的应用程序中的服务类型的信息。 响应包括服务类型的名称、其注册状态、注册它的服务包，以及代码包的激活 ID。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --application-id [必需] | 应用程序的标识。 这通常是不带“fabric\:”URI 方案的应用程序全名。 从版本 6.0 开始，分层名称以“\~”字符隔开。 例如，如果应用程序名称为“fabric\:/myapp/app1”，则 6.0 及更高版本中的应用程序标识为“myapp\~app1”，在以前的版本中为“myapp/app1”。 |
| --node-name [必需] | 节点的名称。 |
| --service-manifest-name | 服务清单的名称，用于筛选部署的服务类型信息的列表。 如果已指定，则响应只包含有关此服务清单中定义的服务类型的信息。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-service-description"></a>sfctl service description
获取现有 Service Fabric 服务的说明。

获取现有 Service Fabric 服务的说明。 只能获取已创建的服务的说明。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --service-id [必需] | 服务的标识。 此 ID 通常是不带“fabric\:”URI 方案的服务全名。 从版本 6.0 开始，分层名称以“\~”字符隔开。 例如，如果服务名称为“fabric\:/myapp/app1/svc1”，则 6.0 及更高版本中的服务标识为“myapp\~app1\~svc1”，在以前的版本中为“myapp/app1/svc1”。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-service-get-container-logs"></a>sfctl service get-container-logs
获取 Service Fabric 节点上部署的容器的容器日志。

获取给定代码包的 Service Fabric 节点上部署的容器的容器日志。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --application-id [必需] | 应用程序的标识。 这通常是不带“fabric\:”URI 方案的应用程序全名。 从版本 6.0 开始，分层名称以“\~”字符隔开。 例如，如果应用程序名称为“fabric\:/myapp/app1”，则 6.0 及更高版本中的应用程序标识为“myapp\~app1”，在以前的版本中为“myapp/app1”。 |
| --code-package-name [必需] | 在 Service Fabric 群集中注册为应用程序类型一部分的服务清单中指定的代码包的名称。 |
| --node-name [必需] | 节点的名称。 |
| --service-manifest-name [必需] | 在 Service Fabric 群集中注册为应用程序类型一部分的服务清单的名称。 |
| --previous | 指定是否代码包实例的从退出/失效容器获取容器日志。 |
| --tail | 从日志末尾显示的行数。 默认值为 100。 “all”表示显示完整日志。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-service-health"></a>sfctl service health
获取指定 Service Fabric 服务的运行状况。

获取指定服务的运行状况信息。 使用 EventsHealthStateFilter 可以根据运行状态筛选针对服务报告的运行状况事件的集合。 使用 PartitionsHealthStateFilter 可以筛选返回的分区集合。 如果指定运行状况存储中不存在的服务，此请求会返回错误。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --service-id          [必需] | 服务的标识。 此 ID 通常是不带“fabric\:”URI 方案的服务全名。 从版本 6.0 开始，分层名称以“\~”字符隔开。 例如，如果服务名称为“fabric\:/myapp/app1/svc1”，则 6.0 及更高版本中的服务标识为“myapp\~app1\~svc1”，在以前的版本中为“myapp/app1/svc1”。 |
| --events-health-state-filter | 用于根据运行状况筛选返回的 HealthEvent 对象集合。 此参数的可能值包括以下运行状态之一的整数值。 仅返回与筛选器匹配的事件。 所有事件用于评估聚合运行状态。 如果未指定，则返回所有项。 状态值为基于标志的枚举，因此该值可以是使用按位“OR”运算符获取的值的组合。 例如，如果提供的值为 6，则返回 HealthState 值为 OK (2) 和 Warning (4) 的所有事件。  <br> - Default - 默认值。 匹配任何 HealthState。 值为 0。  <br> - None - 不与任何 HealthState 值匹配的筛选器。 未返回有关给定状态集合的结果时使用。 值为 1。  <br> - Ok - 与 HealthState 值为 OK 的输入匹配的筛选器。 值为 2。  <br> - Warning - 与 HealthState 值为 Warning 的输入匹配的筛选器。 值为 4。  <br> - Error - 与 HealthState 值为 Error 的输入匹配的筛选器。 值为 8。  <br> - All - 与具有任意 HealthState 值的输入匹配的筛选器。 值为 65535。 |
| --exclude-health-statistics | 指示运行状况统计数据是否应作为查询结果的一部分返回。 默认值为 False。 统计信息显示处于 Ok、Warning 和 Error 运行状况的子实体数。 |
| --partitions-health-state-filter | 用于根据运行状态筛选服务运行状况查询结果中返回的分区运行状态对象。 此参数的可能值包括以下运行状态之一的整数值。 仅返回与筛选器匹配的分区。 所有分区用于评估聚合运行状态。 如果未指定，则返回所有项。 状态值为基于标志的枚举，因此该值可是使用按位“OR”运算符获取的值的组合。 例如，如果提供的值为 6，则返回 HealthState 值为 OK (2) 和 Warning (4) 的分区的运行状态。  <br> - Default - 默认值。 匹配任何 HealthState。 值为 0。  <br> - None - 不与任何 HealthState 值匹配的筛选器。 未返回有关给定状态集合的结果时使用。 值为 1。  <br> - Ok - 与 HealthState 值为 OK 的输入匹配的筛选器。 值为 2。  <br> - Warning - 与 HealthState 值为 Warning 的输入匹配的筛选器。 值为 4。  <br> - Error - 与 HealthState 值为 Error 的输入匹配的筛选器。 值为 8。  <br> - All - 与具有任意 HealthState 值的输入匹配的筛选器。 值为 65535。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-service-info"></a>sfctl service info
获取有关属于 Service Fabric 应用程序的特定服务的信息。

返回有关属于指定 Service Fabric 应用程序的指定服务的信息。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --application-id [必需] | 应用程序的标识。 这通常是不带“fabric\:”URI 方案的应用程序全名。 从版本 6.0 开始，分层名称以“\~”字符隔开。 例如，如果应用程序名称为“fabric\:/myapp/app1”，则 6.0 及更高版本中的应用程序标识为“myapp\~app1”，在以前的版本中为“myapp/app1”。 |
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

## <a name="sfctl-service-list"></a>sfctl service list
获取属于根据应用程序 ID 指定的应用程序的所有服务的相关信息

返回属于根据应用程序 ID 指定的应用程序的所有服务的相关信息

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --application-id [必需] | 应用程序的标识。 这通常是不带“fabric\:”URI 方案的应用程序全名。 从版本 6.0 开始，分层名称以“\~”字符隔开。 例如，如果应用程序名称为“fabric\:/myapp/app1”，则 6.0 及更高版本中的应用程序标识为“myapp\~app1”，在以前的版本中为“myapp/app1”。 |
| --continuation-token | 继续标记参数用于获取下一组结果。 如果单个响应无法容纳来自系统的结果，则 API 响应中包括含有非空值的继续标记。 当此值传递到下一个 API 调用时，API 返回下一组结果。 如果没有更多结果，则该继续标记不包含值。 不应将此参数的值进行 URL 编码。 |
| --service-type-name | 用于筛选要查询的服务的服务类型名称。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-service-manifest"></a>sfctl service manifest
获取描述服务类型的清单。

获取描述服务类型的清单。 响应包含字符串形式的服务清单 XML。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --application-type-name    [必需] | 应用程序类型的名称。 |
| --application-type-version [必需] | 应用程序类型的版本。 |
| --service-manifest-name    [必需] | 在 Service Fabric 群集中注册为应用程序类型一部分的服务清单的名称。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-service-package-deploy"></a>sfctl service package-deploy
将与指定服务清单关联的包下载到指定节点上的映像缓存。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --app-type-name [必需] | 相应请求服务清单的应用程序清单名称。 |
| --app-type-version [必需] | 相应请求服务清单的应用程序清单版本。 |
| --node-name [必需] | 节点的名称。 |
| --service-manifest-name [必需] | 与要下载的包相关联的服务清单的名称。 |
| --share-policy | 共享策略的 JSON 编码列表。 每个共享策略元素由“name”和“scope”组成。 name 对应于代码、配置或要共享的数据包的名称。 scope 可以是“None”、“All”、“Code”、“Config”或“Data”。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-service-package-health"></a>sfctl service package-health
获取针对 Service Fabric 节点和应用程序部署的特定应用程序的服务包运行状况的相关信息。

针对 Service Fabric 节点部署的特定应用程序，获取其服务包运行状况的相关信息。 使用 EventsHealthStateFilter 可以选择性地根据运行状况筛选部署的服务包上报告的 HealthEvent 对象的集合。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --application-id [必需] | 应用程序的标识。 这通常是不带“fabric\:”URI 方案的应用程序全名。 从版本 6.0 开始，分层名称以“\~”字符隔开。 例如，如果应用程序名称为“fabric\:/myapp/app1”，则 6.0 及更高版本中的应用程序标识为“myapp\~app1”，在以前的版本中为“myapp/app1”。 |
| --node-name [必需] | 节点的名称。 |
| --service-package-name [必需] | 服务包的名称。 |
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

## <a name="sfctl-service-package-info"></a>sfctl service package-info
获取完全与指定的名称匹配的 Service Fabric 节点上部署的服务包列表。

返回部署在给定应用程序的 Service Fabric 节点上的服务包的信息。 这些结果针对其名称与指定为参数的服务包名称完全匹配的服务包。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --application-id [必需] | 应用程序的标识。 这通常是不带“fabric\:”URI 方案的应用程序全名。 从版本 6.0 开始，分层名称以“\~”字符隔开。 例如，如果应用程序名称为“fabric\:/myapp/app1”，则 6.0 及更高版本中的应用程序标识为“myapp\~app1”，在以前的版本中为“myapp/app1”。 |
| --node-name [必需] | 节点的名称。 |
| --service-package-name [必需] | 服务包的名称。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-service-package-list"></a>sfctl service package-list
获取部署在 Service Fabric 节点上的服务包的列表。

返回部署在给定应用程序的 Service Fabric 节点上的服务包的信息。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --application-id [必需] | 应用程序的标识。 这通常是不带“fabric\:”URI 方案的应用程序全名。 从版本 6.0 开始，分层名称以“\~”字符隔开。 例如，如果应用程序名称为“fabric\:/myapp/app1”，则 6.0 及更高版本中的应用程序标识为“myapp\~app1”，在以前的版本中为“myapp/app1”。 |
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

## <a name="sfctl-service-recover"></a>sfctl service recover
向 Service Fabric 群集指出应该尝试恢复当前停滞在仲裁丢失状态的指定服务。

向 Service Fabric 群集指出应该尝试恢复当前停滞在仲裁丢失状态的指定服务。 仅当确定已关闭的副本无法恢复时，才执行此操作。 不当地使用此 API 可能导致潜在的数据丢失。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --service-id [必需] | 服务的标识。 此 ID 通常是不带“fabric\:”URI 方案的服务全名。 从版本 6.0 开始，分层名称以“\~”字符隔开。 例如，如果服务名称为“fabric\:/myapp/app1/svc1”，则 6.0 及更高版本中的服务标识为“myapp\~app1\~svc1”，在以前的版本中为“myapp/app1/svc1”。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-service-report-health"></a>sfctl service report-health
发送有关 Service Fabric 服务的运行状况报告。

报告指定的 Service Fabric 服务的运行状况。 该报告必须包含有关运行状况报告及其所报告属性的源的信息。 该报告将发送到 Service Fabric 网关服务，后者会将其转发到运行状况存储。 该报告可能被网关接受但被运行状况存储在执行额外的验证后拒绝。 例如，运行状况存储可能会由于无效的参数（如过时的序列号）而拒绝该报告。 若要了解该报告是否已应用于运行状况存储中，请检查该报告是否显示在服务的运行状况事件中。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --health-property [必需] | 运行状况信息的属性。 <br><br> 一个实体可以有不同属性的运行状况报告。 该属性是一个字符串，不是固定的枚举，因此可使报告器灵活地对触发报告的状态条件进行分类。 例如，SourceId 为“LocalWatchdog”的报告器可以监视节点上的可用磁盘的状态，因此它可以报告该节点的“AvailableDisk”属性。 同一报告器可以监视节点连接，因此它可以报告同一节点的“Connectivity”属性。 在运行状况存储中，这些报告均被视为指定节点的单独运行状况事件。 与 SourceId 一起，该属性唯一地标识运行状况信息。 |
| --health-state    [必需] | 可能的值包括\:“Invalid”、“Ok”、“Warning”、“Error”、“Unknown”。 |
| --service-id [必需] | 服务的标识。 <br><br> 这通常是不带“fabric\:”URI 方案的服务全名。 从版本 6.0 开始，分层名称以“\~”字符隔开。 例如，如果服务名称为“fabric\:/myapp/app1/svc1”，则 6.0 及更高版本中的服务标识为“myapp\~app1\~svc1”，在以前的版本中为“myapp/app1/svc1”。 |
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

## <a name="sfctl-service-resolve"></a>sfctl service resolve
解析 Service Fabric 分区。

解析 Service Fabric 服务分区，以获取服务副本的终结点。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --service-id [必需] | 服务的标识。 此 ID 通常是不带“fabric\:”URI 方案的服务全名。 从版本 6.0 开始，分层名称以“\~”字符隔开。 例如，如果服务名称为“fabric\:/myapp/app1/svc1”，则 6.0 及更高版本中的服务标识为“myapp\~app1\~svc1”，在以前的版本中为“myapp/app1/svc1”。 |
| --partition-key-type | 分区的键类型。 如果服务的分区方案为 Int64Range 或 Named，则此参数是必需的。 可能的值如下。 - None (1) - 指示未指定 PartitionKeyValue 参数。 此值对于分区方案为 Singleton 的分区有效。 这是默认值。 值为 1。 - Int64Range (2) - 指示 PartitionKeyValue 参数为 int64 分区键。 此值对于分区方案为 Int64Range 的分区有效。 值为 2。 - Named (3) - 指示 PartitionKeyValue 参数为分区的名称。 此值对于分区方案为 Named 的分区有效。 值为 3。 |
| --partition-key-value | 分区键。 如果服务的分区方案为 Int64Range 或 Named，则此参数是必需的。 |
| --previous-rsp-version | 先前收到的响应的 Version 字段中的值。 如果用户知道先前获取的结果已过时，则此参数是必需的。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-service-type-list"></a>sfctl service type-list
获取列表，其中包含有关 Service Fabric 群集中预配的应用程序类型支持的服务类型的信息。

获取列表，其中包含有关 Service Fabric 群集中预配的应用程序类型支持的服务类型的信息。 提供的应用程序类型必须存在。 否则，将返回 404 状态。

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

## <a name="sfctl-service-update"></a>sfctl service update
使用给定的更新说明更新指定的服务。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --service-id   [必需] | 服务的标识。 这通常是不带“fabric\:”URI 方案的服务全名。 从版本 6.0 开始，分层名称以“\~”字符隔开。 例如，如果服务名称为“fabric\:/myapp/app1/svc1”，则 6.0 及更高版本中的服务标识为“myapp\~app1\~svc1”，在以前的版本中为“myapp/app1/svc1”。 |
| --constraints | 字符串形式的放置约束。 放置约束是节点属性中的布尔表达式，用于根据服务要求将服务限制到特定的节点。 例如，若要在 NodeType 为 blue 的节点上放置服务，请指定以下语句\: "NodeColor == blue"。 |
| --correlated-service | 要关联的目标服务的名称。 |
| --correlation | 使用对齐关联将服务与现有服务相关联。 |
| --instance-count | 实例计数。 仅适用于无状态服务。 |
| --load-metrics | 在各节点中进行负载均衡时使用的指标的 JSON 编码列表。 |
| --min-replica-set-size | 数字形式的最小副本集大小。 仅适用于有状态服务。 |
| --move-cost | 指定服务的移动成本。 可能的值为\:“Zero”、“Low”、“Medium”和“High”。 |
| --placement-policy-list | 服务的放置策略以及任何关联域名的 JSON 编码列表。 策略可以是下列其中一项或多项\: `NonPartiallyPlaceService`、`PreferPrimaryDomain`、`RequireDomain`、`RequireDomainDistribution`。 |
| --quorum-loss-wait | 允许分区处于仲裁丢失状态的最大持续时间，以秒为单位。 仅适用于有状态服务。 |
| --replica-restart-wait | 副本关闭之后、创建新副本之前的持续时间，以秒为单位。 仅适用于有状态服务。 |
| --scaling-policies | 此服务的缩放策略的 JSON 编码列表。 |
| --stand-by-replica-keep | 删除备用副本之前将其保留的最大持续时间，以秒为单位。 仅适用于有状态服务。 |
| --stateful | 指示目标服务是有状态服务。 |
| --stateless | 指示目标服务是无状态服务。 |
| --target-replica-set-size | 数字形式的目标副本集大小。 仅适用于有状态服务。 |
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
