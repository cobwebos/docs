---
title: 在 Azure 数据工厂中创建翻转窗口触发器依赖项 | Microsoft Docs
description: 了解如何在 Azure 数据工厂中创建翻转窗口触发器上的依赖项。
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/11/2018
ms.author: douglasl
ms.openlocfilehash: c51c1056869cbd7741fae2ed1a554a7c794d1a39
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55966351"
---
# <a name="create-a-tumbling-window-trigger-dependency"></a>创建翻转窗口触发器依赖项

本文提供创建翻转窗口触发器上的依赖项的步骤。 有关翻转窗口触发器的一般信息，请参阅[如何创建翻转窗口触发器](how-to-create-tumbling-window-trigger.md)。

若要生成依赖关系链，并确保某个触发器只在成功执行数据工厂中的另一个触发器之后才执行，请使用此高级功能创建翻转窗口依赖项。

## <a name="create-a-dependency-in-the-data-factory-ui"></a>在数据工厂 UI 中创建依赖项

若要在触发器上创建依赖项，请选择“触发器”>“高级”>“新建”，然后选择要依赖的、具有相应偏移量和大小的触发器。 选择“完成”并发布数据工厂更改，使依赖项生效。

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency01.png)

## <a name="tumbling-window-dependency-properties"></a>翻转窗口依赖项属性

翻转窗口触发器依赖项具有以下属性：

```json
{
    "name": "DemoTrigger",
    "properties": {
        "runtimeState": "Started",
        "pipeline": {
            "pipelineReference": {
                "referenceName": "Demo",
                "type": "PipelineReference"
            }
        },
        "type": "TumblingWindowTrigger",
        "typeProperties": {
            "frequency": "Hour",
            "interval": 1,
            "startTime": "2018-10-04T00:00:00.000Z",
            "delay": "00:00:00",
            "maxConcurrency": 50,
            "retryPolicy": {
                "intervalInSeconds": 30
            },
            "dependsOn": [
                {
                    "type": "TumblingWindowTriggerDependencyReference",
                    "size": "-02:00:00",
                    "offset": "-01:00:00",
                    "referenceTrigger": {
                        "referenceName": "DemoDependency1",
                        "type": "TriggerReference"
                    }
                }
            ]
        }
    }
}
```

下表提供了定义翻转窗口依赖项所需的属性列表。

| **属性名称** | **说明**  | 类型 | **必需** |
|---|---|---|---|
| 触发器  | 所有现有翻转窗口触发器将显示在此下拉列表中。 选择要依赖的触发器。  | TumblingWindowTrigger | 是 |
| Offset | 依赖项触发器的偏移量。 以时间跨度格式提供值，允许使用负数和正数偏移量。 如果触发器依赖于自身，则此参数是必需的；对于其他所有情况，此参数是可选的。 自我依赖项应始终为负偏移量。 | Timespan | 自我依赖项：是；其他情况：否 |
| 窗口大小 | 依赖项翻转窗口的大小。 请以时间跨度格式提供值。 此参数是可选的。 | Timespan | 否  |
|||||

## <a name="tumbling-window-self-dependency-properties"></a>翻转窗口自我依赖项属性

如果在前一个窗口成功完成之前触发器不能转到下一个窗口，请生成自我依赖项。 自我依赖项触发器具有以下属性：

```json
{
    "name": "DemoSelfDependency",
    "properties": {
        "runtimeState": "Started",
        "pipeline": {
            "pipelineReference": {
                "referenceName": "Demo",
                "type": "PipelineReference"
            }
        },
        "type": "TumblingWindowTrigger",
        "typeProperties": {
            "frequency": "Hour",
            "interval": 1,
            "startTime": "2018-10-04T00:00:00Z",
            "delay": "00:01:00",
            "maxConcurrency": 50,
            "retryPolicy": {
                "intervalInSeconds": 30
            },
            "dependsOn": [
                {
                    "type": "SelfDependencyTumblingWindowTriggerReference",
                    "size": "01:00:00",
                    "offset": "-01:00:00"
                }
            ]
        }
    }
}
```

下面演示了翻转窗口依赖项属性的方案和用法。

## <a name="dependency-offset"></a>依赖项偏移量

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency02.png)

## <a name="dependency-size"></a>依赖项大小

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency03.png)

## <a name="self-dependency"></a>自我依赖项

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency04.png)

## <a name="usage-scenarios"></a>使用方案

### <a name="dependency-on-another-tumbling-window-trigger"></a>依赖于另一个翻转窗口触发器

例如，某个每日遥测数据处理作业依赖于另一个每日作业，这后一个作业聚合过去七天的输出，并生成七天滚动窗口流：

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency05.png)

### <a name="dependency-on-itself"></a>依赖于自身

在作业输出流中不存在间隙的每日作业：

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency06.png)

## <a name="monitor-dependencies"></a>监视依赖项

可以通过触发器运行监视页监视依赖关系链和相应的窗口。 导航到“监视”>“触发器运行”。

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency07.png)

单击放大镜图标查看选定窗口的所有依赖触发器运行。

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency08.png)

## <a name="next-steps"></a>后续步骤

查看[如何创建翻转窗口触发器](how-to-create-tumbling-window-trigger.md)。