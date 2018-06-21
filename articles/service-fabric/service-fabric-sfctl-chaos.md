---
title: Azure Service Fabric CLI- sfctl chaos| Microsoft Docs
description: 介绍 Service Fabric CLI sfctl chaos 命令。
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
ms.openlocfilehash: a27cb32243d731850099da88a57f7093878becf6
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763657"
---
# <a name="sfctl-chaos"></a>sfctl chaos
启动、停止和报告 Chaos 测试服务。

## <a name="subgroups"></a>子组
|子组|说明|
| --- | --- |
| [schedule](service-fabric-sfctl-chaos-schedule.md) | 获取和设置 Chaos Schedule。 |
## <a name="commands"></a>命令

|命令|说明|
| --- | --- |
| 活动 | 根据继续标记或时间范围获取 Chaos 事件的下一段。 |
| get | 获取 Chaos 的状态。 |
| start | 在群集中启动 Chaos。 |
| stop | 如果 Chaos 正在群集中运行，则会停止 Chaos 并将 Chaos Schedule 置于已停止状态。 |

## <a name="sfctl-chaos-events"></a>sfctl chaos events
根据继续标记或时间范围获取 Chaos 事件的下一段。

若要获取 Chaos 事件的下一段，可以指定 ContinuationToken。 若要获取 Chaos 事件的新段的开头，可以通过 StartTimeUtc 和 EndTimeUtc 来指定时间范围。 在同一调用中不能同时指定 ContinuationToken 和时间范围。 当 Chaos 事件多于 100 个时，会在多个段中返回 Chaos 事件，其中每段包含不超过 100 个 Chaos 事件，若要获取下一段，请调用此 API 并使用 ContinuationToken。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --continuation-token | 继续标记参数用于获取下一组结果。 如果单个响应无法容纳来自系统的结果，则 API 响应中包括含有非空值的继续标记。 当此值传递到下一个 API 调用时，API 返回下一组结果。 如果没有更多结果，则该继续标记不包含值。 不应将此参数的值进行 URL 编码。 |
| --end-time-utc | Windows 文件时间，表示要生成 Chaos 报告的时间范围的结束时间。 有关详细信息，请参阅 [DateTime.ToFileTimeUtc 方法](https\://msdn.microsoft.com/library/system.datetime.tofiletimeutc(v=vs.110).aspx)。 |
| --max-results | 作为分页查询的一部分返回的最大结果数。 此参数定义返回结果数的上限。 如果根据配置中定义的最大消息大小限制，无法将这些结果容纳到消息中，则返回的结果数可能小于指定的最大结果数。 如果此参数为零或者未指定，则分页查询包含返回消息中最多可容纳的结果数。 |
| --start-time-utc | Windows 文件时间，表示要生成 Chaos 报告的时间范围的开始时间。 有关详细信息，请参阅 [DateTime.ToFileTimeUtc 方法](https\://msdn.microsoft.com/library/system.datetime.tofiletimeutc(v=vs.110).aspx)。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值：60。 |

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值：json、jsonc、table、tsv。  默认值：json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-chaos-get"></a>sfctl chaos get
获取 Chaos 的状态。

获取 Chaos 的状态（指示 Chaos 是否正在运行）、用于运行 Chaos 的 Chaos 参数，以及 Chaos Schedule 的状态。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --timeout -t | 服务器超时，以秒为单位。  默认值：60。 |

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值：json、jsonc、table、tsv。  默认值：json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-chaos-start"></a>sfctl chaos start
在群集中启动 Chaos。

如果尚未在群集中运行 Chaos，则使用 Chaos 参数中指定的值启动 Chaos。 如果进行此调用时运行混沌，调用将失败，错误代码为 FABRIC_E_CHAOS_ALREADY_RUNNING。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --app-type-health-policy-map | 包含特定应用程序类型的最大不正常应用程序百分比的 JSON 编码列表。 每个项以键的形式指定应用程序类型名称，以值的形式指定一个整数，该整数表示用于评估指定应用程序类型的应用程序的 MaxPercentUnhealthyApplications 百分比。 <br><br> 定义包含特定应用程序类型的最大不正常应用程序百分比的映射。 每个项以键的形式指定应用程序类型名称，以值的形式指定一个整数，该整数表示用于评估指定应用程序类型的应用程序的 MaxPercentUnhealthyApplications 百分比。 <br><br> 应用程序类型的运行状况策略对应可以在群集运行状况评估期间，用于描述特殊的应用程序类型。 映射中包含的应用程序类型根据映射中指定的百分比，而不是群集运行状况策略中定义的全局 MaxPercentUnhealthyApplications 进行评估。 映射中指定的应用程序类型不会计入全局应用程序池。 例如，如果某种类型的应用程序至关重要，群集管理员可以将条目添加到该应用程序类型的映射，并将其分配值为 0%（即，不容忍任何失败）。 可以使用设置为 20% 容忍数千个应用程序实例外的一些故障 MaxPercentUnhealthyApplications 计算所有其他应用程序。 仅当群集清单启用应用程序类型运行状况评估的配置条目用于 HealthManager/EnableApplicationTypeHealthEvaluation 使用应用程序类型运行状况策略映射。 |
| --chaos-target-filter | JSON 编码字典具有两个字符串类型键。 这两个键是 NodeTypeInclusionList 和 ApplicationInclusionList。 这两个键的值为字符串列表。 chaos_target_filter 定义所有筛选器目标混沌错误，例如，出错仅特定节点类型或出错仅某些应用程序。 <br><br> 如未使用 chaos_target_filter，Chaos 会使所有群集实体故障。 如果使用 chaos_target_filter，Chaos 仅使满足 chaos_target_filter 规定的实体故障。 NodeTypeInclusionList 和 ApplicationInclusionList 仅允许联合语义。 不可指定 NodeTypeInclusionList 和 ApplicationInclusionList 的交集。 例如，不可指定“仅当此应用程序在该节点类型上时使其故障”。 一旦实体包含在 NodeTypeInclusionList 或 ApplicationInclusionList 中，便不能使用 ChaosTargetFilter 排除该实体。 即使 applicationX 未出现在 ApplicationInclusionList 中，在一些 Chaos 迭代中，也可使 applicationX 故障，因为它恰好在 NodeTypeInclusionList 中的 nodeTypeY 的节点上。 如果 NodeTypeInclusionList 和 ApplicationInclusionList 为空，则会引发 ArgumentException。 <br><br> 所有类型故障（重启节点、重启代码包、删除副本、重启副本、移动主副本和移动辅助副本）均为这些节点类型的节点启用。 如果节点类型（比如 NodeTypeX）未出现在 NodeTypeInclusionList 中，节点级别故障（比如 NodeRestart）将不会为 NodeTypeX 的节点启用。但是，如果 ApplicationInclusionList 中的应用程序碰巧位于 NodeTypeX 的节点上，那么代码包和副本故障仍可为 NodeTypeX 启用。 此列表最多可以包含 100 个节点类型名称，若要增加，MaxNumberOfNodeTypesInChaosEntityFilter 配置需要升级。 <br><br> 所有属于这些应用程序服务的副本服从 Chaos 的副本故障（重启副本、删除副本、移动主副本和移动辅助副本）。 仅在代码包仅托管这些应用程序的副本时，Chaos 可重启代码包。 如果应用程序未出现在此列表中，那么还是可以在某些 Chaos 迭代中使它故障，条件是应用程序最终位于 NodeTypeInclusionList 中的节点类型的节点上。 但是，如果 applicationX 通过放置约束固定为 nodeTypeY，并且 applicationX 不在 ApplicationInclusionList 中同时 nodeTypeY 不在 NodeTypeInclusionList 中，那么不会使 applicationX 故障。 此列表最多可以包含 1000 个应用程序名称，若要增加，MaxNumberOfApplicationsInChaosEntityFilter 配置需要升级。 |
| --context | 类型键值对的 JSON 编码映射 (string, string)。 此映射可用于记录 Chaos 的相关运行信息。 这种键值对不能超过 100 个，并且每个字符串（键或值）的长度不能超过 4095 个字符。 此映射由 Chaos 运行的启动程序设置为根据需要存储特定运行的相关上下文。 |
| --disable-move-replica-faults | 禁用移动主副本错误和移动辅助副本错误。 |
| --max-cluster-stabilization | 等待所有群集实体变稳定和正常运行的最长时间。  默认值：60。 <br><br> 在每次迭代开始时它的验证群集实体的运行状况和混沌在迭代中执行。 在验证期间是否群集实体不稳定状态并且正常内 MaxClusterStabilizationTimeoutInSeconds，混沌会生成验证失败的事件。 |
| --max-concurrent-faults | 每次迭代造成的最大并发错误数。 混沌执行在迭代中，并验证阶段用分隔两个连续的迭代。 并发越高，故障更主动-将系列更复杂的状态，以发现 bug 的注入。 建议是以开头的值为 2 或 3 并向上移动时请务必小心。  默认值：1。 |
| --max-percent-unhealthy-apps | 在 Chaos 期间评估群集运行状况时，报告错误之前允许的最大不正常应用程序百分比。 <br><br> 报告错误之前允许的最大不正常应用程序百分比。 例如，若要允许 10% 的应用程序处于不正常状态，此值为 10。 该百分比表示在将群集视为出错之前可处于不正常状态的应用程序的最大容许百分比。 如果未超过该百分比，但至少存在一个不正常的应用程序，则将运行状况评估为 Warning。 该百分比的计算方式是将不正常的应用程序数除以群集中的应用程序实例总数，不包括 ApplicationTypeHealthPolicyMap 中包含的应用程序类型的应用程序。 计算结果调高为整数，以便容忍少量应用程序出现一次失败。 默认百分比为零。 |
| --max-percent-unhealthy-nodes | 在 Chaos 期间评估群集运行状况时，报告错误之前允许的最大不正常节点百分比。 <br><br> 报告错误之前允许的最大不正常节点百分比。 例如，若要允许 10% 的节点处于不正常状态，此值为 10。 该百分比表示在将群集视为出错之前可处于不正常状态的节点的最大容许百分比。 如果未超过该百分比，但至少存在一个不正常的节点，则将运行状况评估为警告。 该百分比的计算方式是将不正常的节点数除以群集中的节点总数。 计算结果调高为整数，以便容忍少量节点上出现一次失败。 默认百分比为零。 在大型群集中，始终会有一些要关闭或需要修复的节点，因此应配置此百分比以便容忍这种情况。 |
| --time-to-run | Chaos 在自动停止之前要运行的总时间（以秒为单位）。 最大允许值为 4,294,967,295 (System.UInt32.MaxValue)。  默认值：4294967295。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值：60。 |
| --wait-time-between-faults | 单次迭代中发生连续错误的间隔等待时间（以秒为单位）。  默认值：20。 <br><br> 值越大越低错误和更简单之间重叠的状态的序列转换群集内通过。 建议是从开始向上移动时的 1 和 5 和练习请小心之间的值。 |
| --wait-time-between-iterations | Chaos 的每两次连续迭代的间隔时间（以秒为单位）。 值越大，故障注入率越低。  默认值：30。 |
| --warning-as-error | 将运行状况策略设置为将警告视为错误。 |

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值：json、jsonc、table、tsv。  默认值：json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-chaos-stop"></a>sfctl chaos stop
如果 Chaos 正在群集中运行，则会停止 Chaos 并将 Chaos Schedule 置于已停止状态。

使 Chaos 停止执行新故障。 正在进行的故障将继续执行，直到完成。 当前的 Chaos Schedule 将被置于已停止状态。 当某个计划停止后，它将保持已停止状态，不会被 Chaos Schedule 用来安排 Chaos 的新运行。 若要恢复计划执行，必须设置新的 Chaos Schedule。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
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