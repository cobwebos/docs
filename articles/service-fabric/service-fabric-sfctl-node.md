---
title: Azure Service Fabric CLI- sfctl node | Microsoft Docs
description: "介绍 Service Fabric CLI sfctl node 命令。"
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
ms.openlocfilehash: 76037c7b4a2f7ada314a9360e3990245e6fbc06c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="sfctl-node"></a>sfctl node
管理构成群集的节点。

## <a name="commands"></a>命令

|命令|说明|
| --- | --- |
|    disable       | 根据指定的停用意图停用 Service Fabric 群集节点。|
|    enable        | 激活当前已停用的 Service Fabric 群集节点。|
|    health        | 获取 Service Fabric 节点的运行状况。|
|    info          | 获取 Service Fabric 群集中的节点的列表。|
|    list          | 获取 Service Fabric 群集中的节点的列表。|
|    load          | 获取 Service Fabric 节点的负载信息。|
|    remove-state  | 告知 Service Fabric，节点上的保留状态已被永久删除或丢失。|
|    report-health | 发送有关 Service Fabric 节点的运行状况报告。|
|    restart       | 重启 Service Fabric 群集节点。|
|    transition    | 启动或停止群集节点。|
|    transition-status| 获取使用 StartNodeTransition 启动的操作的进度。|


## <a name="sfctl-node-disable"></a>sfctl node disable
根据指定的停用意图停用 Service Fabric 群集节点。

根据指定的停用意图停用 Service Fabric 群集节点。 在停用的过程中，可以提高而不能降低停用意图（例如，根据 Pause 意图停用的节点可以进一步根据 Restart 停用，但反过来不行）。 停用节点后，随时可以使用“激活节点”操作将其重新激活。 如果停用操作未完成，则会取消停用。 在已关闭并在停用状态下恢复启动的节点上放置服务之前，仍需重新激活该节点，

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --node-name [必需]| 节点的名称。|
| --deactivation-intent | 描述停用节点的意图或原因。 可能的值如下。 - Pause - 指示应该暂停节点。 值为 1。 - Restart - 指示意图是在短暂的一段时间后重启节点。 值为 2。 - RemoveData - 指示意图是让节点删除数据。 值为 3。 。|
| --timeout -t       | 服务器超时，以秒为单位。  默认值：60。|

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug            | 提高日志记录详细程度，以显示所有调试日志。|
| --help -h          | 显示此帮助消息并退出。|
| --output -o        | 输出格式。  允许的值：json、jsonc、table、tsv。  默认值：json。|
| --query            | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http://jmespath.org/。|
| --verbose          | 提高日志记录详细程度。 使用 --debug 可获取完整调试日志。|

## <a name="sfctl-node-enable"></a>sfctl node enable
激活当前已停用的 Service Fabric 群集节点。

激活当前已停用的 Service Fabric 群集节点。 激活后，该节点再次成为可放置新副本的有效目标，该节点上剩余的所有已停用副本会重新激活。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --node-name [必需]| 节点的名称。|
| --timeout -t       | 服务器超时，以秒为单位。  默认值：60。|

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug            | 提高日志记录详细程度，以显示所有调试日志。|
| --help -h          | 显示此帮助消息并退出。|
| --output -o        | 输出格式。  允许的值：json、jsonc、table、tsv。  默认值：json。|
| --query            | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http://jmespath.org/。|
| --verbose          | 提高日志记录详细程度。 使用 --debug 可获取完整调试日志。|

## <a name="sfctl-node-health"></a>sfctl node health
获取 Service Fabric 节点的运行状况。

