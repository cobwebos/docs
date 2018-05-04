---
title: Azure Service Fabric CLI - sfctl cluster | Microsoft Docs
description: 介绍 Service Fabric CLI sfctl cluster 命令。
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 02/22/2018
ms.author: ryanwi
ms.openlocfilehash: c83dc3eeb6ca0d66b0c70236354fd7bab80f355f
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/20/2018
---
# <a name="sfctl-cluster"></a>sfctl cluster
选择、管理和操作 Service Fabric 群集。

## <a name="commands"></a>命令

|命令|说明|
| --- | --- |
|    code-versions| 获取 Service Fabric 群集中预配的结构代码版本的列表。|
|    config-versions | 获取 Service Fabric 群集中预配的结构配置版本的列表。|
|    health       | 获取 Service Fabric 群集的运行状况。|
|    manifest     | 获取 Service Fabric 群集清单。|
|    operation-cancel| 取消用户造成的错误操作。|
|    operationgit | 获取根据提供的输入筛选的、用户造成的错误操作列表。|
|    provision     | 预配 Service Fabric 群集的代码包或配置包。|
|    recover-system  | 向 Service Fabric 群集指出应该尝试恢复当前停滞在仲裁丢失状态的系统服务。|
|report-health   | 发送有关 Service Fabric 群集的运行状况报告。|
|    选择       | 连接到 Service Fabric 群集终结点。|
| unprovision     | 取消预配 Service Fabric 群集的代码包或配置包。|
|    升级         | 开始升级 Service Fabric 群集的代码或配置版本。|
|    upgrade-resume  | 使群集升级转移到下一个升级域。|
|    upgrade-rollback| 回滚 Service Fabric 群集的升级。|
|    upgrade-status  | 获取当前群集升级的进度。|
|upgrade-update  | 更新 Service Fabric 群集升级的升级参数。|


## <a name="sfctl-cluster-health"></a>sfctl cluster health
获取 Service Fabric 群集的运行状况。

获取 Service Fabric 群集的运行状况。 使用 EventsHealthStateFilter 可以根据运行状态筛选针对群集报告的运行状况事件的集合。 同样，使用 NodesHealthStateFilter 和 ApplicationsHealthStateFilter 可以根据聚合运行状态筛选返回的节点和应用程序集合。 

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --applications-health-state-filter| 用于根据运行状态筛选群集运行状况查询结果中返回的应用程序运行状态对象。 此参数的可能值包括从成员获取的整数值或对 HealthStateFilter 枚举成员进行位运算获取的整数值。 仅返回与筛选器匹配的应用程序。  所有应用程序都用于评估聚合运行状态。 如果未指定，则返回所有项。 状态值为基于标志的枚举，因此该值可以是使用按位“OR”运算符获取的值的组合。 例如，如果提供的值为 6，则返回 HealthState 值为“OK”(2) 和“Warning”(4) 的应用程序的运行状态。 - Default - 默认值。 匹配任何 HealthState。 值为 0。 - None - 不与任何 HealthState 值匹配的筛选器。 不返回有关给定状态集合的结果时使用。 值为 1。 - Ok - 与 HealthState 值为 OK 的输入匹配的筛选器。 值为 2。 - Warning - 与 HealthState 值为 Warning 的输入匹配的筛选器。 值为 4。 - Error - 与 HealthState 值为 Error 的输入匹配的筛选器。 值为 8。 - All - 与具有任意 HealthState 值的输入匹配的筛选器。 值为 65535。|
| --events-health-state-filter   | 用于根据运行状况筛选返回的 HealthEvent 对象集合。 此参数的可能值包括以下运行状态之一的整数值。 仅返回与筛选器匹配的事件。 所有事件用于评估聚合运行状态。 如果未指定，则返回所有项。 状态值为基于标志的枚举，因此该值可以是使用按位“OR”运算符获取的值的组合。 例如，如果提供的值为 6，则返回 HealthState 值为 OK (2) 和 Warning (4) 的所有事件。 - Default - 默认值。 匹配任何 HealthState。 值为 0。 - None - 不与任何 HealthState 值匹配的筛选器。 不返回有关给定状态集合的结果时使用。 值为 1。 - Ok - 与 HealthState 值为 OK 的输入匹配的筛选器。 值为 2。 - Warning - 与 HealthState 值为 Warning 的输入匹配的筛选器。  值为 4。 - Error - 与 HealthState 值为 Error 的输入匹配的筛选器。 值为 8。 - All - 与具有任意 HealthState 值的输入匹配的筛选器。 值为 65535。|
|--exclude-health-statistics                   | 指示运行状况统计数据是否应作为查询结果的一部分返回。 默认值为 False。 统计信息显示处于 Ok、Warning 和 Error 运行状况的子实体数。|
 |   --include-system-application-health-statistics| 指示运行状况统计信息是否应包括 fabric:/System 应用程序的运行状况统计信息。 默认值为 False。 如果 IncludeSystemApplicationHealthStatistics 设置为 true，则运行状况统计信息包含属于 fabric:/System 应用程序的实体。 否则，查询结果仅包含用户应用程序的运行状况统计信息。 应用此参数后，查询结果中必须包含运行状况统计信息。|
