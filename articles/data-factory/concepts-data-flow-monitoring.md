---
title: Azure 数据工厂映射数据流视觉监视
description: 如何以可视化方式监视 Azure 数据工厂数据流
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 90aa6261aebb9d1f7da89c101854bad8061dd6ff
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61268967"
---
# <a name="monitor-data-flows"></a>监视数据流

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

完成数据流的生成和调试之后，需要在管道上下文中将数据流安排为按计划执行。 可以使用触发器从 Azure 数据工厂计划管道。 或者，可以使用 Azure 数据工厂管道生成器中的“立即触发”选项执行单次运行，以便在管道上下文中测试数据流。

执行管道时，可以监视该管道及其包含的所有活动，包括数据流活动。 在 Azure 数据工厂 UI 的左侧面板中单击监视图标。 将会看到下面所示的屏幕。 使用突出显示的图标可以深入到管道中的活动，包括数据流活动。

<img src="media/data-flow/mon001.png" width="800">

还可以看到此级别的统计信息，包括运行次数和状态。 活动级别的运行 ID 不同于管道级别的运行 ID。 上一级别的运行 ID 适用于管道。 单击眼镜图标可以查看有关数据流执行的深入详细信息。

<img src="media/data-flow/mon002.png" width="800">

在图形节点监视视图中，将会看到数据流图形的简化只读版本。

<img src="media/data-flow/mon003.png" width="800">

## <a name="view-data-flow-execution-plans"></a>查看数据流执行计划

在 Databricks 中执行数据流时，Azure 数据工厂会根据数据流的完整性确定最佳代码路径。 此外，执行路径可能出现在不同的横向扩展节点和数据分区上。 因此，监视图形表示流的设计，它考虑到了转换的执行路径。 单击单个节点时，会看到“分组”，它们表示在群集上统一执行的代码。 看到的计时和计数表示相对于设计中各个步骤的组。

<img src="media/data-flow/mon004.png" width="800"> 

* 在监视窗口中单击空白区域时，底部窗格中的统计信息将显示每个接收器的计时和行数，以及生成转换沿袭的接收器数据的转换。

* 选择单个转换时，右侧面板会显示其他反馈，其中显示了分区统计信息、列数、偏斜度（数据在分区之间的分布均匀度）和峰度（数据的峰度）。

* 在节点视图中单击“接收器”时，会看到列沿袭。 在整个数据流中，可通过三种不同的方法来累积要载入接收器的列。 它们是：

  * 计算：使用列进行条件处理，或者在数据流中的表达式内使用列，但不将其载入接收器
  * 派生：列是在流中生成的新列，即，它不存在于源中
  * 映射：列来源于源，你要将它映射到接收器字段
  
## <a name="monitor-icons"></a>监视图标

此图标表示转换数据已在群集中缓存，因此计时和执行路径已考虑到这种情况：

<img src="media/data-flow/mon005.png" width="800"> 

在转换中还会看到绿色的圆圈图标。 它们表示数据流入的接收器数目。
