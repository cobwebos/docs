---
title: Azure Service Fabric CLI - sfctl chaos | Microsoft Docs
description: "介绍 Service Fabric CLI sfctl chaos 命令。"
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
ms.translationtype: HT
ms.sourcegitcommit: cb9130243bdc94ce58d6dfec3b96eb963cdaafb0
ms.openlocfilehash: 7118ec3f5aeae40fa7ecc592309a28b585c651a1
ms.contentlocale: zh-cn
ms.lasthandoff: 09/26/2017

---
# <a name="sfctl-chaos"></a>sfctl chaos
启动、停止和报告混沌测试服务。

## <a name="commands"></a>命令

|命令|说明|
| --- | --- |
|    report| 根据传入的继续标记或传入的时间范围获取混沌测试报告的下一段。|
|    start | 如果尚未在群集中运行混沌测试，则使用混沌测试参数中指定的值开始运行混沌测试。|
|    stop  | 在群集中停止混沌测试（如果已运行），否则不执行任何操作。|


## <a name="sfctl-chaos-report"></a>sfctl chaos report
根据传入的继续标记或传入的时间范围获取混沌测试报告的下一段。

可以指定 ContinuationToken 以获取混沌测试报告的下一段或者通过 StartTimeUtc 和 EndTimeUtc 指定时间范围，但不能在同一调用中同时指定 ContinuationToken 和时间范围。 如果发生了 100 个以上的混沌测试事件，混沌测试报告会分段返回，每一段包含的混沌测试事件均不能超过 100 个。 

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --continuation-token| 继续标记参数用于获取下一组结果。 如果单个响应无法容纳来自系统的结果，则 API 响应中包括含有非空值的继续标记。 当此值传递到下一个 API 调用时，API 返回下一组结果。 如果没有更多结果，则继续标记不包含值。 不应将此参数的值进行 URL 编码。|
| --end-time-utc   | 表示时间范围结束时间的计时周期的计数
                          Chaos report is to be generated. Please consult [DateTime.Ticks
                          Property](https://msdn.microsoft.com/en-
                          us/library/system.datetime.ticks%28v=vs.110%29) for details about tick.|
| --start-time-utc | 表示要生成混沌测试报告的时间范围开始时间的计时周期的计数。 请参阅 [DateTime.Ticks 属性](https://msdn.microsoft.com/en- us/library/system.datetime.ticks%28v=vs.110%29) 详细了解计时周期。| | --timeout -t     | 服务器超时时间，以秒为单位。  默认值：60。|

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug          | 提高日志记录详细程度，以显示所有调试日志。|
| --help -h        | 显示此帮助消息并退出。|
| --output -o      | 输出格式。  允许的值：json、jsonc、table、tsv。  默认值：json。|
| --query          | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http://jmespath.org/。|
| --verbose        | 提高日志记录详细程度。 使用 --debug 可获取完整调试日志。|

## <a name="sfctl-chaos-start"></a>sfctl chaos start
如果尚未在群集中运行混沌测试，则使用混沌测试参数中指定的值开始运行混沌测试。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --app-type-health-policy-map  | 包含特定应用程序类型的最大不正常应用程序百分比的 JSON 编码列表。 每个项以键的形式指定应用程序类型名称，以值的形式指定一个整数，该整数表示用于评估指定应用程序类型的应用程序的 MaxPercentUnhealthyApplications 百分比。|
| --disable-move-replica-faults | 禁用移动主副本错误和移动辅助副本错误。|
| --max-cluster-stabilization| 等待所有群集实体变稳定和正常运行的最长时间。  默认值：60。|
| --max-concurrent-faults    | 每次迭代造成的最大并发错误数。           默认值：1。|
| --max-percent-unhealthy-apps  | 在混沌测试期间评估群集运行状况时，报告错误之前允许的最大不正常应用程序百分比。|
| --max-percent-unhealthy-nodes | 在混沌测试期间评估群集运行状况时，报告错误之前允许的最大不正常节点百分比。|
| --time-to-run              | 混沌测试在自动停止之前要运行的总时间（以秒为单位）。 最大允许值为 4,294,967,295 (System.UInt32.MaxValue)。  默认值：4294967295。|
| --timeout -t               | 服务器超时，以秒为单位。  默认值：60。|
| --wait-time-between-faults | 单次迭代中发生连续错误的间隔等待时间（以秒为单位）。  默认值：20。|
| --wait-time-between-iterations| 混沌测试的每两次连续迭代的间隔时间（以秒为单位）。  默认值：30。|
| --warning-as-error         | 在混沌测试期间评估群集运行状况时，将警告的严重性视为与错误相同。|

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug                    | 提高日志记录详细程度，以显示所有调试日志。|
| --help -h                  | 显示此帮助消息并退出。|
| --output -o                | 输出格式。  允许的值：json、jsonc、table、tsv。           默认值：json。|
| --query                    | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http://jmespath.org/。|
| --verbose                  | 提高日志记录详细程度。 使用 --debug 可获取完整调试日志。|

## <a name="sfctl-chaos-stop"></a>sfctl chaos stop
在群集中停止混沌测试（如果已运行），否则不执行任何操作。

通过计划更多的错误来停止混沌测试，但即时的错误不受影响。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --timeout -t| 服务器超时，以秒为单位。  默认值：60。|

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug  | 提高日志记录详细程度，以显示所有调试日志。|
| --help -h| 显示此帮助消息并退出。|
| --output -o | 输出格式。  允许的值：json、jsonc、table、tsv。  默认值：json。|
| --query  | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http://jmespath.org/。|
| --verbose| 提高日志记录详细程度。 使用 --debug 可获取完整调试日志。|

## <a name="next-steps"></a>后续步骤
- [安装](service-fabric-cli.md) Service Fabric CLI。
- 了解如何通过[示例脚本](/azure/service-fabric/scripts/sfctl-upgrade-application)使用 Service Fabric CLI。
