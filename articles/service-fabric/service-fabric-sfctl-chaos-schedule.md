---
title: Azure Service Fabric CLI- sfctl chaos schedule | Microsoft Docs
description: 介绍 Service Fabric CLI sfctl chaos schedule 命令。
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
ms.date: 07/31/2018
ms.author: bikang
ms.openlocfilehash: 176b04b9bb16b5f183298c75f16bceb5e885e293
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2018
ms.locfileid: "39492234"
---
# <a name="sfctl-chaos-schedule"></a>sfctl chaos schedule
获取和设置 Chaos Schedule。

## <a name="commands"></a>命令

|命令|Description|
| --- | --- |
| get | 获取定义何时以及如何运行 Chaos 的 Chaos Schedule。 |
| set | 设置要由 Chaos 使用的 Chaos Schedule。 |

## <a name="sfctl-chaos-schedule-get"></a>sfctl chaos schedule get
获取定义何时以及如何运行 Chaos 的 Chaos Schedule。

获取正在使用的 Chaos Schedule 版本以及定义何时以及如何运行 Chaos 的 Chaos Schedule。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

## <a name="sfctl-chaos-schedule-set"></a>sfctl chaos schedule set
设置要由 Chaos 使用的 Chaos Schedule。

设置当前正在由 Chaos 使用的 Chaos Schedule。 Chaos 将自动基于 Chaos Schedule 来计划运行。 提供的输入计划中的版本必须与服务器上的 Chaos Schedule 版本匹配。 如果提供的版本与服务器上的版本不匹配，则不会更新 Chaos Schedule。 如果提供的版本与服务器上的版本相匹配，则会更新 Chaos Schedule，服务器上的 Chaos Schedule 版本将递增 1 并将在达到 2,147,483,647 后回到 0。 如果在进行此调用时正在运行 Chaos，则此调用将失败。

### <a name="arguments"></a>参数

|参数|Description|
| --- | --- |
| --chaos-parameters-dictionary | 一个 JSON 编码列表，表示字符串名称到 Jobs 要使用的 ChaosParameters 的映射。 |
| --expiry-date-utc | 停止使用 Schedule 来计划 Chaos 的日期和时间。  默认值\: 9999-12-31T23\:59\:59.999Z。 |
| --jobs | ChaosScheduleJobs 的 JSON 编码列表，表示何时运行 Chaos 以及使用哪些参数运行 Chaos。 |
| --start-date-utc | 开始使用 Schedule 来计划 Chaos 的日期和时间。  默认值\: 1601-01-01T00\:00\:00.000Z。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |
| --version | Schedule 的版本号。 |

### <a name="global-arguments"></a>全局参数

|参数|Description|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。 |

### <a name="examples"></a>示例

以下命令设置一个计划（假定当前计划的版本为 0），该计划于 2016 年 1 月 1 日开始，于 2038 年 1 月 1 日过期，每周 7 天、每天 24 小时运行 Chaos。 Chaos 将被计划于该时间内在群集上运行。

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

## <a name="next-steps"></a>后续步骤
- [安装](service-fabric-cli.md) Service Fabric CLI。
- 了解如何通过[示例脚本](/azure/service-fabric/scripts/sfctl-upgrade-application)使用 Service Fabric CLI。