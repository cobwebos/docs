---
title: Azure Service Fabric CLI - sfctl replica | Microsoft Docs
description: "介绍 Service Fabric CLI sfctl replica 命令。"
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/22/2017
ms.author: ryanwi
ms.openlocfilehash: 422c19dfa9a204d98a898f76bc1af92a05c054d0
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/18/2018
---
# <a name="sfctl-replica"></a>sfctl replica
管理属于服务分区的副本。

## <a name="commands"></a>命令

|命令|说明|
| --- | --- |
|    deployed  | 获取部署在 Service Fabric 节点上的副本的详细信息。|
|    deployed-list| 获取部署在 Service Fabric 节点上的副本的列表。|
|    health    | 获取 Service Fabric 有状态服务副本或无状态服务实例的运行状况。|
|    info      | 获取有关 Service Fabric 分区的副本的信息。|
|    list      | 获取有关 Service Fabric 服务分区的副本的信息。|
|    remove    | 删除节点上运行的服务副本。|
|    report-health| 发送有关 Service Fabric 副本的运行状况报告。|
|    restart   | 重启节点上运行的持久性服务的服务副本。|


## <a name="sfctl-replica-deployed"></a>sfctl replica deployed
获取部署在 Service Fabric 节点上的副本的详细信息。

获取部署在 Service Fabric 节点上的副本的详细信息。 信息包括服务类型、服务名称、当前服务操作、当前服务操作开始日期时间、分区 ID、副本/实例 ID、报告的负载和其他信息。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --node-name    [必需]| 节点的名称。|
| --partition-id [必需]| 分区的标识。|
| --replica-id   [必需]| 副本的标识符。|
| --timeout -t          | 服务器超时，以秒为单位。  默认值：60。|

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug               | 提高日志记录详细程度，以显示所有调试日志。|
| --help -h             | 显示此帮助消息并退出。|
| --output -o           | 输出格式。  允许的值：json、jsonc、table、tsv。  默认值：json。|
| --query               | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http://jmespath.org/。|
| --verbose             | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。|

## <a name="sfctl-replica-health"></a>sfctl replica health
获取 Service Fabric 有状态服务副本或无状态服务实例的运行状况。

获取 Service Fabric 副本的运行状况。 使用 EventsHealthStateFilter 可以根据运行状态筛选针对副本报告的运行状况事件的集合。 。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --partition-id    [必需]| 分区的标识。|
| --replica-id      [必需]| 副本的标识符。|
| --events-health-state-filter| 用于根据运行状态筛选返回的 HealthEvent 对象集合。 此参数的可能值包括以下运行状态之一的整数值。 仅返回与筛选器匹配的事件。 所有事件用于评估聚合运行状态。 如果未指定，则返回所有项。 状态值为基于标志的枚举，因此该值可是使用按位“OR”运算符获取的值的组合。 例如，如果提供的值为 6，则返回 HealthState 值为 OK (2) 和 Warning (4) 的所有事件。 - Default - 默认值。 匹配任何 HealthState。 值为 0。 - None - 不与任何 HealthState 值匹配的筛选器。 未返回有关给定状态集合的结果时使用。 值为 1。 - Ok - 与 HealthState 值为 OK 的输入匹配的筛选器。 值为 2。 - Warning - 与 HealthState 值为 Warning 的输入匹配的筛选器。 值为 4。 - Error - 与 HealthState 值为 Error 的输入匹配的筛选器。 值为 8。 - All - 与具有任意 HealthState 值的输入匹配的筛选器。 值为 65535。|
| --timeout -t             | 服务器超时，以秒为单位。  默认值：60。|

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug                  | 提高日志记录详细程度，以显示所有调试日志。|
| --help -h                | 显示此帮助消息并退出。|
| --output -o              | 输出格式。  允许的值：json、jsonc、table、tsv。  默认值：json。|
| --query                  | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http://jmespath.org/。|
| --verbose                | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。|

