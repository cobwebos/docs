---
title: Azure 数据工厂映射数据流“优化”选项卡
description: 优化 Azure 数据工厂映射数据流分区设置中使用优化选项卡
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 372922fff600aaa12fddfeeb86ff4522eca3e602
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67075098"
---
# <a name="mapping-data-flow-transformation-optimize-tab"></a>将数据映射到数据流转换优化选项卡

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

每个数据流转换都有一个“优化”选项卡。“优化”选项卡包含用于配置数据流分区方案的可选设置。

<img src="media/data-flow/opt001.png" width="800">

默认设置为“使用当前分区”。 当前分区指示 Azure 数据工厂使用 Azure Databricks 中 Spark 上运行的数据流的原生分区方案。 一般情况下，这是建议的做法。

但在某些情况下，你可能想要调整分区。 例如，如果你要将转换输出到湖中的单个文件，请在“优化”选项卡上选择“单个分区”，以便在接收器转换中进行分区。

另一种情况是，出于性能考虑，你可能想要对数据转换所用的分区方案实行控制。 调整数据分区可对跨计算节点的数据分布以及数据局部优化提供一定的控制度，但会对数据流的总体性能同时造成正面影响和负面影响。

若要更改任何转换的分区，只需单击“优化”选项卡并选中“设置分区”单选按钮。 然后，你会看到一系列用于分区的选项。 实施的最佳分区方法根据数据量、候选键、null 值和基数而异。 最佳做法是从默认分区着手，然后逐渐尝试不同的分区选项。 可以使用管道中的“调试”运行进行测试，然后查看每次转换分组花费的时间，以及“监视”视图中的分区使用情况。

<img src="media/data-flow/opt002.png" width="600">

### <a name="round-robin"></a>轮循机制

轮循机制是一种简单的分区，它自动在分区之间均匀分布数据。 如果没有适当的候选键用于实施可靠、智能的分区策略，请使用轮循机制。 可以设置物理分区数目。

### <a name="hash"></a>哈希

Azure 数据工厂将生成列的哈希来生成统一的分区，使包含类似值的行划归到同一分区。 使用“哈希”选项时，请测试是否存在分区偏斜。 可以设置物理分区数目。

### <a name="dynamic-range"></a>动态范围

动态范围基于提供的列或表达式使用 Spark 动态范围。 可以设置物理分区数目。 

### <a name="fixed-range"></a>固定范围

必须生成一个表达式用于在分区的数据列中提供值的固定范围。 使用此选项之前，应该充分了解你的数据，以避免分区偏斜。 为表达式输入的值将用作分区函数的一部分。 可以设置物理分区数目。

### <a name="key"></a>密钥

如果你已充分了解数据的基数，则键分区可能是不错的分区策略。 键分区将为列中的每个唯一值创建分区。 无法设置分区数目，因为该数目基于数据中的唯一值。

## <a name="next-steps"></a>后续步骤

[映射到数据流的性能指南](concepts-data-flow-performance.md)
[数据流监视](concepts-data-flow-monitoring.md)
