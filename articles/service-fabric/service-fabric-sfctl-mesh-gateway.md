---
title: Azure Service Fabric sfctl 网格网关
description: 了解 sfctl，Azure Service Fabric 命令行界面。 包含用于获取和删除 Service Fabric 网格网关资源的命令的列表。
author: jeffj6123
ms.topic: reference
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: 86e63f88cf1412b54fad6f9e386a1a5052463917
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/03/2020
ms.locfileid: "75646154"
---
# <a name="sfctl-mesh-gateway"></a>sfctl mesh gateway
获取和删除网格网关资源。

## <a name="commands"></a>命令

|命令|Description|
| --- | --- |
| delete | 删除网关资源。 |
| list | 列出所有网关资源。 |
| show | 获取具有给定名称的网关资源。 |

## <a name="sfctl-mesh-gateway-delete"></a>sfctl mesh gateway delete
删除网关资源。

删除由名称标识的网关资源。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --name -n [必需] | 网关资源的名称。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 获取完整的调试日志。 |

## <a name="sfctl-mesh-gateway-list"></a>sfctl mesh gateway list
列出所有网关资源。

获取给定资源组中所有网关资源的相关信息。 此信息包括网关的说明和其他属性。

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 获取完整的调试日志。 |

## <a name="sfctl-mesh-gateway-show"></a>sfctl mesh gateway show
获取具有给定名称的网关资源。

获取具有给定名称的网关资源的相关信息。 此信息包括网关的说明和其他属性。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --name -n [必需] | 网关资源的名称。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 获取完整的调试日志。 |


## <a name="next-steps"></a>后续步骤
- [安装](service-fabric-cli.md) Service Fabric CLI。
- 了解如何通过[示例脚本](/azure/service-fabric/scripts/sfctl-upgrade-application)使用 Service Fabric CLI。