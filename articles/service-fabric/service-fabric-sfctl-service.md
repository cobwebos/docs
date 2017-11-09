---
title: Azure Service Fabric CLI - sfctl service | Microsoft Docs
description: "介绍 Service Fabric CLI sfctl service 命令。"
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: cli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 09/22/2017
ms.author: ryanwi
ms.openlocfilehash: 66649bb6ae317eb227dcdf45aa084905967c117f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="sfctl-service"></a>sfctl service
创建、删除和管理服务、服务类型与服务包。

## <a name="commands"></a>命令

|命令|说明|
| --- | --- |
|    app-name       | 获取服务的 Service Fabric 应用程序名称。|
|    code-package-list | 获取部署在 Service Fabric 节点上的代码包的列表。|
|    create         | 创建说明中指定的 Service Fabric 服务。|
|    delete         | 删除现有的 Service Fabric 服务。|
|    deployed-type  | 获取有关 Service Fabric 群集中节点上部署的应用程序的指定服务类型的信息。|
|    deployed-type-list| 获取列表，其中包含有关 Service Fabric 群集中节点上部署的应用程序中的服务类型的信息。|
|    description    | 获取现有 Service Fabric 服务的说明。|
|    health         | 获取指定 Service Fabric 服务的运行状况。|
|    info           | 获取有关属于 Service Fabric 应用程序的特定服务的信息。|
|    list           | 获取属于根据应用程序 ID 指定的应用程序的所有服务的相关信息|
|    manifest       | 获取描述服务类型的清单。|
|    package-deploy | 将与指定服务清单关联的包下载到指定节点上的映像缓存。|
|    package-health | 获取针对 Service Fabric 节点和应用程序部署的特定应用程序的服务包运行状况的相关信息。|
|    package-info   | 获取完全与指定的名称匹配的 Service Fabric 节点上部署的服务包列表。|
|    package-list   | 获取部署在 Service Fabric 节点上的服务包的列表。|
|    recover        | 向 Service Fabric 群集指出应该尝试恢复当前停滞在仲裁丢失状态的指定服务。|
|    report-health  | 发送有关 Service Fabric 服务的运行状况报告。|
|    resolve        | 解析 Service Fabric 分区。|
|    type-list      | 获取列表，其中包含有关 Service Fabric 群集中预配的应用程序类型支持的服务类型的信息。|
|    update         | 使用给定的更新说明更新指定的服务。|


## <a name="sfctl-service-create"></a>sfctl service create
创建说明中指定的 Service Fabric 服务。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --app-id       [必需]| 父应用程序的标识。 这通常是不带“fabric:”URI 方案的应用程序完整 ID。 从版本 6.0 开始，分层名称以“~”字符隔开。 例如，如果应用程序名称为“fabric://myapp/app1”，则 6.0 及更高版本中的应用程序标识为“myapp~app1”，在以前的版本中为“myapp/app1”。|
| --name         [必需]| 服务的名称。 应是应用程序 ID 的子级。           这是包括 `fabric:` URI 的完整名称。 例如，服务 `fabric:/A/B` 是应用程序 `fabric:/A` 的子级。|
| --service-type [必需]| 服务类型的名称。|
| --activation-mode     | 服务包的激活模式。|
| --constraints         | 字符串形式的放置约束。 放置约束是节点属性中的布尔表达式，用于根据服务要求将服务限制到特定的节点。 例如，若要在 NodeType 为 blue 的节点上放置服务，请指定以下语句："NodeColor == blue"。|
| --correlated-service  | 要关联的目标服务的名称。|
| --correlation         | 使用对齐关联将服务与现有服务相关联。|
| --dns-name            | 要创建的服务的 DNS 名称。 必须为此设置启用 Service Fabric DNS 系统服务。|
| --instance-count      | 实例计数。 仅适用于无状态服务。|
| --int-scheme          | 指示应在一系列无符号整数之间将服务统一分区。|
| --int-scheme-count    | 整数键范围内要创建的分区数（适用于统一整数分区方案）。|
| --int-scheme-high     | 键整数范围的下限（如果使用统一整数分区方案）。|
| --int-scheme-low      | 键整数范围的上限（如果使用统一整数分区方案）。|
| --load-metrics        | 对各节点上的服务进行负载均衡时使用的指标的 JSON 编码列表。|
| --min-replica-set-size| 数字形式的最小副本集大小。 仅适用于有状态服务。|
| --move-cost           | 指定服务的移动成本。 可能的值为：“Zero”、“Low”、“Medium”和“High”。|
| --named-scheme        | 指示服务应包含多个命名分区。|
| --named-scheme-list   | 要在其中将服务分区的名称的 JSON 编码列表（如果使用命名分区方案）。|
| --no-persisted-state  | 如果为 true，则表示服务不会在本地磁盘上存储持久状态，或者只在内存中存储状态。|
| --placement-policy-list  | 服务的放置策略以及任何关联域名的 JSON 编码列表。 策略可以是下列其中一项或多项：`NonPartiallyPlaceService`、`PreferPrimaryDomain`、`RequireDomain`、`RequireDomainDistribution`。|
| --quorum-loss-wait    | 允许分区处于仲裁丢失状态的最大持续时间，以秒为单位。 仅适用于有状态服务。|
| --replica-restart-wait| 副本关闭之后、创建新副本之前的持续时间，以秒为单位。 仅适用于有状态服务。|
| --singleton-scheme    | 指示服务应包含单个分区，或者为非分区服务。|
| --stand-by-replica-keep  | 删除备用副本之前将其保留的最大持续时间，以秒为单位。 仅适用于有状态服务。|
| --stateful            | 指示服务是有状态服务。|
| --stateless           | 指示服务是无状态服务。|
| --target-replica-set-size| 数字形式的目标副本集大小。 仅适用于有状态服务。|
| --timeout -t          | 服务器超时，以秒为单位。  默认值：60。|

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug               | 提高日志记录详细程度，以显示所有调试日志。|
| --help -h             | 显示此帮助消息并退出。|
| --output -o           | 输出格式。  允许的值：json、jsonc、table、tsv。  默认值：json。|
| --query               | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http://jmespath.org/。|
| --verbose             | 提高日志记录详细程度。 使用 --debug 可获取完整调试日志。|

