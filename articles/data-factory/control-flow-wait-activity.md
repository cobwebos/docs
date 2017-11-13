---
title: "Azure 数据工厂中的等待活动 | Microsoft Docs"
description: "等待活动在一段指定的时间内暂停管道的执行。"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2017
ms.author: shlo
ms.openlocfilehash: 892e61397bb6e54781665f210434a4ab9ccb2632
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2017
---
# <a name="wait-activity-in-azure-data-factory"></a>Azure 数据工厂中的等待活动
在管道中使用等待活动时，管道将等待一段指定的时间，然后继续执行后续活动。 

> [!NOTE]
> 本文适用于目前处于预览状态的数据工厂版本 2。 如果使用数据工厂服务版本 1（即正式版 (GA），请参阅[数据工厂 V1 文档](v1/data-factory-introduction.md)。

## <a name="syntax"></a>语法

```json
{
    "name": "MyWaitActivity",
    "type": "Wait",
    "typeProperties": {
        "waitTimeInSeconds": 1
    }
}

```

## <a name="type-properties"></a>Type 属性

属性 | 说明 | 允许的值 | 必选
-------- | ----------- | -------------- | --------
名称 | `Wait` 活动的名称。 | String | 是
type | 必须被设置为 **Wait**。 | String | 是
waitTimeInSeconds | 管道在继续进行处理前所等待的秒数。 | Integer | 是

## <a name="example"></a>示例

> [!NOTE]
> 本部分提供运行管道的 JSON 定义和示例 PowerShell 命令。 有关使用 Azure PowerShell 和 JSON 定义创建数据工厂管道的分步说明演练，请参阅[教程：使用 Azure PowerShell 创建数据工厂](quickstart-create-data-factory-powershell.md)。

### <a name="pipeline-with-wait-activity"></a>等待活动的管道
在此示例中，管道具有两个活动：**Until** 和 **Wait**。 等待活动被配置为等待 1 秒。 管道循环运行 Web 活动，在每次运行之间等待 1 秒。 

```json
{
    "name": "DoUntilPipeline",
    "properties": {
        "activities": [
            {
                "type": "Until",
                "typeProperties": {
                    "expression": {
                        "value": "@equals('Failed', coalesce(body('MyUnauthenticatedActivity')?.status, actions('MyUnauthenticatedActivity')?.status, 'null'))",
                        "type": "Expression"
                    },
                    "timeout": "00:00:01",
                    "activities": [
                        {
                            "name": "MyUnauthenticatedActivity",
                            "type": "WebActivity",
                            "typeProperties": {
                                "method": "get",
                                "url": "https://www.fake.com/",
                                "headers": {
                                    "Content-Type": "application/json"
                                }
                            },
                            "dependsOn": [
                                {
                                    "activity": "MyWaitActivity",
                                    "dependencyConditions": [ "Succeeded" ]
                                }
                            ]
                        },
                        {
                            "type": "Wait",
                            "typeProperties": {
                                "waitTimeInSeconds": 1
                            },
                            "name": "MyWaitActivity"
                        }
                    ]
                },
                "name": "MyUntilActivity"
            }
        ]
    }
}

```

## <a name="next-steps"></a>后续步骤
查看数据工厂支持的其他控制流活动： 

- [If Condition 活动](control-flow-if-condition-activity.md)
- [Execute Pipeline 活动](control-flow-execute-pipeline-activity.md)
- [For Each 活动](control-flow-for-each-activity.md)
- [Get Metadata 活动](control-flow-get-metadata-activity.md)
- [Lookup 活动](control-flow-lookup-activity.md)
- [Web 活动](control-flow-web-activity.md)
- [Unit 活动](control-flow-until-activity.md)