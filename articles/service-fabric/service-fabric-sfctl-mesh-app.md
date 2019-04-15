---
title: Azure Service Fabric CLI - sfctl mesh app | Microsoft Docs
description: 介绍 Service Fabric CLI sfctl mesh app 命令。
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: fb812412c7dd07800c1e2231c9472a122ab7d7d4
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2019
ms.locfileid: "58661830"
---
# <a name="sfctl-mesh-app"></a>sfctl mesh app
获取和删除应用程序资源。

## <a name="commands"></a>命令

|命令|描述|
| --- | --- |
| delete | 删除应用程序资源。 |
| list | 列出所有应用程序资源。 |
| show | 获取具有给定名称的应用程序资源。 |

## <a name="sfctl-mesh-app-delete"></a>sfctl mesh app delete
删除应用程序资源。

删除由名称标识的应用程序资源。

### <a name="arguments"></a>参数

|参数|描述|
| --- | --- |
| --name -n [必需] | 应用程序的名称。 |

### <a name="global-arguments"></a>全局参数

|参数|描述|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整调试日志。 |

## <a name="sfctl-mesh-app-list"></a>sfctl mesh app list
列出所有应用程序资源。

获取给定资源组中所有应用程序资源的相关信息。 此信息包括应用程序的说明和其他属性。

### <a name="global-arguments"></a>全局参数

|参数|描述|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整调试日志。 |

## <a name="sfctl-mesh-app-show"></a>sfctl mesh app show
获取具有给定名称的应用程序资源。

获取具有给定名称的应用程序资源的相关信息。 此信息包括应用程序的说明和其他属性。

### <a name="arguments"></a>参数

|参数|描述|
| --- | --- |
| --name -n [必需] | 应用程序的名称。 |

### <a name="global-arguments"></a>全局参数

|参数|描述|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整调试日志。 |


## <a name="next-steps"></a>后续步骤
- [安装](service-fabric-cli.md) Service Fabric CLI。
- 了解如何通过[示例脚本](/azure/service-fabric/scripts/sfctl-upgrade-application)使用 Service Fabric CLI。