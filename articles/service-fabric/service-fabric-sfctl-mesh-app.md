---
title: Azure Service Fabric CLI - sfctl mesh app | Microsoft Docs
description: 介绍 Service Fabric CLI sfctl mesh app 命令。
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
ms.openlocfilehash: 3c2194f6a001e4fc49dcf1694f8a9cda41550ace
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/12/2018
ms.locfileid: "53282963"
---
# <a name="sfctl-mesh-app"></a>sfctl mesh app
获取和删除应用程序资源。

## <a name="commands"></a>命令

|命令|Description|
| --- | --- |
| delete | 删除应用程序资源。 |
| list | 列出所有应用程序资源。 |
| show | 获取具有给定名称的应用程序资源。 |

## <a name="sfctl-mesh-app-delete"></a>sfctl mesh app delete
删除应用程序资源。

删除由名称标识的应用程序资源。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --name -n [必需] | 应用程序的名称。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-mesh-app-list"></a>sfctl mesh app list
列出所有应用程序资源。

获取给定资源组中所有应用程序资源的相关信息。 此信息包括应用程序的说明和其他属性。

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-mesh-app-show"></a>sfctl mesh app show
获取具有给定名称的应用程序资源。

获取具有给定名称的应用程序资源的相关信息。 此信息包括应用程序的说明和其他属性。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --name -n [必需] | 应用程序的名称。 |

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