---
title: 数据流图
description: 如何使用数据工厂数据流图
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/04/2019
ms.openlocfilehash: 0d357c4c671070a5c5e9d4587e2f90b6628996f4
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605361"
---
# <a name="mapping-data-flow-graphs"></a>映射数据流图

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

映射数据流设计图面是一个"构造"曲面，您可以在其中自上而下、从左到右生成数据流。 具有加号 (+) 符号的每个转换都附加有一个工具箱。 请专注于你的业务逻辑，而非在自由形式的 DAG 环境中通过边缘来连接节点。

下面是管理数据流图的内置机制。

## <a name="move-nodes"></a>移动节点

![聚合转换选项](media/data-flow/agghead.png "聚合器标头")

如果没有拖放范例，"移动"转换节点的方法就是更改传入流。 你将通过更改“传入流”来四处移动转换。

## <a name="streams-of-data-inside-of-data-flow"></a>数据流内部的数据流

在 Azure 数据工厂数据流中，流表示数据的流动。 在转换设置窗格中，您将看到一个"传入流"字段。 它告诉你哪个传入数据流在为该转换提供数据。 可以通过单击传入流名称并选择另一个数据流在图形上更改转换节点的物理位置。 然后，该流上的当前转换以及所有后续转换都将移动到新位置。

如果要移动的转换后面有一个或多个转换，则会通过一个新分支来联接数据流中的新位置。

如果在你选择的节点后面没有后续转换，则只有该转换会移动到新位置。

## <a name="hide-graph-and-show-graph"></a>隐藏图形并显示图形

底部配置窗格的右侧有一个按钮，您可以在处理转换配置时将底部窗格扩展到全屏。 这将允许您使用"上一个"和"下一个"按钮浏览图形的配置。 要移回图形视图，请单击向下按钮并返回到拆分屏幕。

## <a name="search-graph"></a>搜索图

您可以使用设计图面上的搜索按钮搜索图形。

![搜索](media/data-flow/search001.png "搜索图")

## <a name="next-steps"></a>后续步骤

完成数据流设计后，打开调试按钮，直接在[数据流设计器](concepts-data-flow-debug-mode.md)或[管道调试](control-flow-execute-data-flow-activity.md)中以调试模式进行测试。