| --nodes-health-state-filter    | 用于根据运行状态筛选群集运行状况查询结果中返回的节点运行状态对象。 此参数的可能值包括以下运行状态之一的整数值。 仅返回与筛选器匹配的节点。 所有节点用于评估聚合运行状态。 如果未指定，则返回所有项。 状态值为基于标志的枚举，因此该值可以是使用按位“OR”运算符获取的值的组合。 例如，如果提供的值为“6”，则返回 HealthState 值为 OK (2) 和 Warning (4) 的节点的运行状态。 - Default - 默认值。 匹配任何 HealthState。 值为 0。 - None - 不与任何 HealthState 值匹配的筛选器。 不返回有关给定状态集合的结果时使用。 值为 1。 - Ok - 与 HealthState 值为 OK 的输入匹配的筛选器。 值为 2。 - Warning - 与 HealthState 值为 Warning 的输入匹配的筛选器。  值为 4。 - Error - 与 HealthState 值为 Error 的输入匹配的筛选器。 值为 8。 - All - 与具有任意 HealthState 值的输入匹配的筛选器。 值为 65535。|
| --timeout -t                   | 服务器超时，以秒为单位。  默认值：60。|

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug                        | 提高日志记录详细程度，以显示所有调试日志。|
| --help -h                      | 显示此帮助消息并退出。|
| --output -o                    | 输出格式。  允许的值：json、jsonc、table、tsv。                    默认值：json。|
| --query                        | JMESPath 查询字符串。 有关更多信息和示例，请参阅 http://jmespath.org/。|
| --verbose                      | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。|

## <a name="sfctl-cluster-manifest"></a>sfctl cluster manifest
获取 Service Fabric 群集清单。

