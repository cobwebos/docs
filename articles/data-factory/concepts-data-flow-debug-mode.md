---
title: Azure 数据工厂映射数据流调试模式
description: 在生成数据流时启动交互式调试会话
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: 2851517aee0176775bde8b58994f0ed20f6ed01d
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2019
ms.locfileid: "56212304"
---
# <a name="azure-data-factory-mapping-data-flow-debug-mode"></a>Azure 数据工厂映射数据流调试模式

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Azure 数据工厂映射数据流具有调试模式，可以使用设计图面顶部的“调试”按钮打开该模式。 设计数据流时，通过将调试模式设置为打开，可在生成和调试数据流时以交互方式观察数据形状转换。

<img src="media/data-flow/debugbutton.png" width="400">

## <a name="overview"></a>概述
打开调试模式后，将使用正在运行的 Azure Databricks 交互式群集以交互方式生成数据流。 在 Azure 数据工厂中关闭调试后，会话将关闭。 你应该了解 Azure Databricks 在打开调试会话期间产生的每小时费用。

在大多数情况下，最好在调试模式下生成数据流，以便在 Azure 数据工厂中发布工作之前验证业务逻辑并查看数据转换。

## <a name="debug-mode-on"></a>打开调试模式
当你打开调试模式时，系统会提示你使用侧面板窗体，该窗体将要求你指向交互式 Azure Databricks 群集并选择源采样选项。 你必须使用 Azure Databricks 中的交互式群集，并从每个源转换中选择一个采样大小，或选择要用于测试数据的文本文件。

<img src="media/data-flow/upload.png" width="400">

> [!NOTE]
>在数据流中以调试模式运行时，数据不会写入接收器转换。 调试会话旨在用作转换测试工具。 调试期间不需要接收器，并且会在数据流中忽略接收器。 如果希望在接收器中测试数据写入，请从 Azure 数据工厂管道执行数据流，并从管道使用调试执行。

## <a name="debug-settings"></a>调试设置
调试设置可以是数据流中的各个源，将显示在侧面板中，还可以通过在数据流设计工具栏上选择“源设置”进行编辑。 你可以在此处选择要用于每个源转换的限制和/或文件源。 还可以选择要用于调试的 Databricks 群集。

## <a name="cluster-status"></a>群集状态
设计图面顶部有一个群集状态指示器，当群集准备好进行调试时，该指示器将变为绿色。 如果群集已热，那么绿色指示器几乎会立即出现。 如果在进入调试模式时群集尚未运行，则必须等待 5-7 分钟才能启动群集。 指示灯将为黄色，直到群集准备就绪。 群集准备好进行数据流调试后，指示灯将变为绿色。

完成调试后，关闭调试开关，以便 Azure Databricks 群集可以终止调试。

<img src="media/data-flow/datapreview.png" width="400">

## <a name="data-preview"></a>数据预览
打开调试后，“数据预览”选项卡将在底部面板上亮起。 如果未打开调试模式，数据流将仅在“检查”选项卡中显示传入和传出每个转换的当前元数据。数据预览仅查询你在源设置中设置为限制的行数。 你可能需要单击“提取数据”来刷新数据预览。

<img src="media/data-flow/stats.png" width="400">

## <a name="data-profiles"></a>数据配置文件
在数据预览选项卡中选择单个列时，将在数据网格的最右侧弹出一个图表，其中包含有关每个字段的详细统计信息。 Azure 数据工厂将根据要显示的图表类型的数据采样做出决定。 高基数字段将默认为 NULL / NOT NULL 图表，而具有低基数的分类和数值数据将显示条形图（显示数据值频率）。 你还将看到字符串字段的最大/最小长度、数值字段中的最小/最大值、标准偏差、百分点值、计数和平均值。 

<img src="media/data-flow/chart.png" width="400">
