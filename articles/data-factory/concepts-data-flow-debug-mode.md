---
title: Azure 数据工厂映射数据流调试模式
description: 在生成数据流时启动交互式调试会话
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: d86725718217caf7fd1d9dd6d5d67362e5de7270
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147367"
---
# <a name="mapping-data-flow-debug-mode"></a>映射数据流调试模式

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

可以在设计图面顶部的"调试流数据"按钮打开 azure 数据工厂映射数据流的调试模式。 当设计数据流，启用调试模式下将允许您以交互方式监视数据形状转换时生成和调试你的数据的流。 在数据流设计会话以及管道的数据流的调试执行期间，可以使用调试会话。

![“调试”按钮](media/data-flow/debugbutton.png "“调试”按钮")

## <a name="overview"></a>概述
在调试模式下时，你将以交互方式生成数据的流与活动的 Spark 群集。 在 Azure 数据工厂中关闭调试后，会话将关闭。 你应该了解 Azure Databricks 在打开调试会话期间产生的每小时费用。

在大多数情况下，它是生成你在调试模式下的数据流动，以便可以验证您的业务逻辑，并在 Azure 数据工厂中发布你的工作之前查看你的数据转换的好办法。 使用管道面板上的"调试"按钮来测试管道内数据的流。

> [!NOTE]
> 数据工厂工具栏上的绿色调试模式下灯时，需付费的常规计算 8 个核心/小时的数据流调试率使用 60 分钟生存时间 

> [!NOTE]
>在数据流中以调试模式运行时，数据不会写入接收器转换。 调试会话旨在用作转换的测试工具。 调试期间不需要接收器，并且会在数据流中忽略接收器。 如果你想要测试数据写入接收器中，从 Azure 数据工厂管道中执行数据流，并使用调试执行从管道。

## <a name="debug-settings"></a>调试设置
调试可以数据流画布工具栏上单击"调试设置"编辑设置。 您可以选择限制和/或文件源以用于每个源转换。 在本设置中的行限制是仅为当前调试会话。 此外可以选择要用于 SQL 数据仓库源暂存链接的服务。 

![调试设置](media/data-flow/debug-settings.png "调试设置")

## <a name="cluster-status"></a>群集状态
没有将变为绿色，当群集处于准备好进行调试的设计图面顶部的群集状态指示器。 如果群集已热，那么绿色指示器几乎会立即出现。 如果你的群集不已在运行时进入调试模式，则您必须等待 5 到 7 分钟，要启动的群集。 指示器将旋转直到其就绪。

在您完成在调试，请关闭调试开关，以便在 Azure Databricks 群集可以终止，我们将不再计费调试活动。

## <a name="data-preview"></a>数据预览
打开调试后，“数据预览”选项卡将在底部面板上亮起。 如果未打开调试模式，数据流将仅在“检查”选项卡中显示传入和传出每个转换的当前元数据。数据预览仅将查询调试设置中设置为你的限制的行的数。 你可能需要单击“提取数据”来刷新数据预览。

![数据预览](media/data-flow/datapreview.png "数据预览")

## <a name="data-profiles"></a>数据配置文件
在你数据的预览选项卡中选择单个列将弹出图表上数据网格提供有关每个字段的详细统计信息最右侧。 Azure 数据工厂将根据要显示的图表类型的数据采样做出决定。 尽管具有较小的基数的分类和数值数据将显示条形图，显示数据值的频率，高基数字段将默认为 /NOT NULL 的图表。 此外可以看到最大/len 长度的字符串字段中，数值字段、 标准开发人员、 百分点值、 计数和平均值中的最小/最大值。 

![列统计信息](media/data-flow/stats.png "列统计信息")

## <a name="next-steps"></a>后续步骤

在您完成生成和调试数据的流，[从管道执行。](control-flow-execute-data-flow-activity.md)

测试时使用数据流管道，请使用管道[调试运行执行选项。](iterative-development-debugging.md)