## <a name="sfctl-service-delete"></a>sfctl service delete
删除现有的 Service Fabric 服务。

删除现有的 Service Fabric 服务。 只能删除已创建的服务。 默认情况下，Service Fabric 尝试以正常方式关闭服务副本，然后删除服务。 但是，如果服务无法以正常方式关闭，删除操作可能需要花费很长时间，也可能出现停滞。 使用可选的 ForceRemove 标志可跳过正常关闭序列，并强制删除服务。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --service-id [必需]| 服务的标识。 这通常是不带“fabric:”URI 方案的服务全名。 从版本 6.0 开始，分层名称以“~”字符隔开。 例如，如果服务名称为“fabric://myapp/app1/svc1”，则 6.0 及更高版本中的服务标识为“myapp~app1~svc1”，在以前的版本中为“myapp/app1/svc1”。|
| --force-remove      | 强制删除 Service Fabric 应用程序或服务，跳过正常关闭序列。 若因服务代码中问题而无法正常关闭副本，导致应用程序或服务删除超时，可使用此参数强制删除该应用程序或服务。|
| --timeout -t        | 服务器超时，以秒为单位。  默认值：60。|

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug             | 提高日志记录详细程度，以显示所有调试日志。|
| --help -h           | 显示此帮助消息并退出。|
| --output -o         | 输出格式。  允许的值：json、jsonc、table、tsv。  默认值：json。|
| --query             | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http://jmespath.org/。|
| --verbose           | 提高日志记录详细程度。 使用 --debug 可获取完整调试日志。|

## <a name="sfctl-service-description"></a>sfctl service description
获取现有 Service Fabric 服务的说明。

获取现有 Service Fabric 服务的说明。 只能获取已创建的服务的说明。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --service-id [必需]| 服务的标识。 这通常是不带“fabric:”URI 方案的服务全名。 从版本 6.0 开始，分层名称以“~”字符隔开。 例如，如果服务名称为“fabric://myapp/app1/svc1”，则 6.0 及更高版本中的服务标识为“myapp~app1~svc1”，在以前的版本中为“myapp/app1/svc1”。|
| --timeout -t        | 服务器超时，以秒为单位。  默认值：60。|

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug             | 提高日志记录详细程度，以显示所有调试日志。|
| --help -h           | 显示此帮助消息并退出。|
| --output -o         | 输出格式。  允许的值：json、jsonc、table、tsv。  默认值：json。|
| --query             | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http://jmespath.org/。|
| --verbose           | 提高日志记录详细程度。 使用 --debug 可获取完整调试日志。|

