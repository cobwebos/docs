---
title: Azure 数据工厂中的 Filter 活动 | Microsoft Docs
description: Filter 活动将筛选输入。
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/04/2018
ms.openlocfilehash: 5416a603fdf2cc5e21444e16560d662c9603f9d8
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2019
ms.locfileid: "70859090"
---
# <a name="filter-activity-in-azure-data-factory"></a>Azure 数据工厂中的 Filter 活动
可以在管道中使用 Filter 活动将筛选器表达式应用到输入数组。 

## <a name="syntax"></a>语法

```json
{
    "name": "MyFilterActivity",
    "type": "filter",
    "typeProperties": {
        "condition": "<condition>",
        "items": "<input array>"
    }
}
```

## <a name="type-properties"></a>Type 属性

属性 | 说明 | 允许的值 | 必填
-------- | ----------- | -------------- | --------
name | `Filter` 活动的名称。 | String | 是
type | 必须设置为“filter”。 | String | 是
condition | 要用于筛选输入的条件。 | 表达式 | 是
项目 | 应该应用筛选器的输入数组。 | 表达式 | 是

## <a name="example"></a>示例

在此示例中，管道包含两个活动：**Filter** 和 **ForEach**。 Filter 活动配置为筛选输入数组中值大于 3 的项。 然后，ForEach 活动会遍历筛选值并将变量**测试**设置为当前值。

```json
{
    "name": "PipelineName",
    "properties": {
        "activities": [{
                "name": "MyFilterActivity",
                "type": "filter",
                "typeProperties": {
                    "condition": "@greater(item(),3)",
                    "items": "@pipeline().parameters.inputs"
                }
            },
            {
            "name": "MyForEach",
            "type": "ForEach",
            "dependsOn": [
                {
                    "activity": "MyFilterActivity",
                    "dependencyConditions": [
                        "Succeeded"
                    ]
                }
            ],
            "userProperties": [],
            "typeProperties": {
                "items": {
                    "value": "@activity('MyFilterActivity').output.value",
                    "type": "Expression"
                },
                "isSequential": "false",
                "batchCount": 1,
                "activities": [
                    {
                        "name": "Set Variable1",
                        "type": "SetVariable",
                        "dependsOn": [],
                        "userProperties": [],
                        "typeProperties": {
                            "variableName": "test",
                            "value": {
                                "value": "@string(item())",
                                "type": "Expression"
                            }
                        }
                    }
                ]
            }
        }],
        "parameters": {
            "inputs": {
                "type": "Array",
                "defaultValue": [1, 2, 3, 4, 5, 6]
            }
        },

        "variables": {
            "test": {
                "type": "String"
            }
        },
        "annotations": []
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
- [Until 活动](control-flow-until-activity.md)
