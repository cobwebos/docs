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
ms.openlocfilehash: e736cc95628bd0e15bdb7ffd425608278788c353
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879253"
---
# <a name="set-variable-activity-in-azure-data-factory"></a>在 Azure 数据工厂中设置变量活动

使用“设置变量”活动可设置数据工厂管道中定义的 String、Bool 或 Array 类型的现有变量的值。

## <a name="type-properties"></a>Type 属性

Property | 说明 | 必选
-------- | ----------- | --------
name | 管道中活动的名称 | 是
description | 描述活动用途的文本 | 否
type | 必须设置为 **"设置变量"** | 是
值 | 将变量分配给的字符串文本或表达式对象值 | 是
variableName | 此活动将设置的变量的名称 | 是

## <a name="incrementing-a-variable"></a>增加变量

Azure 数据工厂中涉及变量的常见方案是在每个活动之前或每个活动中使用变量作为迭代器。 在集变量活动中，不能引用字段中正在设置的`value`变量。 要解决此限制，请设置临时变量，然后创建第二个集变量活动。 第二组变量活动将迭代器的值设置为临时变量。 

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
