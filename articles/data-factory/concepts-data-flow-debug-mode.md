---
title: Azure 数据工厂映射数据流调试模式
description: 在生成数据流时启动交互式调试会话
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: a50778db5fd57202c17f05407045259371912586
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2019
ms.locfileid: "66239196"
---
# <a name="mapping-data-flow-debug-mode"></a>映射数据流调试模式

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Azure 数据工厂映射数据流有调试模式下，这可以设计图面顶部的调试流数据按钮切换。 设计数据流时，通过将调试模式设置为打开，可在生成和调试数据流时以交互方式观察数据形状转换。 在数据流设计会话以及管道的数据流的调试执行期间，可以使用调试会话。

![调试按钮](media/data-flow/debugbutton.png "调试按钮")

## <a name="overview"></a>概述
在调试模式下时，您将以交互方式生成数据的流与活动的 Spark 群集。 在 Azure 数据工厂中关闭调试后，会话将关闭。 你应该了解 Azure Databricks 在打开调试会话期间产生的每小时费用。

在大多数情况下，最好在调试模式下生成数据流，以便在 Azure 数据工厂中发布工作之前验证业务逻辑并查看数据转换。 您应使用管道面板上的"调试"按钮来测试管道内数据的流。

> [!NOTE]
> 调试模式下浅绿色数据工厂工具栏上时，您都收费的常规计算 8 个核心/小时的数据流调试率 60 分钟生存时间 

## <a name="debug-mode-on"></a>在调试模式
当你打开调试模式时，系统会提示你使用侧面板窗体，该窗体将要求你指向交互式 Azure Databricks 群集并选择源采样选项。 你必须使用 Azure Databricks 中的交互式群集，并从每个源转换中选择一个采样大小，或选择要用于测试数据的文本文件。

<img src="media/data-flow/upload.png" width="400">

> [!NOTE]
>在数据流中以调试模式运行时，数据不会写入接收器转换。 调试会话旨在用作转换测试工具。 调试期间不需要接收器，并且会在数据流中忽略接收器。 如果希望在接收器中测试数据写入，请从 Azure 数据工厂管道执行数据流，并从管道使用调试执行。

## <a name="debug-settings"></a>调试设置
调试设置可以是在数据流中的每个源会显示在侧面板，还可以通过选择"源设置"，在数据流设计器工具栏上进行编辑。 你可以在此处选择要用于每个源转换的限制和/或文件源。 在本设置中的行限制是仅为当前调试会话。 此外可以用于限制行源转换到在源中使用的采样设置。

## <a name="cluster-status"></a>群集状态
设计图面顶部有一个群集状态指示器，当群集准备好进行调试时，该指示器将变为绿色。 如果群集已热，那么绿色指示器几乎会立即出现。 如果在进入调试模式时群集尚未运行，则必须等待 5-7 分钟才能启动群集。 指示灯将为黄色，直到群集准备就绪。 群集准备好进行数据流调试后，指示灯将变为绿色。

在您完成在调试，请关闭调试开关，以便在 Azure Databricks 群集可以终止并将不再计费调试活动。

<img src="media/data-flow/datapreview.png" width="400">

## <a name="data-preview"></a>数据预览
打开调试后，“数据预览”选项卡将在底部面板上亮起。 如果未打开调试模式，数据流将仅在“检查”选项卡中显示传入和传出每个转换的当前元数据。数据预览仅将查询调试设置中设置为你的限制的行的数。 你可能需要单击“提取数据”来刷新数据预览。

<img src="media/data-flow/stats.png" width="400">

## <a name="data-profiles"></a>数据配置文件
在数据预览选项卡中选择单个列时，将在数据网格的最右侧弹出一个图表，其中包含有关每个字段的详细统计信息。 Azure 数据工厂将根据要显示的图表类型的数据采样做出决定。 高基数字段将默认为 NULL / NOT NULL 图表，而具有低基数的分类和数值数据将显示条形图（显示数据值频率）。 你还将看到字符串字段的最大/最小长度、数值字段中的最小/最大值、标准偏差、百分点值、计数和平均值。 

<img src="media/data-flow/chart.png" width="400">

## <a name="next-steps"></a>后续步骤

一旦您已完成的生成和调试数据的流，[从管道执行。](control-flow-execute-data-flow-activity.md)

测试时使用数据流管道，请使用管道[调试运行执行选项。](iterative-development-debugging.md)