获取 Service Fabric 节点的运行状况。 使用 EventsHealthStateFilter 可以根据运行状态筛选针对节点报告的运行状况事件的集合。 如果按名称指定的节点不在运行状况存储中，则会返回错误。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --node-name       [必需]| 节点的名称。|
| --events-health-state-filter| 用于根据运行状态筛选返回的 HealthEvent 对象集合。 此参数的可能值包括以下运行状态之一的整数值。 仅返回与筛选器匹配的事件。 所有事件用于评估聚合运行状态。 如果未指定，则返回所有项。 状态值为基于标志的枚举，因此该值可是使用按位“OR”运算符获取的值的组合。 例如，如果提供的值为 6，则返回 HealthState 值为 OK (2) 和 Warning (4) 的所有事件。 - Default - 默认值。 匹配任何 HealthState。 值为零。 - None - 不与任何 HealthState 值匹配的筛选器。 未返回有关给定状态集合的结果时使用。 值为 1。 - Ok - 与 HealthState 值为 OK 的输入匹配的筛选器。 值为 2。 - Warning - 与 HealthState 值为 Warning 的输入匹配的筛选器。 值为 4。 - Error - 与 HealthState 值为 Error 的输入匹配的筛选器。 值为 8。 - All - 与具有任意 HealthState 值的输入匹配的筛选器。 值为 65535。|
| --timeout -t             | 服务器超时，以秒为单位。  默认值：60。|

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug                  | 提高日志记录详细程度，以显示所有调试日志。|
| --help -h                | 显示此帮助消息并退出。|
| --output -o              | 输出格式。  允许的值：json、jsonc、table、tsv。  默认值：json。|
| --query                  | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http://jmespath.org/。|
| --verbose                | 提高日志记录详细程度。 使用 --debug 可获取完整调试日志。|

## <a name="sfctl-node-info"></a>sfctl node info
获取 Service Fabric 群集中的节点的列表。

获取有关 Service Fabric 群集中特定节点的信息。 响应包括有关节点的名称、状态、ID、运行状况、运行时间和其他详细信息。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --node-name [必需]| 节点的名称。|
| --timeout -t       | 服务器超时，以秒为单位。  默认值：60。|

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug            | 提高日志记录详细程度，以显示所有调试日志。|
| --help -h          | 显示此帮助消息并退出。|
| --output -o        | 输出格式。  允许的值：json、jsonc、table、tsv。  默认值：json。|
| --query            | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http://jmespath.org/。|
| --verbose          | 提高日志记录详细程度。 使用 --debug 可获取完整调试日志。|

## <a name="sfctl-node-list"></a>sfctl node list
获取 Service Fabric 群集中的节点的列表。

Nodes 终结点返回有关 Service Fabric 群集中节点的信息。 响应包括有关节点的名称、状态、ID、运行状况、运行时间和其他详细信息。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --continuation-token| 继续标记参数用于获取下一组结果。 如果单个响应无法容纳来自系统的结果，则 API 响应中包括含有非空值的继续标记。      当此值传递到下一个 API 调用时，API 返回下一组结果。 如果没有更多结果，则继续标记不包含值。 不应将此参数的值进行 URL 编码。|
| --node-status-filter| 用于根据 NodeStatus 筛选节点。 仅返回与指定的筛选器值匹配的节点。 筛选器值可以是下列其中一项。 - default - 此筛选器值匹配除状态为 Unknown 或 Removed 的节点以外的其他所有节点。 -all - 此筛选器值匹配所有节点。 - up - 此筛选器值匹配已启动的节点。 - down - 此筛选器值匹配已关闭的节点。 - enabling - 此筛选器值匹配正在启用且状态为 Enabling 的节点。 - disabling - 此筛选器值匹配正在禁用且状态为 Disabling 的节点。 - disabled - 此筛选器值匹配已禁用的节点。 - unknown - 此筛选器值匹配状态为 Unknown 的节点。 如果 Service Fabric 未获得有关某个节点的权威信息，则该节点将处于 Unknown（未知）状态。 如果系统在运行时才了解某个节点，则可能会出现此情况。 - removed - 此筛选器值匹配状态为 Removed 的节点。 这些节点是使用 RemoveNodeState API 从群集中删除的节点。 。      默认值：default。|
| --timeout -t     | 服务器超时，以秒为单位。  默认值：60。|

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug          | 提高日志记录详细程度，以显示所有调试日志。|
| --help -h        | 显示此帮助消息并退出。|
| --output -o      | 输出格式。  允许的值：json、jsonc、table、tsv。  默认值：json。|
| --query          | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http://jmespath.org/。|
| --verbose        | 提高日志记录详细程度。 使用 --debug 可获取完整调试日志。|

