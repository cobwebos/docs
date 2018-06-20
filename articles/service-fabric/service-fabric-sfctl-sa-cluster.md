---
title: Azure Service Fabric CLI- sfctl sa-cluster | Microsoft Docs
description: 介绍 Service Fabric CLI sfctl standalone cluster 命令。
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
ms.openlocfilehash: ffdbff7edc5af187071615c8b1e61790b3a38429
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763925"
---
# <a name="sfctl-sa-cluster"></a>sfctl sa-cluster
管理独立 Service Fabric 群集。

## <a name="commands"></a>命令

|命令|说明|
| --- | --- |
| config | 获取 Service Fabric 独立群集配置。 |
| config-upgrade | 开始升级 Service Fabric 独立群集的配置。 |
| upgrade-status | 获取 Service Fabric 独立群集的群集配置升级状态。 |

## <a name="sfctl-sa-cluster-config"></a>sfctl sa-cluster config
获取 Service Fabric 独立群集配置。

获取 Service Fabric 独立群集配置。 群集配置包含群集的属性，包括群集上的不同节点类型、安全配置、错误和升级域拓扑，等等。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --configuration-api-version [必需] | 独立群集 json 配置的 API 版本。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值：60。 |

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值：json、jsonc、table、tsv。  默认值：json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-sa-cluster-config-upgrade"></a>sfctl sa-cluster config-upgrade
开始升级 Service Fabric 独立群集的配置。

如果参数有效，则验证提供的配置升级参数并开始升级群集配置。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --cluster-config            [必需] | 群集配置。 |
| --delta-unhealthy-nodes | 升级过程中允许的增量运行状况降级最大百分比。 允许的值为 0 到 100 的整数值。 |
| --health-check-retry | 应用程序或群集不正常时尝试执行运行状况检查所间隔的时间长度。  默认值：PT0H0M0S。 |
| --health-check-stable | 应用程序或群集必须保持正常状态的时间长度。  默认值：PT0H0M0S。 |
| --health-check-wait | 启动运行状况检查进程之前，完成升级域后等待的时间长度。  默认值：PT0H0M0S。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值：60。 |
| --unhealthy-applications | 升级过程中允许的不正常应用程序最大百分比。 允许的值为 0 到 100 的整数值。 |
| --unhealthy-nodes | 升级过程中允许的不正常节点最大百分比。 允许的值为 0 到 100 的整数值。 |
| --upgrade-domain-delta-unhealthy-nodes | 升级过程中允许的升级域增量运行状况降级最大百分比。 允许的值为 0 到 100 的整数值。 |
| --upgrade-domain-timeout | 升级域的超时。  默认值：PT0H0M0S。 |
| --upgrade-timeout | 升级超时。  默认值：PT0H0M0S。 |

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值：json、jsonc、table、tsv。  默认值：json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-sa-cluster-upgrade-status"></a>sfctl sa-cluster upgrade-status
获取 Service Fabric 独立群集的群集配置升级状态。

获取 Service Fabric 独立群集的群集配置升级状态详细信息。

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