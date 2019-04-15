---
title: Azure Service Fabric CLI - sfctl mesh code-package-log | Microsoft Docs
description: 介绍 Service Fabric CLI sfctl mesh code-package-log 命令。
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
ms.openlocfilehash: e7bc8491071946eaa2e322517e5d36d681a49130
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2019
ms.locfileid: "58661065"
---
# <a name="sfctl-mesh-code-package-log"></a>sfctl mesh code-package-log
获取给定服务副本的指定代码包的容器日志。

## <a name="commands"></a>命令

|命令|描述|
| --- | --- |
| get | 从容器中获取日志。 |

## <a name="sfctl-mesh-code-package-log-get"></a>sfctl mesh code-package-log get
从容器中获取日志。

获取服务副本的指定代码包的容器日志。

### <a name="arguments"></a>参数

|参数|描述|
| --- | --- |
| --app-name --application-name [必需] | 应用程序的名称。 |
| --code-package-name [必需] | 服务的代码包名称。 |
| --replica-name [必需] | Service Fabric 副本名称。 |
| --service-name [必需] | 服务的名称。 |
| --tail | 要从日志末尾显示的行数。 默认值为 100。 “all”表示显示完整日志。 |

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