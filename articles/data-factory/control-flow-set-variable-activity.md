---
title: Azure 数据工厂中的设置变量活动
description: 了解如何使用“设置变量”活动来设置数据工厂管道中定义的现有变量的值
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/07/2020
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.openlocfilehash: e5bd3d10e4e43daf3031aae5083ee917cfe65ede
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81417974"
---
# <a name="set-variable-activity-in-azure-data-factory"></a>Azure 数据工厂中的设置变量活动
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

使用“设置变量”活动可设置数据工厂管道中定义的 String、Bool 或 Array 类型的现有变量的值。

## <a name="type-properties"></a>Type 属性

properties | 说明 | 必选
-------- | ----------- | --------
name | 管道中活动的名称 | 是
description | 描述活动用途的文本 | 否
type | 必须设置为**SetVariable** | 是
值 | 变量将分配到的字符串文本或表达式对象值 | 是
variableName | 此活动将设置的变量的名称 | 是

## <a name="incrementing-a-variable"></a>递增变量

涉及 Azure 数据工厂中的变量的常见方案是使用变量作为 until 或 foreach 活动中的迭代器。 在 " `value`集变量" 活动中，无法引用字段中设置的变量。 若要解决此限制，请设置临时变量，然后创建第二个集变量活动。 第二个 "设置变量" 活动将迭代器的值设置为临时变量。 

下面是此模式的示例：

![递增变量](media/control-flow-set-variable-activity/increment-variable.png "递增变量")

``` json
{
    "name": "pipeline3",
    "properties": {
        "activities": [
            {
                "name": "Set I",
                "type": "SetVariable",
                "dependsOn": [
                    {
                        "activity": "Increment J",
                        "dependencyConditions": [
                            "Succeeded"
                        ]
                    }
                ],
                "userProperties": [],
                "typeProperties": {
                    "variableName": "i",
                    "value": {
                        "value": "@variables('j')",
                        "type": "Expression"
                    }
                }
            },
            {
                "name": "Increment J",
                "type": "SetVariable",
                "dependsOn": [],
                "userProperties": [],
                "typeProperties": {
                    "variableName": "j",
                    "value": {
                        "value": "@string(add(int(variables('i')), 1))",
                        "type": "Expression"
                    }
                }
            }
        ],
        "variables": {
            "i": {
                "type": "String",
                "defaultValue": "0"
            },
            "j": {
                "type": "String",
                "defaultValue": "0"
            }
        },
        "annotations": []
    }
}
```


## <a name="next-steps"></a>后续步骤
了解数据工厂支持的相关控制流活动： 

- [追加变量活动](control-flow-append-variable-activity.md)
