---
title: Azure 数据工厂中的等待活动
description: 等待活动在一段指定的时间内暂停管道的执行。
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/12/2018
ms.openlocfilehash: f9dd53fded06eec169219d00993620a0f2aa2bf0
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73678240"
---
# <a name="execute-wait-activity-in-azure-data-factory"></a>在 Azure 数据工厂中执行等待活动
在管道中使用等待活动时，管道将等待一段指定的时间，然后继续执行后续活动。 

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
name | `Wait` 活动的名称。 | String | 是
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
请参阅数据工厂支持的其他控制流活动： 

- [If Condition 活动](control-flow-if-condition-activity.md)
- [Execute Pipeline 活动](control-flow-execute-pipeline-activity.md)
- [For Each 活动](control-flow-for-each-activity.md)
- [Get Metadata 活动](control-flow-get-metadata-activity.md)
- [Lookup 活动](control-flow-lookup-activity.md)
- [Web 活动](control-flow-web-activity.md)
- [Until 活动](control-flow-until-activity.md)
