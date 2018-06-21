---
title: Azure Service Fabric CLI - sfctl container | Microsoft Docs
description: 介绍 Service Fabric CLI sfctl container 命令。
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
ms.openlocfilehash: cd3725ac547a1ed1fd9207dc48ba3b6227e85ef1
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763924"
---
# <a name="sfctl-container"></a>sfctl container
在群集节点上运行与容器相关的命令。

## <a name="commands"></a>命令

|命令|说明|
| --- | --- |
| invoke-api | 调用容器 REST API。 |
| 日志 | 检索容器日志。 |

## <a name="sfctl-container-invoke-api"></a>sfctl container invoke-api
调用容器 REST API。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --application-id           [必需] | 应用程序标识。 |
| --code-package-instance-id [必需] | 代码包实例 ID，可以通过“service code-package-list”来检索。 |
| --code-package-name        [必需] | 代码包名称。 |
| --container-api-uri-path   [必需] | 容器 REST API URI 路径，使用“{ID}”代替容器名称/ID。 |
| --node-name                [必需] | 节点的名称。 |
| --service-manifest-name    [必需] | 服务清单名称。 |
| --container-api-body | 容器 REST API 的 HTTP 请求正文。 |
| --container-api-content-type | 容器 REST API 的内容类型，默认为“application/json”。 |
| --container-api-http-verb | 容器 REST API 的 HTTP 谓词，默认为 GET。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值：60。 |

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值：json、jsonc、table、tsv。  默认值：json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-container-logs"></a>sfctl container logs
检索容器日志。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --application-id           [必需] | 应用程序标识。 |
| --code-package-instance-id [必需] | 代码包实例 ID，可以通过“service code-package-list”来检索。 |
| --code-package-name        [必需] | 代码包名称。 |
| --node-name                [必需] | 节点的名称。 |
| --service-manifest-name    [必需] | 服务清单名称。 |
| --tail | 仅从日志的末尾返回此数量的日志行。 指定为整数，或指定为“all”以输出所有日志行。 默认为“all”。 |
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