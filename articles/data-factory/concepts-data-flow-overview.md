---
title: 在 Azure 数据工厂中映射数据流 |Microsoft Docs
description: 在 Azure 数据工厂中映射数据流的概述
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/7/2019
ms.openlocfilehash: 8d0ad794caee8a06c8d403a981037d6560fb3f43
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030123"
---
# <a name="what-are-mapping-data-flows"></a>映射数据流是什么？

映射数据流是在 Azure 数据工厂中以可视方式设计的数据转换。 数据流允许数据工程师开发图形数据转换逻辑，而无需编写代码。 生成的数据流在 Azure 数据工厂管道中使用扩展的 Spark 群集作为活动执行。 数据流活动可以通过现有的数据工厂计划、控制、流和监视功能来操作化。

映射数据流提供完全直观的体验，无需编码。 数据流将在自己的执行群集上执行，以用于扩展的数据处理。 Azure 数据工厂处理数据流作业的所有代码转换、路径优化以及执行。

## <a name="getting-started"></a>入门

若要创建数据流，请单击 "工厂资源" 下的加号。 

![新建 数据流](media/data-flow/newdataflow2.png "新建")数据流

这会将你转到可在其中创建转换逻辑的数据流画布。 单击 "添加源" 框，开始配置源转换。 有关详细信息，请参阅[源转换](data-flow-source.md)。

## <a name="data-flow-canvas"></a>数据流画布

数据流画布分为三部分：顶栏、图形和配置面板。 

![Canvas](media/data-flow/canvas1.png "Canvas")

### <a name="graph"></a>图形

关系图显示转换流。 它显示源数据流入一个或多个接收器时的沿袭。 若要添加新源，请单击 "添加源" 框。 若要添加新的转换，请单击现有转换右下方的加号。

![Canvas](media/data-flow/canvas2.png "Canvas")

### <a name="configuration-panel"></a>配置面板

配置面板显示特定于当前选定转换的设置，如果未选择任何转换，则显示数据流。 在整个数据流配置中，你可以在 "**常规**" 选项卡下编辑名称和说明，或通过 "**参数**" 选项卡添加参数。有关详细信息，请参阅[映射数据流参数](parameters-data-flow.md)。

每个转换至少具有四个配置选项卡：

#### <a name="transformation-settings"></a>转换设置

每个转换的配置窗格中的第一个选项卡包含特定于该转换的设置。 有关详细信息，请参阅该转换的文档页。

![源设置选项卡](media/data-flow/source1.png "源设置选项卡")

#### <a name="optimize"></a>优化

"_优化_" 选项卡包含配置分区方案的设置。

![优化](media/data-flow/optimize1.png "优化")

默认设置为 "使用当前分区"，它指示 Azure 数据工厂使用本地分区方案和在 Spark 上运行的数据流。 在大多数情况下，建议使用此设置方法。

在某些情况下，你可能想要调整分区。 例如，如果想要将转换输出到 lake 中的单个文件，请在接收器转换中选择 "单一分区"。

您可能想要控制分区方案的另一种情况是优化性能。 通过调整分区，可以控制跨计算节点和数据区域优化的数据分布，同时对整体数据流性能产生正面和负面影响。 有关详细信息，请参阅[数据流性能指南](concepts-data-flow-performance.md)。

若要更改任何转换的分区，请单击 "优化" 选项卡，然后选择 "设置分区" 单选按钮。 然后，将显示一系列用于分区的选项。 分区的最佳方法会因数据量、候选键、null 值和基数的不同而不同。 最佳做法是从默认分区开始，然后尝试不同的分区选项。 您可以使用管道调试运行进行测试，并在 "监视" 视图中查看每个转换分组中的执行时间和分区使用情况。 有关详细信息，请参阅[监视数据流](concepts-data-flow-monitoring.md)。

下面是可用的分区选项。

##### <a name="round-robin"></a>轮循机制 

轮循机制是一种简单的分区，它自动在分区之间均匀分布数据。 如果没有合理的关键候选项来实现坚实的智能分区策略，请使用轮循机制。 可以设置物理分区数目。

##### <a name="hash"></a>哈希

Azure 数据工厂将生成列的哈希来生成统一的分区，使包含类似值的行划归到同一分区。 使用“哈希”选项时，请测试是否存在分区偏斜。 可以设置物理分区数目。

##### <a name="dynamic-range"></a>动态范围

动态范围基于提供的列或表达式使用 Spark 动态范围。 可以设置物理分区数目。 

##### <a name="fixed-range"></a>固定范围

生成一个表达式，该表达式为分区数据列中的值提供固定范围。 在使用此选项之前，应充分了解数据，以避免分区歪斜。 为表达式输入的值将用作分区函数的一部分。 可以设置物理分区数目。

##### <a name="key"></a>Key

如果你已充分了解数据的基数，则键分区可能是不错的分区策略。 键分区将为列中的每个唯一值创建分区。 不能设置分区数，因为数字将基于数据中的唯一值。

#### <a name="inspect"></a>一下

"_检查_" 选项卡可用于查看要转换的数据流的元数据。 您可以查看列计数、更改的列、添加的列、数据类型、列排序和列引用。 检查是元数据的只读视图。 不需要启用调试模式即可在 "检查" 窗格中查看元数据。

![检查](media/data-flow/inspect1.png "检查")

通过转换更改数据的形状时，会看到元数据更改通过 "检查" 窗格流动。 如果源转换中没有定义的架构，则元数据将不会显示在 "检查" 窗格中。 在“架构偏差”方案中，缺少元数据是很常见的。

#### <a name="data-preview"></a>数据预览

如果调试模式为打开状态，则 "_数据预览_" 选项卡将在每次转换时提供数据的交互式快照。 有关详细信息，请参阅[调试模式下的数据预览](concepts-data-flow-debug-mode.md#data-preview)。

### <a name="top-bar"></a>顶部栏

顶部栏包含影响整个数据流（如保存和验证）的操作。 你还可以使用 "**显示关系图**" 和 "**隐藏关系**图" 按钮在关系图和配置模式间切换。

![隐藏关系图](media/data-flow/hideg.png "隐藏关系图")

如果隐藏了图形，可以通过 "**上一**步" 和 "**下一步**" 按钮在转换节点中导航。

![导航](media/data-flow/showhide.png "导航")

## <a name="next-steps"></a>后续步骤

* 了解如何创建[源转换](data-flow-source.md)
* 了解如何在[调试模式下](concepts-data-flow-debug-mode.md)生成数据流
