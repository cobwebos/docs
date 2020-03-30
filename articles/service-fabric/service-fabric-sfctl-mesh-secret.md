---
title: Azure 服务结构 CLI-sfctl 网格机密
description: 了解 sfctl，Azure 服务结构命令行接口。 包括用于获取和删除服务结构网格机密资源的命令列表。
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: fab388ff223eb95020e2ba0945c76532bc54f224
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905981"
---
# <a name="sfctl-mesh-secret"></a>sfctl mesh secret
获取和删除网格机密资源。

## <a name="commands"></a>命令

|命令|描述|
| --- | --- |
| delete | 删除机密资源。 |
| list | 列出所有机密资源。 |
| show | 获取具有给定名称的机密资源。 |

## <a name="sfctl-mesh-secret-delete"></a>sfctl mesh secret delete
删除机密资源。

删除指定的机密资源及其所有其命名值。

### <a name="arguments"></a>自变量

|参数|描述|
| --- | --- |
| --name -n [必需] | 机密资源的名称。 |

### <a name="global-arguments"></a>全局参数

|参数|描述|
| --- | --- |
| --debug | 提高日志记录详细程度以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 获取完整的调试日志。 |

## <a name="sfctl-mesh-secret-list"></a>sfctl mesh secret list
列出所有机密资源。

获取给定资源组中所有机密资源的相关信息。 此信息包括机密的说明和其他属性。

### <a name="global-arguments"></a>全局参数

|参数|描述|
| --- | --- |
| --debug | 提高日志记录详细程度以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 获取完整的调试日志。 |

## <a name="sfctl-mesh-secret-show"></a>sfctl mesh secret show
获取具有给定名称的机密资源。

获取具有给定名称的机密资源的相关信息。 此信息包括机密的说明和其他属性。

### <a name="arguments"></a>自变量

|参数|描述|
| --- | --- |
| --name -n [必需] | 机密资源的名称。 |

### <a name="global-arguments"></a>全局参数

|参数|描述|
| --- | --- |
| --debug | 提高日志记录详细程度以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 获取完整的调试日志。 |


## <a name="next-steps"></a>后续步骤
- [设置](service-fabric-cli.md)服务结构 CLI。
- 了解如何通过[示例脚本](/azure/service-fabric/scripts/sfctl-upgrade-application)使用 Service Fabric CLI。