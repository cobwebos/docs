---
title: 管理映射数据流图表
description: 如何有效地管理和编辑映射数据流图表
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/02/2020
ms.openlocfilehash: 0cdad47123d69ca7cee468c5bb0cea3268d73bfe
ms.sourcegitcommit: 9c262672c388440810464bb7f8bcc9a5c48fa326
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2020
ms.locfileid: "89420064"
---
# <a name="managing-the-mapping-data-flow-graph"></a>管理映射数据流图表

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

映射数据流是使用设计图面来编写的，它被视为数据流图表。 在图形中，转换逻辑是从左向右构建的，而其他数据流则自上而下地添加。 若要添加新的转换，请选择现有转换右下方的加号。

![画布](media/data-flow/canvas2.png "画布")

随着您的数据流变得更加复杂，使用以下机制来有效地浏览和管理数据流图表。 

## <a name="moving-transformations"></a>移动转换

在映射数据流时，一组已连接的转换逻辑称为 " **流**"。 **传入流**字段规定哪个数据流正在传输当前转换。 每个转换都有一个或两个传入流，具体取决于其函数并表示一个输出流。 传入流的输出架构确定哪些列元数据可由当前转换引用。

![移动节点](media/data-flow/move-nodes.png "移动节点")

与管道画布不同，数据流转换不能使用拖放模型进行编辑。 若要更改的传入流或 "移动" 转换，请从 **传入流** 下拉列表中选择其他值。 执行此操作时，所有下游转换将与编辑的转换一起移动。 图形会自动更新，以显示新的逻辑流。 如果将传入流更改为已具有下游转换的转换，则将创建新的分支或并行数据流。 了解有关 [映射数据流中的新分支的](data-flow-new-branch.md)详细信息。

## <a name="hide-graph-and-show-graph"></a>隐藏关系图并显示关系图

编辑转换时，可以展开配置面板，使其占据整个画布，隐藏关系图。 单击画布右侧的向上的 v 形 v 形 v 形 v 形 v 形展开按钮。

![隐藏图形](media/data-flow/hide-graph.png "隐藏关系图")

隐藏关系图后，可以通过单击 " **下一步** " 或 " **上一**步" 在流内的转换之间移动。 单击向下的 v 形 v 形 v 形图标以显示图表。

![显示关系图](media/data-flow/show-graph.png "显示关系图")

## <a name="searching-for-transformations"></a>搜索转换

若要在关系图中快速找到转换，请单击 "缩放" 设置上方的 **搜索** 图标。

![搜索](media/data-flow/search-1.png "搜索关系图")

您可以按转换名称或说明进行搜索以查找转换。

![搜索](media/data-flow/search-2.png "搜索关系图")

## <a name="hide-reference-nodes"></a>隐藏引用节点

如果数据流有任何联接、查找、存在或联合转换，则数据流会向所有传入流显示引用节点。 如果要最大程度地减少所用的垂直空间量，可以最小化引用节点。 为此，请在画布上单击右键，然后选择 " **隐藏引用节点**"。

![隐藏引用节点](media/data-flow/hide-reference-nodes.png "隐藏引用节点")

## <a name="next-steps"></a>后续步骤

完成数据流逻辑后，打开 [调试模式](concepts-data-flow-debug-mode.md) 并在数据预览中对其进行测试。
