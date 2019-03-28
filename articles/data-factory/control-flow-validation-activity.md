---
title: Azure 数据工厂中的验证活动 |Microsoft Docs
description: 验证活动不会继续执行管道，直到它验证了用户指定特定条件的附加数据集。
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: shlo
ms.openlocfilehash: 46447bdbea93d1f99c5682cf878c2035e6f49b78
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2019
ms.locfileid: "58522891"
---
# <a name="validation-activity-in-azure-data-factory"></a>Azure 数据工厂中的验证活动
可以在管道中使用验证以确保管道仅继续执行，一旦其已经验证附加数据集的引用存在，它满足指定的条件时，或达到超时。


## <a name="syntax"></a>语法

```json

{
    "name": "Validation_Activity",
    "type": "Validation",
    "typeProperties": {
        "dataset": {
            "referenceName": "Storage_File",
            "type": "DatasetReference"
        },
        "timeout": "7.00:00:00",
        "sleep": 10,
        "minimumSize": 20
    }
},
{
    "name": "Validation_Activity_Folder",
    "type": "Validation",
    "typeProperties": {
        "dataset": {
            "referenceName": "Storage_Folder",
            "type": "DatasetReference"
        },
        "timeout": "7.00:00:00",
        "sleep": 10,
        "childItems": true
    }
}

```


## <a name="type-properties"></a>Type 属性

属性 | 说明 | 允许的值 | 需要
-------- | ----------- | -------------- | --------
名称 | 验证活动的名称 | String | 是 |
类型 | 必须设置为**验证**。 | String | 是 |
dataset | 活动将阻止执行，直到其已经验证此数据集引用存在，并且满足指定的条件时，或达到超时。 提供数据集应支持"MinimumSize"或"ChildItems"属性。 | 数据集引用 | 是 |
超时 | 指定活动运行的超时。 如果未不指定任何值，默认值为 7 天 ("7.00:00:00")。 格式是 d.hh:mm:ss | String | 否 |
进入睡眠状态 | 以秒为单位验证尝试之间延迟。 如果未不指定任何值，默认值为 10 秒。 | 整数 | 否 |
childItems | 检查文件夹是否包含子项目。 可以设置为 true:验证该文件夹存在并且它具有项。 阻止，直到至少一项是文件夹中存在或达到超时值。-false:验证该文件夹存在并且为空。 达到受到阻止，直到文件夹为空或直到超时值。 如果未不指定任何值，直到该文件夹存在，或达到超时，将阻止活动。 | Boolean | 否 |
minimumSize | 以字节为单位的文件的最小大小。 如果未不指定任何值，默认值为 0 字节 | 整数 | 否 |


## <a name="next-steps"></a>后续步骤
查看数据工厂支持的其他控制流活动：

- [If Condition 活动](control-flow-if-condition-activity.md)
- [Execute Pipeline 活动](control-flow-execute-pipeline-activity.md)
- [For Each 活动](control-flow-for-each-activity.md)
- [Get Metadata 活动](control-flow-get-metadata-activity.md)
- [Lookup 活动](control-flow-lookup-activity.md)
- [Web 活动](control-flow-web-activity.md)
- [Until 活动](control-flow-until-activity.md)
