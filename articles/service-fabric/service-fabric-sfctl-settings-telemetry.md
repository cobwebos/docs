---
title: Azure Service Fabric sfctl 设置遥测
description: 了解 sfctl，Azure Service Fabric 命令行界面。 包含用于配置 sfctl 遥测的命令的列表。
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 6af5fa944ef399756f9e890ddd77a7f5f32e2bfb
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76903025"
---
# <a name="sfctl-settings-telemetry"></a>sfctl 设置遥测
配置 sfctl 的此实例的本地遥测设置。

Sfctl 遥测收集未提供参数的命令名称或其值，Sfctl 版本，OS 类型，python 版本，返回的错误消息。

## <a name="commands"></a>命令

|Command|description|
| --- | --- |
| 设置-遥测 | 打开或关闭遥测。 |

## <a name="sfctl-settings-telemetry-set-telemetry"></a>sfctl 设置遥测设置-遥测
打开或关闭遥测。

### <a name="arguments"></a>参数

|参数|description|
| --- | --- |
| --off | 关闭遥测。 |
| --打开 | 打开遥测。 这是默认值。 |

### <a name="global-arguments"></a>全局参数

|参数|description|
| --- | --- |
| --调试 | 提高日志记录详细程度以显示所有调试日志。 |
| --help-h | 显示此帮助消息并退出。 |
| --output-o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用--debug 获取完整的调试日志。 |

### <a name="examples"></a>示例

关闭遥测。

```
sfctl settings telemetry set_telemetry --off
```

打开遥测。

```
sfctl settings telemetry set_telemetry --on
```


## <a name="next-steps"></a>后续步骤
- [设置](service-fabric-cli.md)Service Fabric CLI。
- 了解如何通过[示例脚本](/azure/service-fabric/scripts/sfctl-upgrade-application)使用 Service Fabric CLI。