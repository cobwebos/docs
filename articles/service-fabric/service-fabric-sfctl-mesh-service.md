---
title: Azure Service Fabric sfctl 网格服务
description: 了解 sfctl，Azure Service Fabric 命令行界面。 包含用于获取应用程序资源的服务详细信息的命令的列表。
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 2b9f16062b02d7a1a3403d396643a7989b8976b7
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76905925"
---
# <a name="sfctl-mesh-service"></a>sfctl mesh service
获取服务详细信息并列出应用程序资源的服务。

## <a name="commands"></a>命令

|命令|Description|
| --- | --- |
| list | 列出所有服务资源。 |
| show | 获取具有给定名称的服务资源。 |

## <a name="sfctl-mesh-service-list"></a>sfctl mesh service list
列出所有服务资源。

获取某个应用程序资源的所有服务的相关信息。 此信息包括服务的说明和其他属性。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --app-name --application-name [必需] | 应用程序的名称。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 获取完整的调试日志。 |

## <a name="sfctl-mesh-service-show"></a>sfctl mesh service show
获取具有给定名称的服务资源。

获取具有给定名称的服务资源的相关信息。 此信息包括服务的说明和其他属性。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --app-name --application-name [必需] | 应用程序的名称。 |
| --name -n [必需] | 服务的名称。 |

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