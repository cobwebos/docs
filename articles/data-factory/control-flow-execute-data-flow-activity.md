---
title: 在 Azure 数据工厂中执行数据流活动 |Microsoft Docs
description: 执行数据的流活动运行数据流。
services: data-factory
documentationcenter: ''
author: kromerm
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: makromer
ms.openlocfilehash: 856f4bd9c2b04ff10ed598c5e641955e1de99398
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2019
ms.locfileid: "57792406"
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

### <a name="run-on"></a>运行位置

选择此执行数据的流的计算环境。 默认值为 Azure 自动解决默认集成运行时。 选择此选项将在数据工厂所在的同一区域中的 Spark 环境上执行数据流。 计算类型将是作业群集，这意味着在计算环境将启动到需要几分钟时间。

### <a name="debugging-pipelines-with-data-flows"></a>调试具有数据流管道

![调试按钮](media/data-flow/debugbutton.png "调试按钮")

使用数据流调试利用 warmed 的群集以供在运行管道调试中以交互方式测试你的数据的流。 使用管道调试选项来测试管道内你数据的流。

### <a name="compute-type"></a>计算类型

您可以选择常规用途、 计算优化或内存优化的取决于数据的流的要求。

### <a name="core-count"></a>核心计数

选择你想要分配给作业的内核数。 对于较小的作业，将更好地工作较少核心。

### <a name="staging-area"></a>临时区域

如果到 Azure 数据仓库接收你的数据，则必须为 Polybase 批处理负载选择暂存位置。

## <a name="parameterized-datasets"></a>参数化数据集

如果使用参数化数据集，请务必设置参数值。

![执行数据流量参数](media/data-flow/params.png "参数")

### <a name="debugging-parameterized-data-flows"></a>调试参数化数据的流

仅可以调试数据流管道 Debug 使用执行数据的流活动运行中的参数化数据集。 目前，在 ADF 数据流中的交互式调试会话不使用参数化数据集。 管道执行和调试运行将使用参数。

## <a name="next-steps"></a>后续步骤
查看数据工厂支持的其他控制流活动： 

- [If Condition 活动](control-flow-if-condition-activity.md)
- [Execute Pipeline 活动](control-flow-execute-pipeline-activity.md)
- [For Each 活动](control-flow-for-each-activity.md)
- [Get Metadata 活动](control-flow-get-metadata-activity.md)
- [Lookup 活动](control-flow-lookup-activity.md)
- [Web 活动](control-flow-web-activity.md)
- [Until 活动](control-flow-until-activity.md)