## <a name="sfctl-replica-info"></a>sfctl replica info
获取有关 Service Fabric 分区的副本的信息。

响应包括 ID、 角色、 状态、 运行状况、 节点名称、 运行时间、 和副本有关其他详细信息。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --partition-id [必需]| 分区的标识。|
| --replica-id   [必需]| 副本的标识符。|
| --continuation-token  | 继续标记参数用于获取下一组结果。 如果单个响应无法容纳来自系统的结果，则 API 响应中包括含有非空值的继续标记。 当此值传递到下一个 API 调用时，API 返回下一组结果。 如果没有更多结果，则继续标记不包含值。 不应将此参数的值进行 URL 编码。|
| --timeout -t          | 服务器超时，以秒为单位。  默认值：60。|

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug               | 提高日志记录详细程度，以显示所有调试日志。|
| --help -h             | 显示此帮助消息并退出。|
| --output -o           | 输出格式。  允许的值：json、jsonc、table、tsv。  默认值：json。|
| --query               | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http://jmespath.org/。|
| --verbose             | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。|

## <a name="sfctl-replica-list"></a>sfctl replica list
获取有关 Service Fabric 服务分区的副本的信息。

GetReplicas 终结点返回有关指定分区的副本的信息。
响应包括 ID、 角色、 状态、 运行状况、 节点名称、 运行时间、 和副本有关其他详细信息。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --partition-id [必需]| 分区的标识。|
| --continuation-token  | 继续标记参数用于获取下一组结果。 如果单个响应无法容纳来自系统的结果，则 API 响应中包括含有非空值的继续标记。 当此值传递到下一个 API 调用时，API 返回下一组结果。 如果没有更多结果，则继续标记不包含值。 不应将此参数的值进行 URL 编码。|
| --timeout -t          | 服务器超时，以秒为单位。  默认值：60。|

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug               | 提高日志记录详细程度，以显示所有调试日志。|
| --help -h             | 显示此帮助消息并退出。|
| --output -o           | 输出格式。  允许的值：json、jsonc、table、tsv。  默认值：json。|
| --query               | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http://jmespath.org/。|
| --verbose             | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。|

## <a name="sfctl-replica-remove"></a>sfctl replica remove
删除节点上运行的服务副本。

此 API 通过从 Service Fabric 群集中删除副本来模拟 Service Fabric 副本故障。 删除操作会关闭副本，将副本转换为“无”角色，然后从群集中删除副本的所有状态信息。 此 API 测试副本状态删除路径，并通过客户端 API 模拟报告错误永久路径。 警告 - 使用此 API 时不会执行任何安全检查。 不当地使用此 API 可能导致有状态服务的数据丢失。此外，forceRemove 标志会影响同一进程中承载的其他所有副本。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --node-name    [必需]| 节点的名称。|
| --partition-id [必需]| 分区的标识。|
| --replica-id   [必需]| 副本的标识符。|
| --force-remove        | 强制删除 Service Fabric 应用程序或服务，跳过正常关闭序列。 若因服务代码中问题而无法正常关闭副本，导致应用程序或服务删除超时，可使用此参数强制删除该应用程序或服务。|
| --timeout -t          | 服务器超时，以秒为单位。  默认值：60。|

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug               | 提高日志记录详细程度，以显示所有调试日志。|
| --help -h             | 显示此帮助消息并退出。|
| --output -o           | 输出格式。  允许的值：json、jsonc、table、tsv。  默认值：json。|
| --query               | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http://jmespath.org/。|
| --verbose             | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。|

## <a name="sfctl-replica-restart"></a>sfctl replica restart
重启节点上运行的持久性服务的服务副本。

重启节点上运行的持久性服务的服务副本。 警告 - 使用此 API 时不会执行任何安全检查。 不当地使用此 API 可能导致有状态服务失去可用性。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --node-name    [必需]| 节点的名称。|
| --partition-id [必需]| 分区的标识。|
| --replica-id   [必需]| 副本的标识符。|
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
