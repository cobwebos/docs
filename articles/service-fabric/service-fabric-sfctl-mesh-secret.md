---
title: Azure Service Fabric sfctl 网格机密
description: 了解 sfctl（Azure Service Fabric 命令行接口）。 包含用于获取和删除 Service Fabric 网格密钥资源的命令的列表。
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 2c91a59e57613934c946e3cd56089b330893a51a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86260336"
---
# <a name="sfctl-mesh-secret"></a>sfctl mesh secret
获取和删除网格机密资源。

## <a name="commands"></a>命令

|Command|描述|
| --- | --- |
| “删除” | 删除机密资源。 |
| list | 列出所有机密资源。 |
| 显示 | 获取具有给定名称的机密资源。 |

## <a name="sfctl-mesh-secret-delete"></a>sfctl mesh secret delete
删除机密资源。

删除指定的机密资源及其所有其命名值。

### <a name="arguments"></a>参数

|参数|描述|
| --- | --- |
| --name -n [必需] | 机密资源的名称。 |

### <a name="global-arguments"></a>全局参数

|参数|说明|
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

|参数|说明|
| --- | --- |
| --debug | 提高日志记录详细程度以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 获取完整的调试日志。 |

## <a name="sfctl-mesh-secret-show"></a>sfctl mesh secret show
获取具有给定名称的机密资源。

获取具有给定名称的机密资源的相关信息。 此信息包括机密的说明和其他属性。

### <a name="arguments"></a>参数

|参数|描述|
| --- | --- |
| --name -n [必需] | 机密资源的名称。 |

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug | 提高日志记录详细程度以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 获取完整的调试日志。 |


## <a name="next-steps"></a>后续步骤
- [安装](service-fabric-cli.md) Service Fabric CLI。
- 了解如何通过[示例脚本](./scripts/sfctl-upgrade-application.md)使用 Service Fabric CLI。
