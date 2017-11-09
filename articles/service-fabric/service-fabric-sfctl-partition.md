---
title: Azure Service Fabric CLI - sfctl partition | Microsoft Docs
description: "介绍 Service Fabric CLI sfctl partition 命令。"
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
ms.openlocfilehash: 99756378f2106707b4f6d634a1183d5c32243ee2
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2017
---
# <a name="sfctl-partition"></a>sfctl partition
查询和管理任何服务的分区。

## <a name="commands"></a>命令

|命令|说明|
| --- | --- |
|    data-loss      | 此 API 造成指定的分区发生数据丢失。|
|    data-loss-status  | 获取使用 StartDataLoss API 启动的分区数据丢失操作的进度。|
|    health         | 获取指定 Service Fabric 分区的运行状况。|
|    info           | 获取有关 Service Fabric 分区的信息。|
|    list           | 获取 Service Fabric 服务的分区列表。|
|    load           | 获取指定的 Service Fabric 分区的负载。|
|    load-reset     | 重置 Service Fabric 分区的当前负载。|
|    quorum-loss    | 造成给定的有状态服务分区发生仲裁丢失。|
|    quorum-loss-status| 获取使用 StartQuorumLoss API 在分区上启动的仲裁丢失操作的进度。|
|    recover        | 向 Service Fabric 群集指出应该尝试恢复当前停滞在仲裁丢失状态的特定分区。|
|    recover-all    | 向 Service Fabric 群集指出应该尝试恢复当前停滞在仲裁丢失状态的所有服务（包括系统服务）。|
|    report-health  | 发送有关 Service Fabric 分区的运行状况报告。|
|    restart        | 此 API 重启指定分区的部分或所有副本或者实例。|
|    restart-status | 获取使用 StartPartitionRestart 启动的 PartitionRestart 操作的进度。|
|    svc-name       | 获取分区的 Service Fabric 服务名称。|


## <a name="sfctl-partition-health"></a>sfctl partition health
获取指定 Service Fabric 分区的运行状况。

获取指定分区的运行状况信息。 使用 EventsHealthStateFilter 可以根据运行状态筛选针对服务报告的运行状况事件的集合。
使用 ReplicasHealthStateFilter 可以筛选分区上 ReplicaHealthState 对象的集合。 如果指定运行状况存储中不存在的分区，此 cmdlet 会返回错误。 。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --partition-id      [必需]| 分区的标识。|
| --events-health-state-filter  | 用于根据运行状态筛选返回的 HealthEvent 对象集合。 此参数的可能值包括以下运行状态之一的整数值。                仅返回与筛选器匹配的事件。 所有事件用于评估聚合运行状态。 如果未指定，则返回所有项。 状态值为基于标志的枚举，因此该值可是使用按位“OR”运算符获取的值的组合。 例如，如果提供的值为 6，则返回 HealthState 值为 OK (2) 和 Warning (4) 的所有事件。 - Default - 默认值。 匹配任何 HealthState。 值为零。 - None - 不与任何 HealthState 值匹配的筛选器。 未返回有关给定状态集合的结果时使用。 值为 1。 - Ok - 与 HealthState 值为 OK 的输入匹配的筛选器。 值为 2。 - Warning - 与 HealthState 值为 Warning 的输入匹配的筛选器。 值为 4。 - Error - 与 HealthState 值为 Error 的输入匹配的筛选器。 值为 8。                - All - 与具有任意 HealthState 值的输入匹配的筛选器。                值为 65535。|
|--exclude-health-statistics   | 指示是否作为查询结果的一部分返回运行状况统计数据。 默认值为 False。 统计信息显示处于 Ok、Warning 和 Error 运行状态的子实体数。|
| --replicas-health-state-filter| 用于筛选分区上 ReplicaHealthState 对象的集合。 可从位运算的成员或 HealthStateFilter 的成员获取该值。 仅返回与筛选器匹配的副本。 所有副本用于评估聚合运行状态。 如果未指定，则返回所有项。 状态值为基于标志的枚举，因此该值可是使用按位“OR”运算符获取的值的组合。 例如，如果提供的值为 6，则返回 HealthState 值为 OK (2) 和 Warning (4) 的所有事件。 此参数的可能值包括以下运行状态之一的整数值。 - Default - 默认值。 匹配任何 HealthState。 值为零。 - None - 不与任何 HealthState 值匹配的筛选器。 未返回有关给定状态集合的结果时使用。 值为 1。 - Ok - 与 HealthState 值为 OK 的输入匹配的筛选器。 值为 2。 - Warning - 与 HealthState 值为 Warning 的输入匹配的筛选器。 值为 4。 - Error - 与 HealthState 值为 Error 的输入匹配的筛选器。 值为 8。 - All - 与具有任意 HealthState 值的输入匹配的筛选器。 值为 65535。|
| --timeout -t               | 服务器超时，以秒为单位。  默认值：60。|

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug                    | 提高日志记录详细程度，以显示所有调试日志。|
| --help -h                  | 显示此帮助消息并退出。|
| --output -o                | 输出格式。  允许的值：json、jsonc、table、tsv。                默认值：json。|
| --query                    | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http://jmespath.org/。 |
| --verbose                  | 提高日志记录详细程度。 使用 --debug 可获取完整调试日志。|

## <a name="sfctl-partition-info"></a>sfctl partition info
获取有关 Service Fabric 分区的信息。

