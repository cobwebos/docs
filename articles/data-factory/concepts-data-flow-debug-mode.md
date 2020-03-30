---
title: 映射数据流调试模式
description: 在生成数据流时启动交互式调试会话
ms.author: makromer
author: kromerm
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/06/2019
ms.openlocfilehash: 18848695327a374f12fbe5a34d03366b050d8b65
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74928358"
---
# <a name="mapping-data-flow-debug-mode"></a>映射数据流调试模式

## <a name="overview"></a>概述

Azure 数据工厂映射数据流的调试模式允许您在生成和调试数据流时以交互方式监视数据形状转换。 调试会话既可用于数据流设计会话，也可用于数据流的管道调试执行。 要打开调试模式，请使用设计图面顶部的"数据流调试"按钮。

![调试滑块](media/data-flow/debugbutton.png "调试滑块")

打开滑块后，系统将提示您选择要使用的集成运行时配置。 如果选择了 AutoResolve 集成运行时间，则具有 8 个常规计算核心的群集，其运行时间为 60 分钟。 有关数据流集成运行时的详细信息，请参阅[数据流性能](concepts-data-flow-performance.md#increasing-compute-size-in-azure-integration-runtime)。

![调试红外选择](media/data-flow/debugbutton2.png "调试红外选择")

当调试模式打开时，您将使用活动 Spark 群集以交互方式构建数据流。 在 Azure 数据工厂中关闭调试后，会话将关闭。 你应该了解 Azure Databricks 在打开调试会话期间产生的每小时费用。

在大多数情况下，最好在调试模式下构建数据流，以便在在 Azure 数据工厂中发布工作之前验证业务逻辑并查看数据转换。 使用管道面板上的"调试"按钮测试管道中的数据流。

## <a name="cluster-status"></a>群集状态

当群集准备进行调试时，设计图面顶部的群集状态指示器将变为绿色。 如果群集已热，那么绿色指示器几乎会立即出现。 如果群集在进入调试模式时尚未运行，则必须等待 5-7 分钟，群集才能启动。 指示灯将旋转，直到准备就绪。

完成调试后，关闭调试开关，以便 Azure 数据块群集可以终止，并且不再为调试活动计费。

## <a name="debug-settings"></a>调试设置

可以通过单击"数据流"画布工具栏上的"调试设置"来编辑调试设置。 您可以在此处选择要用于每个源转换的行限制或文件源。 此设置中的行限制仅适用于当前调试会话。 您还可以选择要用于 SQL DW 源的过渡链接服务。 

![调试设置](media/data-flow/debug-settings.png "调试设置")

如果数据流或其任何引用数据集中具有参数，则可以通过选择 **"参数"** 选项卡来指定在调试期间要使用的值。

![调试设置参数](media/data-flow/debug-settings2.png "调试设置参数")

## <a name="data-preview"></a>数据预览

打开调试后，“数据预览”选项卡将在底部面板上亮起。 如果不打开调试模式，数据流将仅在"检查"选项卡中显示进出每个转换的当前元数据。数据预览将仅查询在调试设置中设置为限制的行数。 单击 **"刷新"** 以获取数据预览。

![数据预览](media/data-flow/datapreview.png "数据预览")

> [!NOTE]
> 文件源仅限制您看到的行，而不是要读取的行。 对于非常大的数据集，建议您获取该文件的一小部分并将其用于测试。 您可以在"调试设置"中为文件数据集类型的每个源选择临时文件。

在数据流中以调试模式运行时，数据不会写入接收器转换。 调试会话旨在用作转换的测试工具。 调试期间不需要接收器，并且会在数据流中忽略接收器。 如果要测试在 Sink 中写入数据，请从 Azure 数据工厂管道执行数据流，并使用管道中的调试执行。

### <a name="testing-join-conditions"></a>测试联接条件

当单元测试联接、存在或查找转换时，请确保为测试使用一小组已知数据。 您可以使用上面的"调试设置"选项来设置用于测试的临时文件。 这是必需的，因为在限制或采样大型数据集中的行时，无法预测将哪些行以及哪些键将读取到流中进行测试。 结果是非确定性的，这意味着您的联接条件可能会失败。

### <a name="quick-actions"></a>快速操作

看到数据预览后，可以生成快速转换以键入、删除或对列进行修改。 单击列标题，然后从数据预览工具栏中选择一个选项。

![快速操作](media/data-flow/quick-actions1.png "快速操作")

选择修改后，数据预览将立即刷新。 单击右上角的 **"确认"** 以生成新的转换。

![快速操作](media/data-flow/quick-actions2.png "快速操作")

**类型转换**和**修改**将生成派生列转换，**删除**将生成 Select 变换。

![快速操作](media/data-flow/quick-actions3.png "快速操作")

> [!NOTE]
> 如果编辑数据流，则需要在添加快速转换之前重新提取数据预览。

### <a name="data-profiling"></a>数据分析

在数据预览选项卡中选择一列，然后单击数据预览工具栏中的 **"统计信息"，** 将在数据网格的最右侧弹出一个图表，其中包含有关每个字段的详细统计信息。 Azure 数据工厂将根据要显示的图表类型的数据采样做出决定。 高基数字段将默认为 NULL/NOT NULL 图表，而基数较低的分类和数字数据将显示显示数据值频率的条形图。 您还将看到字符串字段的最大/len 长度、数值字段中的最小/最大值、标准开发、百分位数、计数和平均值。

![列统计信息](media/data-flow/stats.png "列统计信息")

## <a name="next-steps"></a>后续步骤

* 完成数据流的构建和调试后，[请从管道执行数据流。](control-flow-execute-data-flow-activity.md)
* 使用数据流测试管道时，请使用管道[调试运行执行选项。](iterative-development-debugging.md)
