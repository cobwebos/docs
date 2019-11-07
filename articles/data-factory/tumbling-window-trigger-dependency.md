---
title: 在 Azure 数据工厂中创建翻转窗口触发器依赖项
description: 了解如何在 Azure 数据工厂中创建翻转窗口触发器上的依赖项。
services: data-factory
documentationcenter: ''
author: djpmsft
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: daperlov
ms.openlocfilehash: 3780a50429b95c402459866b7e3c87e8a91207d3
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683707"
---
# <a name="create-a-tumbling-window-trigger-dependency"></a>创建翻转窗口触发器依赖项

本文提供创建翻转窗口触发器上的依赖项的步骤。 有关翻转窗口触发器的一般信息，请参阅[如何创建翻转窗口触发器](how-to-create-tumbling-window-trigger.md)。

若要生成依赖关系链，并确保某个触发器只在成功执行数据工厂中的另一个触发器之后才执行，请使用此高级功能创建翻转窗口依赖项。

## <a name="create-a-dependency-in-the-data-factory-ui"></a>在数据工厂 UI 中创建依赖项

若要在触发器上创建依赖项，请选择“触发器”>“高级”>“新建”，然后选择要依赖的、具有相应偏移量和大小的触发器。 选择“完成”并发布数据工厂更改，使依赖项生效。

![依赖关系创建](media/tumbling-window-trigger-dependency/tumbling-window-dependency01.png "依赖关系创建")

## <a name="tumbling-window-dependency-properties"></a>翻转窗口依赖项属性

具有依赖项的翻转窗口触发器具有以下属性：

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
| offset | 依赖项触发器的偏移量。 提供一个时间跨度格式的值，同时允许负数和正偏移量。 如果触发器依赖于自身，而在所有其他情况下，此属性是可选的，则此属性是必需的。 自我依赖项应始终为负偏移量。 如果未指定任何值，则窗口与触发器本身相同。 | Timespan<br/>(hh:mm:ss) | 自依赖项：是<br/>其他：否 |
| size | 依赖项翻转窗口的大小。 提供正的 timespan 值。 此属性是可选的。 | Timespan<br/>(hh:mm:ss) | 否  |

> [!NOTE]
> 翻转窗口触发器最多可以有两个其他触发器。

## <a name="tumbling-window-self-dependency-properties"></a>翻转窗口自我依赖项属性

如果在上一窗口成功完成之前，触发器不应继续到下一个窗口，则生成一个自依赖项。 依赖于以前的 hr 中自身运行的成功的依赖项触发器将具有以下属性：

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

以下是翻转窗口依赖项属性的方案和使用情况的插图。

### <a name="dependency-offset"></a>依赖项偏移量

![偏移示例](media/tumbling-window-trigger-dependency/tumbling-window-dependency02.png "偏移示例")

### <a name="dependency-size"></a>依赖项大小

![大小示例](media/tumbling-window-trigger-dependency/tumbling-window-dependency03.png "大小示例")

### <a name="self-dependency"></a>自我依赖项

![自依赖项](media/tumbling-window-trigger-dependency/tumbling-window-dependency04.png "自我依赖项")

### <a name="dependency-on-another-tumbling-window-trigger"></a>依赖于另一个翻转窗口触发器

每日遥测处理作业，具体取决于每日聚合最后七天的作业输出，并生成七天的滚动窗口流：

![依赖项示例](media/tumbling-window-trigger-dependency/tumbling-window-dependency05.png "依赖项示例")

### <a name="dependency-on-itself"></a>依赖于自身

在作业输出流中不存在间隙的每日作业：

![自依赖项示例](media/tumbling-window-trigger-dependency/tumbling-window-dependency06.png "自依赖项示例")

## <a name="monitor-dependencies"></a>监视依赖项

你可以从 "触发器运行监视" 页监视依赖关系链和对应的窗口。 导航到“监视”>“触发器运行”。 在 "操作" 列下，您可以重新运行触发器或查看其依赖项。

![监视触发器运行](media/tumbling-window-trigger-dependency/tumbling-window-dependency07.png "监视触发器运行")

如果单击 "查看触发器依赖项"，可以查看依赖关系的状态。 如果某个依赖项触发器失败，则必须成功重新运行它才能运行相关触发器。 翻转窗口触发器将等待依赖项在七天后超时。

![监视依赖项](media/tumbling-window-trigger-dependency/tumbling-window-dependency08.png "监视依赖项")

若要更直观地查看触发器依赖关系计划，请选择 "甘特图" 视图。

![监视依赖项](media/tumbling-window-trigger-dependency/tumbling-window-dependency09.png "监视依赖项")

## <a name="next-steps"></a>后续步骤

* 查看[如何创建翻转窗口触发器](how-to-create-tumbling-window-trigger.md)
