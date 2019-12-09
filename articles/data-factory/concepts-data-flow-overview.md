---
title: 映射数据流
description: 在 Azure 数据工厂中映射数据流的概述
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/7/2019
ms.openlocfilehash: 397ecdb805f0be9f374c53ae7128f806bfb789d3
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928288"
---
# <a name="what-are-mapping-data-flows"></a>映射数据流是什么？

映射数据流是在 Azure 数据工厂中以可视方式设计的数据转换。 数据流允许数据工程师开发图形数据转换逻辑，而无需编写代码。 生成的数据流将作为使用扩展 Spark 群集的 Azure 数据工厂管道中的活动执行。 数据流活动可以通过现有的数据工厂计划、控制、流和监视功能来操作化。

映射数据流提供完全直观的体验，无需编码。 数据流将在自己的执行群集上运行，以便进行扩展的数据处理。 Azure 数据工厂处理所有代码转换、路径优化和数据流作业的执行。

## <a name="getting-started"></a>入门

若要创建数据流，请选择 "**工厂资源**" 下的加号，然后选择 **"数据流"。** 

![新建数据流](media/data-flow/newdataflow2.png "新建数据流")

这会将你转到可在其中创建转换逻辑的数据流画布。 选择 "**添加源**" 开始配置源转换。 有关详细信息，请参阅[源转换](data-flow-source.md)。

## <a name="data-flow-canvas"></a>数据流画布

数据流画布分为三部分：顶栏、图形和配置面板。 

![画布](media/data-flow/canvas1.png "画布")

### <a name="graph"></a>图形

关系图显示转换流。 它显示源数据流入一个或多个接收器时的沿袭。 若要添加新源，请选择 "**添加源**"。 若要添加新的转换，请选择现有转换右下方的加号。

![画布](media/data-flow/canvas2.png "画布")

### <a name="azure-integration-runtime-data-flow-properties"></a>Azure 集成运行时数据流属性

![调试按钮](media/data-flow/debugbutton.png "“调试”按钮")

开始在 ADF 中处理数据流时，需要为浏览器 UI 顶部的数据流启用 "调试" 开关。 这会加速用于交互式调试、数据预览和管道调试执行的 Azure Databricks 群集。 可以通过选择自定义[Azure Integration Runtime](concepts-integration-runtime.md)来设置使用的群集的大小。 上次数据预览或上次调试管道执行后，调试会话将保持活动状态长达60分钟。

当你使用数据流活动操作管道时，ADF 将使用与 "运行方式" 属性中的[活动](control-flow-execute-data-flow-activity.md)相关联的 Azure Integration Runtime。

默认 Azure Integration Runtime 是一个小型四核单辅助角色节点群集，旨在允许您预览数据并快速执行最小成本的调试管道。 如果要对大型数据集执行操作，请设置较大的 Azure IR 配置。

可以通过在 "Azure IR 数据流" 属性中设置 TTL 来指示 ADF 维护群集资源池（Vm）。 这将导致后续活动的执行速度更快。

#### <a name="azure-integration-runtime-and-data-flow-strategies"></a>Azure 集成运行时和数据流策略

##### <a name="execute-data-flows-in-parallel"></a>并行执行数据流

如果并行执行管道中的数据流，则 ADF 会根据附加到每个活动的 Azure Integration Runtime 中的设置，为每个活动执行开启单独 Azure Databricks 的群集。 若要在 ADF 管道中设计并行执行，请在 UI 中添加无优先约束的数据流活动。

在这三个选项中，此选项可能会在最短时间内执行。 但是，将在单独的群集上同时执行每个并行数据流，因此事件的顺序是不确定的。

##### <a name="overload-single-data-flow"></a>重载单一数据流

如果将所有逻辑都置于单个数据流中，则 ADF 将在单个 Spark 群集实例上的同一作业执行上下文中执行。

此选项可能更难执行并排除故障，因为你的业务规则和业务逻辑将混杂在一起。 此选项也不能提高可用性。

##### <a name="execute-data-flows-serially"></a>按顺序执行数据流

如果在管道中执行序列中的数据流活动，并在 Azure IR 配置上设置了 TTL，则 ADF 会重复使用计算资源（Vm），从而加快后续执行时间。 对于每次执行，你仍然会收到新的 Spark 上下文。

对于这三个选项，这可能需要最长的时间来执行端到端。 但它确实提供了每个数据流步骤中逻辑操作的完全分离。

### <a name="configuration-panel"></a>配置面板