Partitions 终结点返回有关指定的分区的信息。 响应包括分区 ID、分区方案信息、分区支持的密钥、状态、运行状况和有关分区的其他详细信息。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --partition-id [必需]| 分区的标识。|
| --timeout -t          | 服务器超时，以秒为单位。  默认值：60。|

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug               | 提高日志记录详细程度，以显示所有调试日志。|
| --help -h             | 显示此帮助消息并退出。|
| --output -o           | 输出格式。  允许的值：json、jsonc、table、tsv。  默认值：json。|
| --query               | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http://jmespath.org/。|
| --verbose             | 提高日志记录详细程度。 使用 --debug 可获取完整调试日志。|

## <a name="sfctl-partition-list"></a>sfctl partition list
获取 Service Fabric 服务的分区列表。

获取 Service Fabric 服务的分区列表。 分区 ID、分区方案信息、分区支持的密钥、状态、运行状况和有关分区的其他详细信息。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --service-id [必需]| 服务的标识。 这通常是不带“fabric:”URI 方案的服务全名。 从版本 6.0 开始，分层名称以“~”字符隔开。 例如，如果服务名称为“fabric://myapp/app1/svc1”，则 6.0 及更高版本中的服务标识为“myapp~app1~svc1”，在以前的版本中为“myapp/app1/svc1”。|
| --continuation-token| 继续标记参数用于获取下一组结果。         如果单个响应无法容纳来自系统的结果，则 API 响应中包括含有非空值的继续标记。 当此值传递到下一个 API 调用时，API 返回下一组结果。 如果没有更多结果，则继续标记不包含值。 不应将此参数的值进行 URL 编码。|
| --timeout -t        | 服务器超时，以秒为单位。  默认值：60。|

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug             | 提高日志记录详细程度，以显示所有调试日志。|
| --help -h           | 显示此帮助消息并退出。|
| --output -o         | 输出格式。  允许的值：json、jsonc、table、tsv。  默认值：json。|
| --query             | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http://jmespath.org/。|
| --verbose           | 提高日志记录详细程度。 使用 --debug 可获取完整调试日志。|

## <a name="sfctl-partition-load"></a>sfctl partition load
获取指定的 Service Fabric 分区的负载。

返回有关指定的分区的信息。 响应包括负载信息列表。 每项信息包括负载指标名称、值以及上次报告时间 (UTC)。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --partition-id [必需]| 分区的标识。|
| --timeout -t          | 服务器超时，以秒为单位。  默认值：60。|

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug               | 提高日志记录详细程度，以显示所有调试日志。|
| --help -h             | 显示此帮助消息并退出。|
| --output -o           | 输出格式。  允许的值：json、jsonc、table、tsv。  默认值：json。|
| --query               | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http://jmespath.org/。|
| --verbose             | 提高日志记录详细程度。 使用 --debug 可获取完整调试日志。|

## <a name="sfctl-partition-recover"></a>sfctl partition recover
向 Service Fabric 群集指出应该尝试恢复当前停滞在仲裁丢失状态的特定分区。

向 Service Fabric 群集指出应该尝试恢复当前停滞在仲裁丢失状态的特定分区。 仅当确定已关闭的副本无法恢复时，才执行此操作。 不当地使用此 API 可能导致潜在的数据丢失。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --partition-id [必需]| 分区的标识。|
| --timeout -t          | 服务器超时，以秒为单位。  默认值：60。|

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug               | 提高日志记录详细程度，以显示所有调试日志。|
| --help -h             | 显示此帮助消息并退出。|
| --output -o           | 输出格式。  允许的值：json、jsonc、table、tsv。  默认值：json。|
| --query               | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http://jmespath.org/。|
| --verbose             | 提高日志记录详细程度。 使用 --debug 可获取完整调试日志。|

## <a name="sfctl-partition-restart"></a>sfctl partition restart
此 API 重启指定分区的部分或所有副本或者实例。

此 API 可用于测试故障转移。 如果用于针对无状态服务分区，RestartPartitionMode 必须是 AllReplicasOrInstances。 使用相同的 OperationId 调用 GetPartitionRestartProgress API 可获取进度。 。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --operation-id           [必需]| 用于标识此 API 的调用的 GUID。  需将此参数传入相应的 GetProgress API。|
| --partition-id           [必需]| 分区的标识。|
| --restart-partition-mode [必需]| - Invalid - 保留值。  不传入 API。 - AllReplicasOrInstances - 重启分区中的所有副本或实例一次。 -OnlyActiveSecondaries - 仅重启辅助副本。 。|
| --service-id             [必需]| 服务的标识。 这通常是不带“fabric:”URI 方案的服务全名。 从版本 6.0 开始，分层名称以“~”字符隔开。 例如，如果服务名称为“fabric://myapp/app1/svc1”，则 6.0 及更高版本中的服务标识为“myapp~app1~svc1”，在以前的版本中为“myapp/app1/svc1”。|
| --timeout -t                    | 服务器超时，以秒为单位。  默认值：60。|

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug                         | 提高日志记录详细程度，以显示所有调试日志。|
| --help -h                       | 显示此帮助消息并退出。|
| --output -o                     | 输出格式。  允许的值：json、jsonc、table、tsv。                     默认值：json。|
| --query                         | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http://jmespath.org/。|
| --verbose                       | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。|

## <a name="next-steps"></a>后续步骤
- [安装](service-fabric-cli.md) Service Fabric CLI。
- 了解如何通过[示例脚本](/azure/service-fabric/scripts/sfctl-upgrade-application)使用 Service Fabric CLI。
