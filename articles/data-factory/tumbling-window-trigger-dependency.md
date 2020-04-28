---
title: 创建翻转窗口触发器依赖项
description: 了解如何在 Azure 数据工厂中创建翻转窗口触发器上的依赖项。
services: data-factory
ms.author: daperlov
author: djpmsft
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/29/2019
ms.openlocfilehash: 39ea8dda0fd823d3061b2cb29e1c548f99281c82
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81418790"
---
# <a name="create-a-tumbling-window-trigger-dependency"></a>创建翻转窗口触发器依赖项
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文提供创建翻转窗口触发器上的依赖项的步骤。 有关翻转窗口触发器的一般信息，请参阅[如何创建翻转窗口触发器](how-to-create-tumbling-window-trigger.md)。

若要生成依赖关系链，并确保某个触发器只在成功执行数据工厂中的另一个触发器之后才执行，请使用此高级功能创建翻转窗口依赖项。

## <a name="create-a-dependency-in-the-data-factory-ui"></a>在数据工厂 UI 中创建依赖项

若要在触发器上创建依赖项，请选择“触发器”>“高级”>“新建”，然后选择要依赖的、具有相应偏移量和大小的触发器。  选择“完成”并发布数据工厂更改，使依赖项生效。 

![依赖项创建](media/tumbling-window-trigger-dependency/tumbling-window-dependency01.png "依赖项创建")

## <a name="tumbling-window-dependency-properties"></a>翻转窗口依赖项属性

带有依赖项的翻转窗口触发器具有以下属性：

```json
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
        "runtimeState": <<Started/Stopped/Disabled - readonly>>,
        "typeProperties": {
            "frequency": <<Minute/Hour>>,
            "interval": <<int>>,
            "startTime": <<datetime>>,
            "endTime": <<datetime – optional>>,
            "delay": <<timespan – optional>>,
            "maxConcurrency": <<int>> (required, max allowed: 50),
            "retryPolicy": {
                "count": <<int - optional, default: 0>>,
                "intervalInSeconds": <<int>>,
            },
            "dependsOn": [
                {
                    "type": "TumblingWindowTriggerDependencyReference",
                    "size": <<timespan – optional>>,
                    "offset": <<timespan – optional>>,
                    "referenceTrigger": {
                        "referenceName": "MyTumblingWindowDependency1",
                        "type": "TriggerReference"
                    }
                },
                {
                    "type": "SelfDependencyTumblingWindowTriggerReference",
                    "size": <<timespan – optional>>,
                    "offset": <<timespan>>
                }
            ]
        }
    }
}
```

下表提供了定义翻转窗口依赖项所需的属性列表。

| **属性名称** | **说明**  | **类型** | **必需** |
|---|---|---|---|
| type  | 所有现有翻转窗口触发器将显示在此下拉列表中。 选择要依赖的触发器。  | TumblingWindowTriggerDependencyReference 或 SelfDependencyTumblingWindowTriggerReference | 是 |
| offset | 依赖项触发器的偏移量。 以时间跨度格式提供值，负数和正数偏移量均可。 如果触发器依赖于自身，则此属性是必需的；对于其他所有情况，此属性是可选的。 自我依赖项应始终为负偏移量。 如果未指定任何值，则该窗口与触发器本身相同。 | Timespan<br/>(hh:mm:ss) | 自我依赖项：是<br/>其他：否 |
| 大小 | 依赖项翻转窗口的大小。 提供正的 timespan 值。 此属性是可选的。 | Timespan<br/>(hh:mm:ss) | 否  |

> [!NOTE]
> 翻转窗口触发器最多可以依赖于两个其他触发器。

## <a name="tumbling-window-self-dependency-properties"></a>翻转窗口自我依赖项属性

对于在上一个窗口成功完成之前触发器不应转到下一个窗口的情况，请生成自我依赖项。 依赖于前一小时内自身运行是否成功的自我依赖项触发器将具有以下属性：

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
## <a name="usage-scenarios-and-examples"></a>使用方案和示例

下面演示了翻转窗口依赖项属性的方案和用法。

### <a name="dependency-offset"></a>依赖项偏移量

![偏移量示例](media/tumbling-window-trigger-dependency/tumbling-window-dependency02.png "偏移量示例")

### <a name="dependency-size"></a>依赖项大小

![大小示例](media/tumbling-window-trigger-dependency/tumbling-window-dependency03.png "大小示例")

### <a name="self-dependency"></a>自我依赖项

![自我依赖项](media/tumbling-window-trigger-dependency/tumbling-window-dependency04.png "自我依赖项")

### <a name="dependency-on-another-tumbling-window-trigger"></a>依赖于另一个翻转窗口触发器

某个每日遥测数据处理作业依赖于另一个每日作业，这后一个作业聚合过去七天的输出，并生成七天滚动窗口流：

![依赖项示例](media/tumbling-window-trigger-dependency/tumbling-window-dependency05.png "依赖项示例")

### <a name="dependency-on-itself"></a>依赖于自身

在作业输出流中不存在间隙的每日作业：

![自我依赖项示例](media/tumbling-window-trigger-dependency/tumbling-window-dependency06.png "自我依赖项示例")

有关如何使用翻转窗口触发器在 Azure 数据工厂中创建从属管道的演示，请观看以下视频：

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Create-dependent-pipelines-in-your-Azure-Data-Factory/player]

## <a name="monitor-dependencies"></a>监视依赖项

可以从“触发器运行监视”页面监视依赖链和相应的窗口。 导航到“监视”>“触发器运行”。**** 在“操作”列下，可以重新运行触发器或查看其依赖项。

![监视触发器运行](media/tumbling-window-trigger-dependency/tumbling-window-dependency07.png "监视触发器运行")

如果单击“查看触发器依赖关系”，可以查看依赖项的状态。 如果某个依赖项触发器失败，则必须成功重新运行它才能使相关触发器可以运行。 翻转窗口触发器将在超时前等待依赖项七天。

![监视依赖项](media/tumbling-window-trigger-dependency/tumbling-window-dependency08.png "监视依赖项")

若要更直观地查看触发器依赖项计划，请选择甘特图视图。

![监视依赖项](media/tumbling-window-trigger-dependency/tumbling-window-dependency09.png "监视依赖项")

## <a name="next-steps"></a>后续步骤

* 查看[如何创建翻转窗口触发器](how-to-create-tumbling-window-trigger.md)
