---
title: Azure Service Fabric CLI- sfctl mesh secretvalue | Microsoft Docs
description: 介绍了 Service Fabric CLI sfctl mesh secretvalue 命令。
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
ms.openlocfilehash: b8172ba50f7fec8f0aa1ba768c05d087ecabf8c8
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/12/2018
ms.locfileid: "53283793"
---
# <a name="sfctl-mesh-secretvalue"></a>sfctl mesh secretvalue
获取和删除网格机密值资源。

## <a name="commands"></a>命令

|命令|说明|
| --- | --- |
| delete | 删除已命名机密资源的指定值。 |
| list | 列出指定的机密资源的所有值的名称。 |
| show | 检索机密资源的指定版本的值。 |

## <a name="sfctl-mesh-secretvalue-delete"></a>sfctl mesh secretvalue delete
删除已命名机密资源的指定值。

删除由名称标识的机密值资源。 资源的名称通常是与该值关联的版本。 如果指定的值在使用中，则删除将失败。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --secret-name -n [必需] | 机密资源的名称。 |
| --version -v     [必需] | 机密版本的名称。 |

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-mesh-secretvalue-list"></a>sfctl mesh secretvalue list
列出指定的机密资源的所有值的名称。

获取指定的机密资源的所有机密值资源的信息。 该信息包括机密值资源的名称，但不包括实际值。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --secret-name -n [必需] | 机密资源的名称。 |

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-mesh-secretvalue-show"></a>sfctl mesh secretvalue show
检索机密资源的指定版本的值。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --secret-name -n [必需] | 机密资源的名称。 |
| --version -v     [必需] | 机密版本的名称。 |
| --show-value | 显示机密版本的实际值。 |

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |


## <a name="next-steps"></a>后续步骤
- [安装](service-fabric-cli.md) Service Fabric CLI。
- 了解如何通过[示例脚本](/azure/service-fabric/scripts/sfctl-upgrade-application)使用 Service Fabric CLI。