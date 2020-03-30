---
title: 数据流活动
description: 如何在数据工厂管道中执行数据流。
services: data-factory
documentationcenter: ''
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: makromer
ms.date: 03/16/2020
ms.openlocfilehash: 115cb3e499117457629e130b6432a1cbc2224edb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79463044"
---
# <a name="data-flow-activity-in-azure-data-factory"></a>Azure 数据工厂中的数据流活动

使用数据流活动通过映射数据流转换和移动数据。 如果您是数据流的新人，请参阅[映射数据流概述](concepts-data-flow-overview.md)

## <a name="syntax"></a>语法

```json
{
    "name": "MyDataFlowActivity",
    "type": "ExecuteDataFlow",
    "typeProperties": {
      "dataflow": {
         "referenceName": "MyDataFlow",
         "type": "DataFlowReference"
      },
      "compute": {
         "coreCount": 8,
         "computeType": "General"
      },
      "staging": {
          "linkedService": {
              "referenceName": "MyStagingLinkedService",
              "type": "LinkedServiceReference"
          },
          "folderPath": "my-container/my-folder"
      },
      "integrationRuntime": {
          "referenceName": "MyDataFlowIntegrationRuntime",
          "type": "IntegrationRuntimeReference"
      }
}

```

## <a name="type-properties"></a>Type 属性

properties | 描述 | 允许的值 | 必选
-------- | ----------- | -------------- | --------
数据流 | 对正在执行的数据流的引用 | 数据流参考 | 是
集成运行时间 | 数据流运行的计算环境。 如果未指定，将使用自动解析 Azure 集成运行时 | 集成运行时参考 | 否
计算.核心计数 | 火花簇中使用的内核数。 仅当使用自动解析 Azure 集成运行时时才能指定 | 8, 16, 32, 48, 80, 144, 272 | 否
计算.计算类型 | 火花群集中使用的计算类型。 仅当使用自动解析 Azure 集成运行时时才能指定 | "常规"、"计算优化"、"内存优化" | 否
暂存.链接服务 | 如果您使用的是 SQL DW 源或接收器，则用于 PolyBase 暂存的存储帐户 | 链接服务参考 | 仅当数据流读取或写入 SQL DW 时
暂存.文件夹路径 | 如果使用 SQL DW 源或接收器，则 Blob 存储帐户中的文件夹路径用于 PolyBase 暂存 | String | 仅当数据流读取或写入 SQL DW 时

![执行数据流](media/data-flow/activity-data-flow.png "执行数据流")

### <a name="dynamically-size-data-flow-compute-at-runtime"></a>运行时动态大小数据流计算

可以动态设置核心计数和计算类型属性，以根据运行时传入源数据的大小进行调整。 使用管道活动（如查找或获取元数据）来查找源数据集数据的大小。 然后，在数据流活动属性中使用"添加动态内容"。

![动态数据流](media/data-flow/dyna1.png "动态数据流")