获取 Service Fabric 群集清单。 群集清单包含群集的属性，包括群集上的不同节点类型、安全配置、错误和升级域拓扑，等等。这些属性是在部署独立群集时作为 ClusterConfig.JSON 文件的一部分指定的。 但是，群集清单中的大部分信息是在其他部署方案中部署群集期间由 Service Fabric 在内部生成的（例如，使用 [Azure 门户](https://portal.azure.com)时）。 群集清单的内容仅供参考，用户不应依赖于文件内容的格式或其解释。 

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
| --query  | JMESPath 查询字符串。 有关更多信息和示例，请参阅 http://jmespath.org/。|
| --verbose| 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。|

## <a name="sfctl-cluster-provision"></a>sfctl cluster provision
预配 Service Fabric 群集的代码包或配置包。
验证和预配 Service Fabric 群集的代码包或配置包。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
|--cluster-manifest-file-path| 群集清单文件路径。|
|    --code-file-path            | 群集代码包文件路径。|
|    --timeout -t                | 服务器超时，以秒为单位。  默认值：60。|

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。|
| --help -h  | 显示此帮助消息并退出。|
| --output -o| 输出格式。  允许的值：json、jsonc、table、tsv。  默认值：json。|
| --query | JMESPath 查询字符串。 有关更多信息和示例，请参阅 http://jmespath.org/。|
| --verbose  | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。|

## <a name="sfctl-cluster-select"></a>sfctl cluster select
连接到 Service Fabric 群集终结点。

如果连接到安全群集，请指定证书 (.crt) 和密钥文件 (.key)，或指定包含此两者的单个文件 (.pem)。 不要同时指定上述两项。 （可选）如果连接到安全群集，则还要指定 CA 捆绑文件的路径，或受信任 CA 证书的目录。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --endpoint [必需]| 群集终结点 URL，包括端口和 HTTP 或 HTTPS 前缀。|
| --aad             | 使用 Azure Active Directory 进行身份验证。|
| --ca              | 视为有效的 CA 证书目录的路径，或 CA 捆绑文件的路径。|
| --cert            | 客户端证书文件的路径。|
| --key             | 客户端证书密钥文件的路径。|
| --no-verify       | 使用 HTTPS 时禁用证书验证。注意：这是一个不安全的选项，不应该用于生产环境。|
| --pem             | 采用 .pem 文件格式的客户端证书的路径。|

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug           | 提高日志记录详细程度，以显示所有调试日志。|
| --help -h         | 显示此帮助消息并退出。|
| --output -o       | 输出格式。  允许的值：json、jsonc、table、tsv。  默认值：json。|
| --query           | JMESPath 查询字符串。 有关更多信息和示例，请参阅 http://jmespath.org/。|
| --verbose         | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。|

## <a name="sfctl-cluster-unprovision"></a>sfctl cluster unprovision
取消预配 Service Fabric 群集的代码包或配置包。

取消预配 Service Fabric 群集的代码包或配置包。 支持分别取消预配代码和配置。

### <a name="arguments"></a>参数
|参数|说明|
| --- | --- |
|--code-version  | 群集代码包版本。|
|    --config-version| 群集清单版本。|
|    --timeout -t    | 服务器超时，以秒为单位。  默认值：60。|

### <a name="global-arguments"></a>全局参数
|参数|说明|
| --- | --- |
|--debug         | 提高日志记录详细程度，以显示所有调试日志。|
 |   --help -h       | 显示此帮助消息并退出。|
 |   --output -o     | 输出格式。  允许的值：json、jsonc、table、tsv。  默认值：json。|
 |   --query         | JMESPath 查询字符串。 有关更多信息和示例，请参阅 http://jmespath.org/。|
 |   --verbose       | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。|


## <a name="sfctl-cluster-upgrade"></a>sfctl cluster upgrade
开始升级 Service Fabric 群集的代码或配置版本。
如果参数有效，验证提供的升级参数并对 Service Fabric 群集的代码或配置版本启动升级。

### <a name="arguments"></a>参数
|参数|说明|
| --- | --- |
|    --app-health-map                      | 应用程序名称对的 JSON 编码字典以及引发错误之前的最大不正常百分比。|
 |   --app-type-health-map                 | 应用程序类型名称对的 JSON 编码字典以及引发错误之前的最大不正常百分比。|
 |   --code-version                        | 群集代码版本。|
 |   --config-version                      | 群集配置版本。|
 |   --delta-health-evaluation             | 每个升级域完成后，启用增量运行状况评估，而不是绝对运行状况评估。|
 |   --delta-unhealthy-nodes               | 群集升级过程中允许的节点运行状况降级最大百分比。  默认值：10。 在开始升级时的节点状态与运行状况评估时的节点状态之间测得的增量值。 每个升级域升级完成后执行检查，确保群集的全局状态在允许的限制内。|
 |   --failure-action                      | 可能的值包括：“Invalid”、“Rollback”和“Manual”。|
 |   --force-restart                       | 强制重启。|
 |   --health-check-retry                  | 运行状况检查重试超时时间，以毫秒为单位。|
 |   --health-check-stable                 | 运行状况检查稳定持续时间，以毫秒为单位。|
  |  --health-check-wait                   | 运行状况检查等待持续时间，以毫秒为单位。|
  |  --replica-set-check-timeout           | 升级副本集检查超时时间，以秒为单位。|
 |   --rolling-upgrade-mode                | 可能的值包括：“Invalid”、“UnmonitoredAuto”、“UnmonitoredManual”和“Monitored”。  默认值：UnmonitoredAuto。|
  |  --timeout -t                          | 服务器超时，以秒为单位。  默认值：60。|
  |  --unhealthy-applications              | 报告错误之前允许的最大不正常应用程序百分比。 例如，若要允许 10% 的应用程序处于不正常状态，此值为 10。 该百分比表示在将群集视为出错之前可处于不正常状态的应用程序的最大容许百分比。 如果未超过该百分比，但至少存在一个不正常的应用程序，则将运行状况评估为 Warning。 该百分比的计算方式是将不正常的应用程序数除以群集中的应用程序实例总数，不包括 ApplicationTypeHealthPolicyMap 中包含的应用程序类型的应用程序。 计算结果调高为整数，以便容忍少量应用程序出现一次失败。|
 |   --unhealthy-nodes                     | 报告错误之前允许的最大不正常节点百分比。 例如，若要允许 10% 的节点处于不正常状态，此值为 10。 该百分比表示在将群集视为出错之前可处于不正常状态的节点的最大容许百分比。 如果未超过该百分比，但至少存在一个不正常的节点，则将运行状况评估为警告。 该百分比的计算方式是将不正常的节点数除以群集中的节点总数。 计算结果调高为整数，以便容忍少量节点上出现一次失败。 在大型群集中，始终会有一些要关闭或需要修复的节点，因此应配置此百分比以便容忍这种情况。|
 |   --upgrade-domain-delta-unhealthy-nodes| 群集升级过程中允许的升级域节点运行状况降级最大百分比。 默认值：15。 在开始升级时的升级域节点状态与运行状况评估时的升级域节点状态之间测得的增量值。 每个升级域升级完成后对所有已完成的升级域执行检查，以便确保升级域的状态在允许的限制内。|
 |   --upgrade-domain-timeout              | 升级域超时时间，以毫秒为单位。|
 |   --upgrade-timeout                     | 升级超时时间，以毫秒为单位。|
 |   --warning-as-error                    | 将警告的严重性视为与错误相同。|

### <a name="global-arguments"></a>全局参数
|参数|说明|
| --- | --- |
|--debug                               | 提高日志记录详细程度，以显示所有调试日志。|
|    --help -h                             | 显示此帮助消息并退出。|
|    --output -o                           | 输出格式。  允许的值：json、jsonc、table、tsv。 默认值：json。|
|    --query                               | JMESPath 查询字符串。 有关更多信息和示例，请参阅 http://jmespath.org/。|
|    --verbose                             | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。|

## <a name="next-steps"></a>后续步骤
- [安装](service-fabric-cli.md) Service Fabric CLI。
- 了解如何通过[示例脚本](/azure/service-fabric/scripts/sfctl-upgrade-application)使用 Service Fabric CLI。