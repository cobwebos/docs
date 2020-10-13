---
title: Azure Service Fabric CLI - sfctl chaos schedule
description: 了解 sfctl（Azure Service Fabric 命令行接口）。 包含用于混沌测试计划的命令列表。
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: dd911574cd37c18e3043cfc2482bd1fcd5f3857b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86260853"
---
# <a name="sfctl-chaos-schedule"></a>sfctl chaos schedule
获取和设置 Chaos Schedule。

## <a name="commands"></a>命令

|Command|说明|
| --- | --- |
| get | 获取定义何时以及如何运行 Chaos 的 Chaos Schedule。 |
| set | 设置 Chaos 使用的计划。 |

## <a name="sfctl-chaos-schedule-get"></a>sfctl chaos schedule get
获取定义何时以及如何运行 Chaos 的 Chaos Schedule。

获取正在使用的 Chaos Schedule 版本以及定义何时以及如何运行 Chaos 的 Chaos Schedule。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --timeout -t | 执行操作的服务器超时，以秒为单位。 此超时指定客户端可以等待请求的操作完成的持续时间。 此参数的默认值为 60 秒。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug | 提高日志记录详细程度以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 获取完整的调试日志。 |

## <a name="sfctl-chaos-schedule-set"></a>sfctl chaos schedule set
设置 Chaos 使用的计划。

Chaos 将自动基于 Chaos Schedule 来计划运行。 如果提供的版本与服务器上的版本匹配，则将更新 Chaos Schedule。 更新 Chaos Schedule 时，服务器上的版本将递增 1。 在达到较大数字后，服务器上的版本将自动换回 0。 如果在进行此调用时正在运行 Chaos，则此调用将失败。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --chaos-parameters-dictionary | 一个 JSON 编码列表，表示字符串名称到 Jobs 要使用的 ChaosParameters 的映射。 |
| --expiry-date-utc | 停止使用 Schedule 来计划 Chaos 的日期和时间。  默认值\: 9999-12-31T23\:59\:59.999Z。 |
| --jobs | ChaosScheduleJobs 的 JSON 编码列表，表示何时运行 Chaos 以及使用哪些参数运行 Chaos。 |
| --start-date-utc | 开始使用 Schedule 来计划 Chaos 的日期和时间。  默认值\: 1601-01-01T00\:00\:00.000Z。 |
| --timeout -t | 默认值\: 60。 |
| --version | Schedule 的版本号。 |

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug | 提高日志记录详细程度以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 获取完整的调试日志。 |

### <a name="examples"></a>示例

以下命令设置一个计划（假定当前计划的版本为 0），该计划于 2016 年 1 月 1 日开始，于 2038 年 1 月 1 日过期，每周 7 天、每天 24 小时运行 Chaos。
Chaos 将被计划于该时间内在群集上运行。
```
sfctl chaos schedule set --version 0 --start-date-utc "2016-01-01T00:00:00.000Z" --expiry-date-utc "2038-01-01T00:00:00.000Z"
    --chaos-parameters-dictionary
    [
    {
        "Key":"adhoc",
        "Value":{
            "MaxConcurrentFaults":3,
            "EnableMoveReplicaFaults":true,
            "ChaosTargetFilter":{
                "NodeTypeInclusionList":[
                "N0010Ref",
                "N0020Ref",
                "N0030Ref",
                "N0040Ref",
                "N0050Ref"
                ]
            },
            "MaxClusterStabilizationTimeoutInSeconds":60,
            "WaitTimeBetweenIterationsInSeconds":15,
            "WaitTimeBetweenFaultsInSeconds":30,
            "TimeToRunInSeconds":"600",
            "Context":{
                "Map":{
                "test":"value"
                }
            },
            "ClusterHealthPolicy":{
                "MaxPercentUnhealthyNodes":0,
                "ConsiderWarningAsError":true,
                "MaxPercentUnhealthyApplications":0
            }
        }
    }
    ]
    --jobs
    [
    {
        "ChaosParameters":"adhoc",
        "Days":{
            "Sunday":true,
            "Monday":true,
            "Tuesday":true,
            "Wednesday":true,
            "Thursday":true,
            "Friday":true,
            "Saturday":true
        },
        "Times":[
            {
                "StartTime":{
                "Hour":0,
                "Minute":0
                },
                "EndTime":{
                "Hour":23,
                "Minute":59
                }
            }
        ]
    }
    ]
```



## <a name="next-steps"></a>后续步骤
- [安装](service-fabric-cli.md) Service Fabric CLI。
- 了解如何通过[示例脚本](./scripts/sfctl-upgrade-application.md)使用 Service Fabric CLI。