## <a name="sfctl-service-health"></a>sfctl service health
获取指定 Service Fabric 服务的运行状况。

获取指定服务的运行状况信息。 使用 EventsHealthStateFilter 可以根据运行状态筛选针对服务报告的运行状况事件的集合。 使用 PartitionsHealthStateFilter 可以筛选返回的分区集合。 如果指定运行状况存储中不存在的服务，此 cmdlet 会返回错误。 。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --service-id          [必需]| 服务的标识。 这通常是不带“fabric:”URI 方案的服务全名。 从版本 6.0 开始，分层名称以“~”字符隔开。 例如，如果服务名称为“fabric://myapp/app1/svc1”，则 6.0 及更高版本中的服务标识为“myapp~app1~svc1”，在以前的版本中为“myapp/app1/svc1”。|
| --events-health-state-filter | 用于根据运行状态筛选返回的 HealthEvent 对象集合。 此参数的可能值包括以下运行状态之一的整数值。 仅返回与筛选器匹配的事件。 所有事件用于评估聚合运行状态。 如果未指定，则返回所有项。 状态值为基于标志的枚举，因此该值可是使用按位“OR”运算符获取的值的组合。 例如，如果提供的值为 6，则返回 HealthState 值为 OK (2) 和 Warning (4) 的所有事件。 - Default - 默认值。 匹配任何 HealthState。 值为零。 - None - 不与任何 HealthState 值匹配的筛选器。 未返回有关给定状态集合的结果时使用。 值为 1。 - Ok - 与 HealthState 值为 OK 的输入匹配的筛选器。 值为 2。 - Warning - 与 HealthState 值为 Warning 的输入匹配的筛选器。 值为 4。 - Error - 与 HealthState 值为 Error 的输入匹配的筛选器。 值为 8。 - All - 与具有任意 HealthState 值的输入匹配的筛选器。 值为 65535。|
|--exclude-health-statistics     | 指示是否作为查询结果的一部分返回运行状况统计数据。 默认值为 False。 统计信息显示处于 Ok、Warning 和 Error 运行状态的子实体数。|
| --partitions-health-state-filter| 用于根据运行状态筛选服务运行状况查询结果中返回的分区运行状态对象。 此参数的可能值包括以下运行状态之一的整数值。 仅返回与筛选器匹配的分区。 所有分区用于评估聚合运行状态。 如果未指定，则返回所有项。 状态值为基于标志的枚举，因此该值可是使用按位“OR”运算符获取的值的组合。 例如，如果提供的值为“6”，则返回 HealthState 值为 OK (2) 和 Warning (4) 的分区的运行状态。 - Default - 默认值。 匹配任何 HealthState。                  值为零。 - None - 不与任何 HealthState 值匹配的筛选器。 未返回有关给定状态集合的结果时使用。 值为 1。 - Ok - 与 HealthState 值为 OK 的输入匹配的筛选器。 值为 2。 - Warning - 与 HealthState 值为 Warning 的输入匹配的筛选器。 值为 4。 - Error - 与 HealthState 值为 Error 的输入匹配的筛选器。 值为 8。 - All - 与具有任意 HealthState 值的输入匹配的筛选器。 值为 65535。|
| --timeout -t                 | 服务器超时，以秒为单位。  默认值：60。|

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug                      | 提高日志记录详细程度，以显示所有调试日志。|
| --help -h                    | 显示此帮助消息并退出。|
| --output -o                  | 输出格式。  允许的值：json、jsonc、table、tsv。                  默认值：json。|
| --query                      | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http://jmespath.org/。|
| --verbose                    | 提高日志记录详细程度。 使用 --debug 可获取完整调试日志。|

## <a name="sfctl-service-info"></a>sfctl service info
获取有关属于 Service Fabric 应用程序的特定服务的信息。

返回有关属于指定 Service Fabric 应用程序的指定服务的信息。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --application-id [必需]| 应用程序的标识。 这通常是不带“fabric:”URI 方案的应用程序全名。 从版本 6.0 开始，分层名称以“~”字符隔开。 例如，如果应用程序名称为“fabric://myapp/app1”，则 6.0 及更高版本中的应用程序标识为“myapp~app1”，在以前的版本中为“myapp/app1”。|
| --service-id     [必需]| 服务的标识。 这通常是不带“fabric:”URI 方案的服务全名。 从版本 6.0 开始，分层名称以“~”字符隔开。 例如，如果服务名称为“fabric://myapp/app1/svc1”，则 6.0 及更高版本中的服务标识为“myapp~app1~svc1”，在以前的版本中为“myapp/app1/svc1”。|
| --timeout -t            | 服务器超时，以秒为单位。  默认值：60。|

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug                 | 提高日志记录详细程度，以显示所有调试日志。|
| --help -h               | 显示此帮助消息并退出。|
| --output -o             | 输出格式。  允许的值：json、jsonc、table、tsv。  默认值：json。|
| --query                 | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http://jmespath.org/。|
| --verbose               | 提高日志记录详细程度。 使用 --debug 可获取完整调试日志。|

