---
title: Azure 数据工厂中的数据流活动 |Microsoft Docs
description: 如何在数据工厂管道中执行数据流。
services: data-factory
documentationcenter: ''
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.author: makromer
ms.date: 10/07/2019
ms.openlocfilehash: cbfa1acac34187263f8c4203e41bbe61d7e4c745
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030505"
---
# <a name="data-flow-activity-in-azure-data-factory"></a>Azure 数据工厂中的数据流活动

使用数据流活动可以通过映射数据流来转换和移动数据。 如果你不熟悉数据流，请参阅[映射数据流概述](concepts-data-flow-overview.md)

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

属性 | 说明 | 允许的值 | 需要
-------- | ----------- | -------------- | --------
数据流 | 对正在执行的数据流的引用 | DataFlowReference | 是
integrationRuntime | 运行数据流的计算环境 | IntegrationRuntimeReference | 是
暂存。 linkedService | 如果使用的是 SQL DW 源或接收器，则用于 PolyBase 暂存的存储帐户 | LinkedServiceReference | 仅当数据流读取或写入 SQL DW 时
暂存。 folderPath | 如果使用的是 SQL DW 源或接收器，则用于 PolyBase 暂存的 blob 存储帐户中的文件夹路径 | String | 仅当数据流读取或写入 SQL DW 时

![执行数据流](media/data-flow/activity-data-flow.png "执行数据流")

### <a name="data-flow-integration-runtime"></a>数据流集成运行时

选择要用于数据流活动执行的 Integration Runtime。 默认情况下，数据工厂将使用具有四个辅助角色的 "自动解析 Azure 集成运行时" 和 "无生存时间（TTL）"。 此 IR 具有常规用途计算类型，并与工厂在同一区域中运行。 你可以创建自己的 Azure 集成运行时，用于定义数据流活动执行的特定区域、计算类型、核心计数和 TTL。

对于管道执行，群集是作业群集，在执行开始之前需要几分钟时间启动。 如果未指定 TTL，则每次运行管道时都需要此启动时间。 如果指定 TTL，则在上一次执行之后指定的时间，温群集池将保持活动状态，从而缩短启动时间。 例如，如果 TTL 为60分钟，并且一小时运行一次数据流，则群集池将保持活动状态。 有关详细信息，请参阅[Azure 集成运行时](concepts-integration-runtime.md)。

![Azure Integration Runtime](media/data-flow/ir-new.png "Azure Integration Runtime")

> [!NOTE]
> 数据流活动中的 Integration Runtime 选择仅适用于管道的已触发执行。 在调试会话中指定的群集上运行数据流时，调试管道。

### <a name="polybase"></a>PolyBase

如果使用 Azure SQL 数据仓库作为接收器或源，则必须为 PolyBase 批处理负载选择一个暂存位置。 PolyBase 允许批量加载，而不是逐行加载数据。 PolyBase 大大降低了 SQL DW 的加载时间。

## <a name="parameterizing-data-flows"></a>参数化数据流

### <a name="parameterized-datasets"></a>参数化数据集

如果数据流使用参数化数据集，则在 "**设置**" 选项卡中设置参数值。

![执行数据流参数](media/data-flow/params.png "参数")

### <a name="parameterized-data-flows"></a>参数化数据流

如果您的数据流已参数化，则在 "**参数**" 选项卡中设置数据流参数的动态值。您可以使用 ADF 管道表达式语言（仅适用于字符串类型）或数据流表达式语言来分配动态或文本参数值。 有关详细信息，请参阅[数据流参数](parameters-data-flow.md)。

![Execute 数据流参数示例](media/data-flow/parameter-example.png "参数示例")

## <a name="pipeline-debug-of-data-flow-activity"></a>数据流活动的管道调试

若要执行带有数据流活动的调试管道运行，必须通过顶部栏上的 "数据流**调试**" 滑块来切换数据流调试模式。 调试模式允许您针对活动的 Spark 群集运行数据流。 有关详细信息，请参阅[调试模式](concepts-data-flow-debug-mode.md)。

![“调试”按钮](media/data-flow/debugbutton.png "“调试”按钮")

调试管道针对活动调试群集运行，而不是在数据流活动设置中指定的集成运行时环境。 启动调试模式时，可以选择调试计算环境。

## <a name="monitoring-the-data-flow-activity"></a>监视数据流活动

数据流活动具有特殊的监视体验，你可以在其中查看分区、阶段时间和数据沿袭信息。 通过 "**操作**" 下的 "眼镜" 图标打开 "监视" 窗格。 有关详细信息，请参阅[监视数据流](concepts-data-flow-monitoring.md)。

## <a name="next-steps"></a>后续步骤

请参阅数据工厂支持的控制流活动： 

- [If Condition 活动](control-flow-if-condition-activity.md)
- [Execute Pipeline 活动](control-flow-execute-pipeline-activity.md)
- [For Each 活动](control-flow-for-each-activity.md)
- [Get Metadata 活动](control-flow-get-metadata-activity.md)
- [Lookup 活动](control-flow-lookup-activity.md)
- [Web 活动](control-flow-web-activity.md)
- [Until 活动](control-flow-until-activity.md)
