---
title: 在 Azure 数据工厂中执行数据流活动 |Microsoft Docs
description: 如何执行数据从流中的数据工厂管道。
services: data-factory
documentationcenter: ''
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: makromer
ms.openlocfilehash: c33219eacb1d3bada5630a7792f98ba33dba824e
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2019
ms.locfileid: "66235863"
---
# <a name="execute-data-flow-activity-in-azure-data-factory"></a>在 Azure 数据工厂中执行数据流活动
使用 execute 数据流活动的管道调试 （沙盒） 运行和触发的管道运行中运行 ADF 数据的流。

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

## <a name="syntax"></a>语法

```json
{
    "name": "MyDataFlowActivity",
    "type": "ExecuteDataFlow",
    "typeProperties": {
      "dataflow": {
         "referenceName": "dataflow1",
         "type": "DataFlowReference"
      },
        "compute": {
          "computeType": "General",
          "coreCount": 8,
      }
}

```

## <a name="type-properties"></a>Type 属性

* ```dataflow``` 是你想要执行的数据流实体的名称
* ```compute``` 描述了 Spark 执行环境
* ```coreCount``` 是要分配给此活动执行的数据的核心数

![执行数据流](media/data-flow/activity-data-flow.png "执行数据流")

### <a name="debugging-pipelines-with-data-flows"></a>调试具有数据流管道

![调试按钮](media/data-flow/debugbutton.png "调试按钮")

使用数据流调试利用 warmed 的群集以供在运行管道调试中以交互方式测试你的数据的流。 使用管道调试选项来测试管道内你数据的流。

### <a name="run-on"></a>运行位置

这是必填的字段，用于定义要用于您数据流的活动执行的集成运行时。 默认情况下，数据工厂将使用默认自动解决 Azure 集成运行时。 但是，可以创建自己的 Azure 集成运行时，定义特定的区域，请将数据流活动执行的计算类型、 内核计数和 TTL。

数据流执行的默认设置是与 TTL 为 60 分钟的常规计算的 8 个核心。

选择此执行数据的流的计算环境。 默认值为 Azure 自动解决默认集成运行时。 选择此选项将在数据工厂所在的同一区域中的 Spark 环境上执行数据流。 计算类型将是作业群集，这意味着在计算环境将启动到需要几分钟时间。

Data Flow 活动可以控制的 Spark 执行环境。 在中[Azure 集成运行时](concepts-integration-runtime.md)设置计算类型 （常规用途、 内存优化和计算优化），辅助内核数和到-生存时间以匹配使用数据流计算的执行引擎的设置系统要求。 此外，设置 TTL 将允许您维护的热群集，可立即用于作业执行。

![Azure 集成运行时](media/data-flow/ir-new.png "Azure 集成运行时")

> [!NOTE]
> 中的数据流活动的集成运行时选择仅适用于*触发执行*管道。 使用调试调试你的流数据的管道将执行针对 8 核默认 Spark 群集。

### <a name="staging-area"></a>临时区域

如果到 Azure 数据仓库接收你的数据，则必须为 Polybase 批处理负载选择暂存位置。

## <a name="parameterized-datasets"></a>参数化数据集

如果使用参数化数据集，请务必设置参数值。

![执行数据流量参数](media/data-flow/params.png "参数")

### <a name="debugging-parameterized-data-flows"></a>调试参数化数据的流

仅可以调试数据流管道 Debug 使用执行数据的流活动运行中的参数化数据集。 目前，在 ADF 数据流中的交互式调试会话不使用参数化数据集。 管道执行和调试运行将使用参数。

较好的做法是生成与静态数据集数据的流，以便在设计时具有可用的完整元数据列传播。 然后替换为静态数据集的动态参数化数据集时实施数据流管道。

## <a name="next-steps"></a>后续步骤
查看数据工厂支持的其他控制流活动： 

- [If Condition 活动](control-flow-if-condition-activity.md)
- [Execute Pipeline 活动](control-flow-execute-pipeline-activity.md)
- [For Each 活动](control-flow-for-each-activity.md)
- [Get Metadata 活动](control-flow-get-metadata-activity.md)
- [Lookup 活动](control-flow-lookup-activity.md)
- [Web 活动](control-flow-web-activity.md)
- [Until 活动](control-flow-until-activity.md)