## <a name="sfctl-service-list"></a>sfctl service list
获取属于根据应用程序 ID 指定的应用程序的所有服务的相关信息

返回属于根据应用程序 ID 指定的应用程序的所有服务的相关信息

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --application-id [必需]| 应用程序的标识。 这通常是不带“fabric:”URI 方案的应用程序全名。 从版本 6.0 开始，分层名称以“~”字符隔开。 例如，如果应用程序名称为“fabric://myapp/app1”，则 6.0 及更高版本中的应用程序标识为“myapp~app1”，在以前的版本中为“myapp/app1”。|
| --continuation-token    | 继续标记参数用于获取下一组结果。 如果单个响应无法容纳来自系统的结果，则 API 响应中包括含有非空值的继续标记。 当此值传递到下一个 API 调用时，API 返回下一组结果。 如果没有更多结果，则继续标记不包含值。 不应将此参数的值进行 URL 编码。|
| --service-type-name     | 用于筛选要查询的服务的服务类型名称。|
| --timeout -t            | 服务器超时，以秒为单位。  默认值：60。|

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug                 | 提高日志记录详细程度，以显示所有调试日志。|
| --help -h               | 显示此帮助消息并退出。|
| --output -o             | 输出格式。  允许的值：json、jsonc、table、tsv。  默认值：json。|
| --query                 | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http://jmespath.org/。|
| --verbose               | 提高日志记录详细程度。 使用 --debug 可获取完整调试日志。|

## <a name="sfctl-service-manifest"></a>sfctl service manifest
获取描述服务类型的清单。

获取描述服务类型的清单。 响应包含字符串形式的服务清单 XML。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --application-type-name    [必需]| 应用程序类型的名称。|
| --application-type-version [必需]| 应用程序类型的版本。|
| --service-manifest-name    [必需]| 在 Service Fabric 群集中注册为应用程序类型一部分的服务清单的名称。|
| --timeout -t                      | 服务器超时，以秒为单位。  默认值：60。|

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug                           | 提高日志记录详细程度，以显示所有调试日志。|
| --help -h                         | 显示此帮助消息并退出。|
| --output -o                       | 输出格式。  允许的值：json、jsonc、table、tsv。                       默认值：json。|
| --query                           | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http://jmespath.org/。|
| --verbose                         | 提高日志记录详细程度。 使用 --debug 可获取完整调试日志。|

## <a name="sfctl-service-recover"></a>sfctl service recover
向 Service Fabric 群集指出应该尝试恢复当前停滞在仲裁丢失状态的指定服务。

向 Service Fabric 群集指出应该尝试恢复当前停滞在仲裁丢失状态的指定服务。 仅当已关闭的副本无法恢复时，才执行此操作。 不当地使用此 API 可能导致潜在的数据丢失。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --service-id [必需]| 服务的标识。 这通常是不带“fabric:”URI 方案的服务全名。 从版本 6.0 开始，分层名称以“~”字符隔开。 例如，如果服务名称为“fabric://myapp/app1/svc1”，则 6.0 及更高版本中的服务标识为“myapp~app1~svc1”，在以前的版本中为“myapp/app1/svc1”。|
| --timeout -t        | 服务器超时，以秒为单位。  默认值：60。|

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug             | 提高日志记录详细程度，以显示所有调试日志。|
| --help -h           | 显示此帮助消息并退出。|
| --output -o         | 输出格式。  允许的值：json、jsonc、table、tsv。  默认值：json。|
| --query             | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http://jmespath.org/。|
| --verbose           | 提高日志记录详细程度。 使用 --debug 可获取完整调试日志。|

## <a name="sfctl-service-resolve"></a>sfctl service resolve
解析 Service Fabric 分区。

