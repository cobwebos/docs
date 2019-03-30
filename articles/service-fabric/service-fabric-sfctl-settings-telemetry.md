---
title: Azure Service Fabric CLI - sfctl settings telemetry | Microsoft Docs
description: 介绍 Service Fabric CLI sfctl settings telemetry 命令。
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
ms.openlocfilehash: 42a82ab0be37f260a48a1da6cecab5120c24d293
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2019
ms.locfileid: "58669259"
---
# <a name="sfctl-settings-telemetry"></a>sfctl settings telemetry
配置此 sfctl 实例的本地遥测设置。

Sfctl 遥测收集未提供参数的命令名称或其值、sfctl 版本、OS 类型、python 版本、命令是成功还是失败、返回的错误消息。

## <a name="commands"></a>命令

|命令|描述|
| --- | --- |
| set-telemetry | 启用或禁用遥测。 |

## <a name="sfctl-settings-telemetry-set-telemetry"></a>sfctl settings telemetry set-telemetry
启用或禁用遥测。

### <a name="arguments"></a>参数

|参数|描述|
| --- | --- |
| --off | 禁用遥测。 |
| --on | 启用遥测。 这是默认值。 |

### <a name="global-arguments"></a>全局参数

|参数|描述|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

### <a name="examples"></a>示例

禁用遥测。

```
sfctl settings telemetry set_telemetry --off
```

启用遥测。

```
sfctl settings telemetry set_telemetry --on
```


## <a name="next-steps"></a>后续步骤
- [安装](service-fabric-cli.md) Service Fabric CLI。
- 了解如何通过[示例脚本](/azure/service-fabric/scripts/sfctl-upgrade-application)使用 Service Fabric CLI。