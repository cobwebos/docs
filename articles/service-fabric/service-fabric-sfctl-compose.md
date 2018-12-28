---
title: Azure Service Fabric CLI - sfctl compose | Microsoft Docs
description: 介绍 Service Fabric CLI sfctl compose 命令。
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
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: d71b0c020fb9ceb305b56216d466bacb42ad21e8
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/12/2018
ms.locfileid: "53278145"
---
# <a name="sfctl-compose"></a>sfctl compose
创建、删除和管理 Docker Compose 应用程序。

## <a name="commands"></a>命令

|命令|Description|
| --- | --- |
| create | 创建 Service Fabric compose 部署。 |
| list | 获取在 Service Fabric 群集中创建的 compose 部署列表。 |
| remove | 从群集中删除现有的 Service Fabric compose 部署。 |
| status | 获取有关 Service Fabric compose 部署的信息。 |
| 升级 | 开始升级 Service Fabric 群集中的 compose 部署。 |
| upgrade-rollback | 开始在 Service Fabric 群集中回退 compose 部署升级。 |
| upgrade-status | 获取在此 Service Fabric Compose 部署中执行的最新升级的详细信息。 |

## <a name="sfctl-compose-create"></a>sfctl compose create
创建 Service Fabric compose 部署。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --deployment-name [必需] | 部署的名称。 |
| --file-path       [必需] | 目标 Docker Compose 文件的路径。 |
| --encrypted-pass | 不提示输入容器注册表密码，而是使用已加密的通行短语。 |
| --has-pass | 将提示输入容器注册表的密码。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |
| --user | 用于连接容器注册表的用户名。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-compose-list"></a>sfctl compose list
获取在 Service Fabric 群集中创建的 compose 部署列表。

获取 Service Fabric 群集中已创建的或正在创建的 compose 部署的状态。 响应包括名称、状态和有关 compose 部署的其他详细信息。 如果部署列表在一页中显示不下，则将返回一页结果及一个继续标记，该标记可用于获取下一页。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --continuation-token | 继续标记参数用于获取下一组结果。 如果单个响应无法容纳来自系统的结果，则 API 响应中包括含有非空值的继续标记。 当此值传递到下一个 API 调用时，API 返回下一组结果。 如果没有更多结果，则该继续标记不包含值。 不应将此参数的值进行 URL 编码。 |
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

## <a name="sfctl-compose-remove"></a>sfctl compose remove
从群集中删除现有的 Service Fabric compose 部署。

删除现有的 Service Fabric compose 部署。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --deployment-name [必需] | 部署的标识。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-compose-status"></a>sfctl compose status
获取有关 Service Fabric compose 部署的信息。

返回 Service Fabric 群集中已创建或正在创建的、且名称与指定为参数的部署相匹配的 compose 部署的状态。 响应包括名称、状态和有关部署的其他详细信息。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --deployment-name [必需] | 部署的标识。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-compose-upgrade"></a>sfctl compose upgrade
开始升级 Service Fabric 群集中的 compose 部署。

验证提供的升级参数，如果参数有效，则开始升级部署。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --deployment-name [必需] | 部署的名称。 |
| --file-path        [必需] | 目标 Docker compose 文件的路径。 |
| --default-svc-type-health-map | 描述用于评估服务运行状况的运行状况策略的 JSON 编码字典。 |
| --encrypted-pass | 不提示输入容器注册表密码，而是使用已加密的通行短语。 |
| --failure-action | 可能的值包括\:“Invalid”、“Rollback”和“Manual”。 |
| --force-restart | 即使代码版本没有改变，也在升级过程中强制重新启动进程。 <br><br> 升级仅更改配置或数据。 |
| --has-pass | 将提示输入容器注册表的密码。 |
| --health-check-retry | 应用程序或群集不正常时尝试执行运行状况检查所间隔的时间长度。 |
| --health-check-stable | 升级继续到下一升级域之前，应用程序或群集必须保持正常的时长。 <br><br> 首先，会将其解释为表示 ISO 8601 持续时间的一个字符串。 如果那失败，则会将其解释为表示总毫秒数的一个数字。 |
| --health-check-wait | 启动运行状况检查进程之前，完成升级域后等待的时间长度。 |
| --replica-set-check | 出现意外问题时，阻止处理升级域并防止可用性丢失的最大时长。 <br><br> 当此超时到期时，无论是否存在可用性丢失问题，都将继续处理升级域。 每个升级域启动时重置超时。 有效值介于 0 和 42949672925（含）之间。 |
| --svc-type-health-map | 描述用于评估不同服务类型的运行状况的运行状况策略的 JSON 编码对象列表。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |
| --unhealthy-app | 报告错误之前允许的最大不正常应用程序百分比。 <br><br> 例如，若要允许 10% 的应用程序处于不正常状态，此值为 10。 该百分比表示在将群集视为出错之前可处于不正常状态的应用程序的最大容许百分比。 如果未超过该百分比，但至少存在一个不正常的应用程序，则将运行状况评估为 Warning。 该百分比的计算方式是将不正常的应用程序数除以群集中的应用程序实例总数。 |
| --upgrade-domain-timeout | 执行 FailureAction 前，每个升级域需等待的时长。 <br><br> 首先，会将其解释为表示 ISO 8601 持续时间的一个字符串。 如果那失败，则会将其解释为表示总毫秒数的一个数字。 |
| --upgrade-kind | 默认值\: Rolling。 |
| --upgrade-mode | 可能的值包括\:“Invalid”、“UnmonitoredAuto”、“UnmonitoredManual”和“Monitored”。  默认值\: UnmonitoredAuto。 |
| --upgrade-timeout | 执行 FailureAction 前，完成整个升级需等待的时长。 <br><br> 首先，会将其解释为表示 ISO 8601 持续时间的一个字符串。 如果那失败，则会将其解释为表示总毫秒数的一个数字。 |
| --user | 用于连接容器注册表的用户名。 |
| --warning-as-error | 指示是否将警告的严重性视为与错误相同。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-compose-upgrade-rollback"></a>sfctl compose upgrade-rollback
开始在 Service Fabric 群集中回退 compose 部署升级。

回退 Service Fabric compose 部署升级。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --deployment-name [必需] | 部署的标识。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-compose-upgrade-status"></a>sfctl compose upgrade-status
获取在此 Service Fabric Compose 部署中执行的最新升级的详细信息。

返回有关 compose 部署升级状态的信息以及可帮助调试应用程序运行状况问题的详细信息。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --deployment-name [必需] | 部署的标识。 |
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