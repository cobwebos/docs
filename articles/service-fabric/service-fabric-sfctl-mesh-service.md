---
title: Azure Service Fabric CLI - sfctl mesh service | Microsoft Docs
description: 介绍 Service Fabric CLI sfctl mesh service 命令。
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: 6afcb891de763f156705bc9825ab7575a272c1db
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035025"
---
# <a name="sfctl-mesh-service"></a>sfctl mesh service
获取服务详细信息并列出应用程序资源的服务。

## <a name="commands"></a>命令

|Command|描述|
| --- | --- |
| 列表 | 列出所有服务资源。 |
| show | 获取具有给定名称的服务资源。 |

## <a name="sfctl-mesh-service-list"></a>sfctl mesh service list
列出所有服务资源。

获取某个应用程序资源的所有服务的相关信息。 此信息包括服务的说明和其他属性。

### <a name="arguments"></a>参数

|参数|描述|
| --- | --- |
| --app-name --application-name [必需] | 应用程序的名称。 |

### <a name="global-arguments"></a>全局参数

|参数|描述|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整调试日志。 |

## <a name="sfctl-mesh-service-show"></a>sfctl mesh service show
获取具有给定名称的服务资源。

获取具有给定名称的服务资源的相关信息。 此信息包括服务的说明和其他属性。

### <a name="arguments"></a>参数

|参数|描述|
| --- | --- |
| --app-name --application-name [必需] | 应用程序的名称。 |
| --name -n [必需] | 服务的名称。 |

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