"配置" 面板显示特定于当前选定转换的设置。 如果未选择任何转换，则会显示数据流。 在整个数据流配置中，你可以在 "**常规**" 选项卡下编辑名称和说明，或通过 "**参数**" 选项卡添加参数。有关详细信息，请参阅[映射数据流参数](parameters-data-flow.md)。

每个转换至少具有四个配置选项卡。

#### <a name="transformation-settings"></a>转换设置

每个转换的配置窗格中的第一个选项卡包含特定于该转换的设置。 有关详细信息，请参阅该转换的文档页。

![源设置选项卡](media/data-flow/source1.png "源设置选项卡")

#### <a name="optimize"></a>优化

"**优化**" 选项卡包含配置分区方案的设置。

![优化](media/data-flow/optimize1.png "优化")

默认设置为 "**使用当前分区**"，指示 Azure 数据工厂使用对 Spark 上运行的数据流的本机分区方案。 在大多数情况下，建议采用此设置。

在某些情况下，你可能需要调整分区。 例如，如果想要将转换输出到 lake 中的单个文件，请在接收器转换中选择 "**单个分区**"。

您可能想要控制分区方案的另一种情况是优化性能。 通过调整分区，可以控制跨计算节点和数据区域优化的数据分布，同时对整体数据流性能产生正面和负面影响。 有关详细信息，请参阅[数据流性能指南](concepts-data-flow-performance.md)。

若要更改任何转换的分区，请选择 "**优化**" 选项卡，然后选择 "**设置分区**" 单选按钮。 然后，将显示一系列用于分区的选项。 分区的最佳方法会因数据量、候选键、null 值和基数的不同而不同。 

最佳做法是从默认分区开始，然后尝试不同的分区选项。 您可以使用管道调试运行进行测试，并在 "监视" 视图中查看每个转换分组中的执行时间和分区使用情况。 有关详细信息，请参阅[监视数据流](concepts-data-flow-monitoring.md)。

以下分区选项可用。

##### <a name="round-robin"></a>轮循机制 

轮循机制是一种简单的分区，可跨分区均匀地分布数据。 如果没有合理的关键候选项来实现坚实的智能分区策略，请使用轮循机制。 可以设置物理分区数目。

##### <a name="hash"></a>哈希

Azure 数据工厂将生成列的哈希来生成统一的分区，使包含类似值的行划归到同一分区。 使用 Hash 选项时，请测试可能的分区偏差。 可以设置物理分区数目。

##### <a name="dynamic-range"></a>动态范围

动态范围将根据你提供的列或表达式使用 Spark 动态范围。 可以设置物理分区数目。 

##### <a name="fixed-range"></a>固定范围

生成一个表达式，该表达式为分区数据列中的值提供固定范围。 若要避免分区歪斜，应在使用此选项之前对数据有充分的了解。 为表达式输入的值将用作分区函数的一部分。 可以设置物理分区数目。

##### <a name="key"></a>密钥

如果您对数据的基数有充分了解，键分区可能是一个不错的策略。 键分区将为列中的每个唯一值创建分区。 不能设置分区数，因为数字将基于数据中的唯一值。

#### <a name="inspect"></a>检查

"**检查**" 选项卡可用于查看要转换的数据流的元数据。 您可以查看列计数、更改的列、添加的列、数据类型、列排序和列引用。 **检查**是元数据的只读视图。 不需要启用调试模式即可在 "**检查**" 窗格中查看元数据。

![检查](media/data-flow/inspect1.png "检查")

通过转换更改数据形状时，"**检查**" 窗格中会显示元数据更改流。 如果源转换中没有定义的架构，则元数据将不会显示在 "**检查**" 窗格中。 缺少元数据在架构偏移方案中很常见。

#### <a name="data-preview"></a>数据预览

如果调试模式为打开状态，则 "**数据预览**" 选项卡将在每次转换时提供数据的交互式快照。 有关详细信息，请参阅[调试模式下的数据预览](concepts-data-flow-debug-mode.md#data-preview)。

### <a name="top-bar"></a>上栏

顶部栏包含影响整个数据流的操作，如保存和验证。 还可以通过使用 "**显示关系图**" 和 "**隐藏关系**图" 按钮，在关系图和配置模式间切换。

![隐藏关系图](media/data-flow/hideg.png "隐藏图形")

如果隐藏了图形，可以通过 "**上一**步" 和 "**下一步**" 按钮，通过横向浏览转换节点。

![上一个和下一个按钮](media/data-flow/showhide.png "上一个和下一个按钮")

## <a name="next-steps"></a>后续步骤

* 了解如何创建[源转换](data-flow-source.md)。
* 了解如何在[调试模式下](concepts-data-flow-debug-mode.md)生成数据流。
