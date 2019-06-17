---
title: Azure 数据工厂数据流移动节点
description: 如何移动节点在 Azure 数据工厂映射数据流关系图
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: 951a5d4fcbd561b085b0377bde48e820dc8972a2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65519968"
---
# <a name="mapping-data-flow-move-nodes"></a>映射数据流移动节点

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![聚合转换选项](media/data-flow/agghead.png "聚合器标头")

Azure 数据工厂数据流设计界面是一个“构造”界面，可以在其中从上到下、从左到右构建数据流。 具有加号 (+) 符号的每个转换都附加有一个工具箱。 请专注于你的业务逻辑，而非在自由形式的 DAG 环境中通过边缘来连接节点。

因此，在没有拖放范例的情况下，“移动”转换节点的方式是更改传入流。 你将通过更改“传入流”来四处移动转换。

## <a name="streams-of-data-inside-of-data-flow"></a>在数据流的数据流

在 Azure 数据工厂数据流中，流表示数据的流动。 在“转换设置”窗格中，你会看到一个“传入流”字段。 它告诉你哪个传入数据流在为该转换提供数据。 可以通过单击传入流名称并选择另一个数据流在图形上更改转换节点的物理位置。 然后，该流上的当前转换以及所有后续转换都将移动到新位置。

如果要移动的转换后面有一个或多个转换，则会通过一个新分支来联接数据流中的新位置。

如果在你选择的节点后面没有后续转换，则只有该转换会移动到新位置。

## <a name="next-steps"></a>后续步骤

数据流设计后，打开调试按钮并测试此程序在调试模式下可以直接在[数据流设计器](concepts-data-flow-debug-mode.md)或[管道调试](control-flow-execute-data-flow-activity.md)。