## <a name="sfctl-node-load"></a>sfctl node load
获取 Service Fabric 节点的负载信息。

获取 Service Fabric 节点的负载信息。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --node-name [必需]| 节点的名称。|
| --timeout -t       | 服务器超时，以秒为单位。  默认值：60。|

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug            | 提高日志记录详细程度，以显示所有调试日志。|
| --help -h          | 显示此帮助消息并退出。|
| --output -o        | 输出格式。  允许的值：json、jsonc、table、tsv。  默认值：json。|
| --query            | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http://jmespath.org/。|
| --verbose          | 提高日志记录详细程度。 使用 --debug 可获取完整调试日志。|

## <a name="sfctl-node-restart"></a>sfctl node restart
重启 Service Fabric 群集节点。

重启已启动的 Service Fabric 群集节点。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --node-name [必需]| 节点的名称。|
| --create-fabric-dump  | 指定 True 会创建结构节点进程的转储。 此参数区分大小写。  默认值：False。|
| --node-instance-id | 目标节点的实例 ID。 如果指定了实例 ID，则仅当该 ID 与节点的当前实例匹配时，才重启该节点。 默认值“0”会匹配任何实例 ID。 可以使用 get node 查询获取实例 ID。  默认值：0。|
| --timeout -t       | 服务器超时，以秒为单位。  默认值：60。|

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug            | 提高日志记录详细程度，以显示所有调试日志。|
| --help -h          | 显示此帮助消息并退出。|
| --output -o        | 输出格式。  允许的值：json、jsonc、table、tsv。  默认值：json。|
| --query            | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http://jmespath.org/。|
| --verbose          | 提高日志记录详细程度。 使用 --debug 可获取完整调试日志。|

## <a name="sfctl-node-transition"></a>sfctl node transition
启动或停止群集节点。

启动或停止群集节点。  群集节点是一个进程，而不是 OS 实例本身。
若要启动节点，请为 NodeTransitionType 参数传入“Start”。 若要停止节点，请为 NodeTransitionType 参数传入“Stop”。 此 API 启动操作 - API 返回时，节点可能尚未完成转换。 结合相同的 OperationId 调用 GetNodeTransitionProgress 可获取操作进度。 。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --node-instance-id         [必需]| 目标节点的节点实例 ID。 可通过 GetNodeInfo API 确定此信息。|
| --node-name                [必需]| 节点的名称。|
| --node-transition-type     [必需]| 指示要执行的转换类型。                       NodeTransitionType.Start 启动已停止的节点。                       NodeTransitionType.Stop 停止已启动的节点。 - Invalid - 保留值。  不传入 API。 - Start - 将已停止的节点转换为启动状态。 - Stop - 将已启动的节点转换为停止状态。 。|
| --operation-id             [必需]| 用于标识此 API 的调用的 GUID。  需将此参数传入相应的 GetProgress API。|
| --stop-duration-in-seconds [必需]| 使节点保持停止状态的持续时间，以秒为单位。  最小值为 600，最大值为 14400。 此时间过后，节点会自动恢复启动状态。|
| --timeout -t                      | 服务器超时，以秒为单位。  默认值：60。|

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug                           | 提高日志记录详细程度，以显示所有调试日志。|
| --help -h                         | 显示此帮助消息并退出。|
| --output -o                       | 输出格式。  允许的值：json、jsonc、table、tsv。                       默认值：json。|
| --query                           | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http://jmespath.org/。|
| --verbose                         | 提高日志记录详细程度。 使用 --debug 可获取完整调试日志。|

## <a name="next-steps"></a>后续步骤
- [安装](service-fabric-cli.md) Service Fabric CLI。
- 了解如何通过[示例脚本](/azure/service-fabric/scripts/sfctl-upgrade-application)使用 Service Fabric CLI。