[这里是一个简短的视频教程解释这种技术](https://www.youtube.com/watch?v=jWSkJdtiJNM)

### <a name="data-flow-integration-runtime"></a>数据流集成运行时

选择要用于数据流活动执行的集成运行时。 默认情况下，数据工厂将使用具有四个工作核心且没有时间生存 （TTL） 的自动解析 Azure 集成运行时。 此 IR 具有通用计算类型，并且与工厂在同一区域运行。 您可以创建自己的 Azure 集成运行时，用于定义特定区域、计算类型、核心计数和 TTL 以执行数据流活动。

对于管道执行，群集是作业群集，在执行启动前需要几分钟时间启动。 如果未指定 TTL，则每次管道运行都需要此启动时间。 如果指定 TTL，则暖群集池将在上次执行后指定的时间内保持活动状态，从而导致启动时间缩短。 例如，如果 TTL 为 60 分钟，并且每小时在它上运行一次数据流，群集池将保持活动状态。 有关详细信息，请参阅[Azure 集成运行时](concepts-integration-runtime.md)。

![Azure 集成运行时](media/data-flow/ir-new.png "Azure 集成运行时")

> [!NOTE]
> 数据流活动中的 Integration Runtime 选择仅适用于管道的已触发执行。** 使用数据流调试管道，在调试会话中指定的群集上运行。

### <a name="polybase"></a>PolyBase

如果将 Azure SQL 数据仓库用作接收器或源，则必须为 PolyBase 批处理负载选择暂存位置。 PolyBase 允许批量批量加载批处理，而不是逐行加载数据。 PolyBase 大大减少了 SQL DW 的加载时间。

## <a name="parameterizing-data-flows"></a>参数化数据流

### <a name="parameterized-datasets"></a>参数化数据集

如果数据流使用参数化数据集，请在 **"设置"** 选项卡中设置参数值。

![执行数据流参数](media/data-flow/params.png "参数")

### <a name="parameterized-data-flows"></a>参数化数据流

如果数据流已参数化，请在 **"参数"** 选项卡中设置数据流参数的动态值。您可以使用 ADF 管道表达式语言（仅适用于字符串类型）或数据流表达式语言来分配动态或文本参数值。 有关详细信息，请参阅[数据流参数](parameters-data-flow.md)。

![执行数据流参数示例](media/data-flow/parameter-example.png "参数示例")

### <a name="parameterized-compute-properties"></a>参数化计算属性。

如果使用自动解析 Azure 集成运行时并指定计算值，则可以对核心计数或计算类型进行参数化。

![执行数据流参数示例](media/data-flow/parameterize-compute.png "参数示例")

## <a name="pipeline-debug-of-data-flow-activity"></a>数据流活动的管道调试

要执行使用数据流活动运行的调试管道，必须通过顶部栏上**的数据流调试**滑块打开数据流调试模式。 调试模式允许您针对活动 Spark 群集运行数据流。 有关详细信息，请参阅[调试模式](concepts-data-flow-debug-mode.md)。

![“调试”按钮](media/data-flow/debugbutton.png "“调试”按钮")

调试管道针对活动调试群集运行，而不是数据流活动设置中指定的集成运行时环境。 启动调试模式时，可以选择调试计算环境。

## <a name="monitoring-the-data-flow-activity"></a>监视数据流活动

数据流活动具有特殊的监视体验，您可以在其中查看分区、阶段时间和数据血统信息。 通过 **"操作"** 下的眼镜图标打开监控窗格。 有关详细信息，请参阅[监视数据流](concepts-data-flow-monitoring.md)。

### <a name="use-data-flow-activity-results-in-a-subsequent-activity"></a>使用数据流活动会导致后续活动

数据流活动输出有关写入每个接收器的行数和从每个源读取的行数的指标。 这些结果在活动运行结果的部分`output`中返回。 返回的指标采用以下 json 的格式。

``` json
{
    "runStatus": {
        "metrics": {
            "<your sink name1>": {
                "rowsWritten": <number of rows written>,
                "sinkProcessingTime": <sink processing time in ms>,
                "sources": {
                    "<your source name1>": {
                        "rowsRead": <number of rows read>
                    },
                    "<your source name2>": {
                        "rowsRead": <number of rows read>
                    },
                    ...
                }
            },
            "<your sink name2>": {
                ...
            },
            ...
        }
    }
}
```

例如，要获取在名为"数据流活动"的活动中写入名为"sink1"的接收器的行数，请使用`@activity('dataflowActivity').output.runStatus.metrics.sink1.rowsWritten`。

要获取从该接收器中使用的名为"source1"的源读取的行数，请使用`@activity('dataflowActivity').output.runStatus.metrics.sink1.sources.source1.rowsRead`。

> [!NOTE]
> 如果接收器的行为零，则不会显示在指标中。 可以使用 函数`contains`验证存在。 例如，`contains(activity('dataflowActivity').output.runStatus.metrics, 'sink1')`将检查是否将任何行写入 sink1。

## <a name="next-steps"></a>后续步骤

请参阅数据工厂支持的控制流活动： 

- [If Condition 活动](control-flow-if-condition-activity.md)
- [执行管道活动](control-flow-execute-pipeline-activity.md)
- [对于每个活动](control-flow-for-each-activity.md)
- [获取元数据活动](control-flow-get-metadata-activity.md)
- [Lookup 活动](control-flow-lookup-activity.md)
- [网站活动](control-flow-web-activity.md)
- [Until 活动](control-flow-until-activity.md)
