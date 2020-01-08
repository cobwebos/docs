---
title: Azure Service Fabric sfctl 容器
description: 了解 sfctl，Azure Service Fabric 命令行界面。 包含容器命令的列表。
author: jeffj6123
ms.topic: reference
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: 316715e65af07176b3dc276d47979a5fa3dc1d6f
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/03/2020
ms.locfileid: "75639133"
---
# <a name="sfctl-container"></a>sfctl container
在群集节点上运行与容器相关的命令。

## <a name="commands"></a>命令

|命令|Description|
| --- | --- |
| invoke-api | 在 Service Fabric 节点上部署的容器上调用给定代码包的容器 API。 |
| 日志 | 获取 Service Fabric 节点上部署的容器的容器日志。 |

## <a name="sfctl-container-invoke-api"></a>sfctl container invoke-api
在 Service Fabric 节点上部署的容器上调用给定代码包的容器 API。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --application-id           [必需] | 应用程序的标识。 <br><br> 这通常是不带“fabric\:”URI 方案的应用程序全名。 从版本 6.0 开始，分层名称以“\~”字符隔开。 例如，如果应用程序名称为“fabric\:/myapp/app1”，则 6.0 及更高版本中的应用程序标识为“myapp\~app1”，在以前的版本中为“myapp/app1”。 |
| --code-package-instance-id [必需] | 唯一标识 Service Fabric 节点上部署的代码包实例的 ID。 <br><br> 可以使用“service code-package-list”检索。 |
| --code-package-name        [必需] | 在 Service Fabric 群集中注册为应用程序类型一部分的服务清单中指定的代码包的名称。 |
| --container-api-uri-path   [必需] | 容器 REST API URI 路径，使用“{ID}”代替容器名称/ID。 |
| --node-name                [必需] | 节点的名称。 |
| --service-manifest-name    [必需] | 在 Service Fabric 群集中注册为应用程序类型一部分的服务清单的名称。 |
| --container-api-body | 容器 REST API 的 HTTP 请求正文。 |
| --container-api-content-type | 容器 REST API 的内容类型，默认为“application/json”。 |
| --container-api-http-verb | 容器 REST API 的 HTTP 谓词，默认为 GET。 |
| --timeout -t | 默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 获取完整的调试日志。 |

## <a name="sfctl-container-logs"></a>sfctl container logs
获取 Service Fabric 节点上部署的容器的容器日志。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --application-id           [必需] | 应用程序的标识。 <br><br> 这通常是不带“fabric\:”URI 方案的应用程序全名。 从版本 6.0 开始，分层名称以“\~”字符隔开。 例如，如果应用程序名称为“fabric\:/myapp/app1”，则 6.0 及更高版本中的应用程序标识为“myapp\~app1”，在以前的版本中为“myapp/app1”。 |
| --code-package-instance-id [必需] | 代码包实例 ID，可以通过“service code-package-list”来检索。 |
| --code-package-name        [必需] | 在 Service Fabric 群集中注册为应用程序类型一部分的服务清单中指定的代码包的名称。 |
| --node-name                [必需] | 节点的名称。 |
| --service-manifest-name    [必需] | 在 Service Fabric 群集中注册为应用程序类型一部分的服务清单的名称。 |
| --tail | 要从日志末尾显示的行数。 默认值为 100。 “all”表示显示完整日志。 |
| --timeout -t | 默认值\: 60。 |

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