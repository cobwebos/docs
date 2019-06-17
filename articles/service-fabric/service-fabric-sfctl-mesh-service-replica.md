---
title: Azure Service Fabric CLI - sfctl mesh service-replica | Microsoft Docs
description: 介绍 Service Fabric CLI sfctl mesh service-replica 命令。
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
ms.openlocfilehash: 1e0955ef2a52a6313f0449b956229e03f0a5b5e8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "61038442"
---
# <a name="sfctl-mesh-service-replica"></a>sfctl mesh service-replica
获取副本详细信息并列出应用程序资源中给定服务的副本。

## <a name="commands"></a>命令

|命令|描述|
| --- | --- |
| list | 列出某个服务的所有副本。 |
| show | 获取应用程序服务的给定副本。 |

## <a name="sfctl-mesh-service-replica-list"></a>sfctl mesh service-replica list
列出某个服务的所有副本。

获取某个服务的所有副本的相关信息。 此信息包括服务副本的说明和其他属性。

### <a name="arguments"></a>参数

|参数|描述|
| --- | --- |
| --app-name --application-name [必需] | 应用程序的名称。 |
| --service-name [必需] | 服务的名称。 |

### <a name="global-arguments"></a>全局参数

|参数|描述|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整调试日志。 |

## <a name="sfctl-mesh-service-replica-show"></a>sfctl mesh service-replica show
获取应用程序服务的给定副本。

获取具有给定名称的服务副本的相关信息。 此信息包括服务副本的说明和其他属性。

### <a name="arguments"></a>参数

|参数|描述|
| --- | --- |
| --app-name --application-name [必需] | 应用程序的名称。 |
| --name -n [必需] | 服务副本的名称。 |
| --service-name [必需] | 服务的名称。 |

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