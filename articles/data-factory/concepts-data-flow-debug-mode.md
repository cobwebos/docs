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
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928358"
---
# <a name="mapping-data-flow-debug-mode"></a>映射数据流调试模式

## <a name="overview"></a>概述

Azure 数据工厂映射数据流的调试模式允许您在生成和调试数据流时以交互方式监视数据形状转换。 调试会话既可以在数据流设计会话中使用，也可以在数据流的管道调试执行过程中使用。 若要启用调试模式，请使用设计图面顶部的 "数据流调试" 按钮。

![调试滑块](media/data-flow/debugbutton.png "调试滑块")

打开滑块后，系统将提示您选择要使用的集成运行时配置。 如果选择了 "AutoResolveIntegrationRuntime"，则将启动具有8个常规计算的群集，其生存时间为60分钟。 有关数据流集成运行时的详细信息，请参阅数据流[性能](concepts-data-flow-performance.md#increasing-compute-size-in-azure-integration-runtime)。

![调试 IR 选择](media/data-flow/debugbutton2.png "调试 IR 选择")

当调试模式为 on 时，将使用活动的 Spark 群集以交互方式生成数据流。 在 Azure 数据工厂中关闭调试后，会话将关闭。 你应该了解 Azure Databricks 在打开调试会话期间产生的每小时费用。

在大多数情况下，最好在调试模式下生成数据流，以便在 Azure 数据工厂中发布工作之前验证业务逻辑和查看数据转换。 使用 "管道" 面板上的 "调试" 按钮来测试管道中的数据流。

## <a name="cluster-status"></a>群集状态

当群集准备好进行调试时，设计图面顶部的群集状态指示器将变为绿色。 如果群集已热，那么绿色指示器几乎会立即出现。 如果在你进入调试模式时群集尚未运行，则你必须等待5-7 分钟以便群集启动。 指示器将旋转到其就绪状态。

完成调试后，请关闭调试开关，以便 Azure Databricks 群集可以终止，并且不再为调试活动付费。

## <a name="debug-settings"></a>调试设置

可以通过单击 "数据流画布" 工具栏上的 "调试设置" 来编辑调试设置。 你可以在此处选择要用于每个源转换的行限制或文件源。 此设置中的行限制仅适用于当前调试会话。 你还可以选择要用于 SQL DW 源的暂存链接的服务。 

![调试设置](media/data-flow/debug-settings.png "调试设置")

如果数据流或其引用的任何数据集中都有参数，则可以通过选择 "**参数**" 选项卡指定要在调试期间使用的值。

![调试设置参数](media/data-flow/debug-settings2.png "调试设置参数")

## <a name="data-preview"></a>数据预览

打开调试后，“数据预览”选项卡将在底部面板上亮起。 如果在上没有调试模式，数据流将仅在 "检查" 选项卡中显示和转换每个转换的当前元数据。数据预览只会查询您在调试设置中设置为限制的行数。 单击 "**刷新**" 提取数据预览。

![数据预览](media/data-flow/datapreview.png "数据预览")

> [!NOTE]
> 文件源仅限制您看到的行数，而不限制所读取的行数。 对于非常大的数据集，建议你拍摄该文件的一小部分，并将其用于测试。 可以为文件数据集类型的每个源选择 "调试" 设置中的临时文件。

在数据流中以调试模式运行时，数据不会写入接收器转换。 调试会话旨在用作转换的测试工具。 调试期间不需要接收器，并且会在数据流中忽略接收器。 如果希望测试在接收器中写入数据，请从 Azure 数据工厂管道执行数据流，并使用管道中的调试执行。

### <a name="testing-join-conditions"></a>测试联接条件

单元测试联接、存在或查找转换时，请确保为测试使用少量的已知数据。 你可以使用上面的 "调试设置" 选项来设置用于测试的临时文件。 这是必需的，因为在对大型数据集中的行进行限制或采样时，无法预测哪些行以及哪些键将被读入流以进行测试。 结果是非确定性的，这意味着联接条件可能会失败。

### <a name="quick-actions"></a>快速操作

看到数据预览后，就可以生成快速转换来转换、删除或修改列。 单击列标题，然后从数据预览工具栏中选择一个选项。

![快速操作](media/data-flow/quick-actions1.png "快速操作")

选择修改后，数据预览会立即刷新。 在右上角单击 "**确认**" 以生成新的转换。

![快速操作](media/data-flow/quick-actions2.png "快速操作")

**转换**和**Modify**将生成一个派生列转换，并且**删除**将生成一个 Select 转换。

![快速操作](media/data-flow/quick-actions3.png "快速操作")

> [!NOTE]
> 如果编辑数据流，则需要在添加快速转换之前重新提取数据预览。

### <a name="data-profiling"></a>数据事件探查

在 "数据预览" 选项卡中选择一个列，然后在 "数据预览" 工具栏中单击 "**统计信息**"，将在数据网格最右侧弹出一个图表，其中包含有关每个字段的详细统计信息。 Azure 数据工厂将根据要显示的图表类型的数据采样做出决定。 高基数字段将默认为 NULL/NOT NULL 图表，而具有较低基数的分类和数值数据将显示显示数据值频率的条形图。 你还将看到字符串字段的 max/len 长度、数字字段中的最小/最大值、标准 dev、百分位数、计数和平均值。

![列统计信息](media/data-flow/stats.png "列统计信息")

## <a name="next-steps"></a>后续步骤

* 完成生成和调试数据流后，请[从管道中执行它。](control-flow-execute-data-flow-activity.md)
* 使用数据流测试管道时，请使用管道[调试 "运行执行" 选项。](iterative-development-debugging.md)
