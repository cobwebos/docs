---
title: Azure Service Fabric CLI- sfctl settings telemetry
description: 了解 sfctl（Azure Service Fabric 命令行接口）。 包含用于配置 sfctl 遥测的命令列表。
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: ef720a14617b4131474d50875701d0ef27df4151
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86245495"
---
# <a name="sfctl-settings-telemetry"></a>sfctl settings telemetry
配置此 sfctl 实例的本地遥测设置。

Sfctl 遥测收集未提供参数的命令名称或其值、sfctl 版本、OS 类型、python 版本、命令是成功还是失败、返回的错误消息。

## <a name="commands"></a>命令

|Command|说明|
| --- | --- |
| set-telemetry | 启用或禁用遥测。 |

## <a name="sfctl-settings-telemetry-set-telemetry"></a>sfctl settings telemetry set-telemetry
启用或禁用遥测。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --off | 禁用遥测。 |
| --on | 启用遥测。 这是默认值。 |

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug | 提高日志记录详细程度以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 获取完整的调试日志。 |

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
- 了解如何通过[示例脚本](./scripts/sfctl-upgrade-application.md)使用 Service Fabric CLI。
