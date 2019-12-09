---
title: 数据流图表
description: 如何使用数据工厂数据流图表
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/04/2019
ms.openlocfilehash: da180bfb1aec29fa15b070fd73ba84d708ada927
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928313"
---
# <a name="mapping-data-flow-graphs"></a>映射数据流图表

映射数据流设计图面是一个 "构造" 图面，可在其中自上而下、从左向右构建数据流。 具有加号 (+) 符号的每个转换都附加有一个工具箱。 请专注于你的业务逻辑，而非在自由形式的 DAG 环境中通过边缘来连接节点。

下面是用于管理数据流图形的内置机制。

## <a name="move-nodes"></a>移动节点

![聚合转换选项](media/data-flow/agghead.png "聚合器标头")

如果不使用拖放模式，"移动" 转换节点的方式是更改传入流。 你将通过更改“传入流”来四处移动转换。

## <a name="streams-of-data-inside-of-data-flow"></a>数据流中的数据流

在 Azure 数据工厂数据流中，流表示数据的流动。 在 "转换设置" 窗格中，您将看到 "传入流" 字段。 它告诉你哪个传入数据流在为该转换提供数据。 可以通过单击传入流名称并选择另一个数据流在图形上更改转换节点的物理位置。 然后，该流上的当前转换以及所有后续转换都将移动到新位置。

如果要移动的转换后面有一个或多个转换，则会通过一个新分支来联接数据流中的新位置。

如果在你选择的节点后面没有后续转换，则只有该转换会移动到新位置。

## <a name="hide-graph-and-show-graph"></a>隐藏关系图并显示关系图

底部的 "配置" 窗格的右下角有一个按钮，可以在使用转换配置时将底部窗格扩展到全屏。 这样，便可以使用 "上一步" 和 "下一步" 按钮在图形配置中导航。 若要移回图形视图，请单击 "下一步" 按钮并返回到 "拆分屏幕"。

## <a name="search-graph"></a>搜索关系图

您可以在设计图面上搜索包含 "搜索" 按钮的关系图。

![搜索](media/data-flow/search001.png "搜索关系图")

## <a name="next-steps"></a>后续步骤

完成数据流设计后，打开 "调试" 按钮，并在调试模式下直接在 "数据流[设计器](concepts-data-flow-debug-mode.md)" 或 "[管道调试](control-flow-execute-data-flow-activity.md)" 中对其进行测试。