解析 Service Fabric 服务分区，以获取服务副本的终结点。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --service-id [必需]| 服务的标识。 这通常是不带“fabric:”URI 方案的服务全名。 从版本 6.0 开始，分层名称以“~”字符隔开。 例如，如果服务名称为“fabric://myapp/app1/svc1”，则 6.0 及更高版本中的服务标识为“myapp~app1~svc1”，在以前的版本中为“myapp/app1/svc1”。|
| --partition-key-type| 分区的键类型。 如果服务的分区方案为 Int64Range 或 Named，则此参数是必需的。 可能的值如下。 - None (1) - 指示未指定 PartitionKeyValue 参数。 此值对于分区方案为 Singleton 的分区有效。 这是默认值。 值为 1。 - Int64Range (2) - 指示 PartitionKeyValue 参数为 int64 分区键。 此值对于分区方案为 Int64Range 的分区有效。 值为 2。 - Named (3) - 指示 PartitionKeyValue 参数为分区的名称。 此值对于分区方案为 Named 的分区有效。 值为 3。|
| --partition-key-value  | 分区键。 如果服务的分区方案为 Int64Range 或 Named，则此参数是必需的。|
| --previous-rsp-version | 先前收到的响应的 Version 字段中的值。 如果用户知道先前获取的结果已过时，则此参数是必需的。|
| --timeout -t        | 服务器超时，以秒为单位。  默认值：60。|

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug             | 提高日志记录详细程度，以显示所有调试日志。|
| --help -h           | 显示此帮助消息并退出。|
| --output -o         | 输出格式。  允许的值：json、jsonc、table、tsv。  默认值：json。|
| --query             | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http://jmespath.org/。|
| --verbose           | 提高日志记录详细程度。 使用 --debug 可获取完整调试日志。|

## <a name="sfctl-service-update"></a>sfctl service update
使用给定的更新说明更新指定的服务。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --service-id   [必需]| 要更新的目标服务。 这通常是不带“fabric:”URI 方案的服务完整 ID。 从版本 6.0 开始，分层名称以“~”字符隔开。 例如，如果服务名称为“fabric://myapp/app1/svc1”，则 6.0 及更高版本中的服务标识为“myapp~app1~svc1”，在以前的版本中为“myapp/app1/svc1”。|
| --constraints         | 字符串形式的放置约束。 放置约束是节点属性中的布尔表达式，用于根据服务要求将服务限制到特定的节点。 例如，若要在 NodeType 为 blue 的节点上放置服务，请指定以下语句："NodeColor == blue"。|
| --correlated-service  | 要关联的目标服务的名称。|
| --correlation         | 使用对齐关联将服务与现有服务相关联。|
| --instance-count      | 实例计数。 仅适用于无状态服务。|
| --load-metrics        | 在各节点中进行负载均衡时使用的指标的 JSON 编码列表。|
| --min-replica-set-size| 数字形式的最小副本集大小。 仅适用于有状态服务。|
| --move-cost           | 指定服务的移动成本。 可能的值为：“Zero”、“Low”、“Medium”和“High”。|
| --placement-policy-list  | 服务的放置策略以及任何关联域名的 JSON 编码列表。 策略可以是下列其中一项或多项：`NonPartiallyPlaceService`、`PreferPrimaryDomain`、`RequireDomain`、`RequireDomainDistribution`。|
| --quorum-loss-wait    | 允许分区处于仲裁丢失状态的最大持续时间，以秒为单位。 仅适用于有状态服务。|
| --replica-restart-wait| 副本关闭之后、创建新副本之前的持续时间，以秒为单位。 仅适用于有状态服务。|
| --stand-by-replica-keep  | 删除备用副本之前将其保留的最大持续时间，以秒为单位。 仅适用于有状态服务。|
| --stateful            | 指示目标服务是有状态服务。|
| --stateless           | 指示目标服务是无状态服务。|
| --target-replica-set-size| 数字形式的目标副本集大小。 仅适用于有状态服务。|
| --timeout -t          | 服务器超时，以秒为单位。  默认值：60。|

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug               | 提高日志记录详细程度，以显示所有调试日志。|
| --help -h             | 显示此帮助消息并退出。|
| --output -o           | 输出格式。  允许的值：json、jsonc、table、tsv。  默认值：json。|
| --query               | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http://jmespath.org/。|
| --verbose             | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。|

## <a name="next-steps"></a>后续步骤
- [安装](service-fabric-cli.md) Service Fabric CLI。
- 了解如何通过[示例脚本](/azure/service-fabric/scripts/sfctl-upgrade-application)使用 Service Fabric CLI。