---
title: 映射数据流
description: 在 Azure 数据工厂中映射数据流的概述
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: references_regions
ms.date: 09/02/2020
ms.openlocfilehash: 7d73d832f96d087964c46c6c735c0385832c08db
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91370906"
---
# <a name="mapping-data-flows-in-azure-data-factory"></a>在 Azure 数据工厂中映射数据流

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="what-are-mapping-data-flows"></a>映射数据流是什么？

映射数据流是在 Azure 数据工厂中以可视方式设计的数据转换。 数据流允许数据工程师无需编写代码即可开发数据转换逻辑。 生成的数据流将作为使用向外 Apache Spark 群集的 Azure 数据工厂管道中的活动执行。 可以使用现有的 Azure 数据工厂计划、控制、流和监视功能操作化数据流活动。

映射数据流提供完全直观的体验，无需编码。 数据流在 ADF 托管的执行群集上运行，用于扩展的数据处理。 Azure 数据工厂处理所有代码转换、路径优化和数据流作业的执行。

## <a name="getting-started"></a>入门

数据流是从 "工厂资源" 窗格（如管道和数据集）创建的。 若要创建数据流，请选择 "**工厂资源**" 旁边的加号，然后选择 "**数据流"。** 

![新建数据流](media/data-flow/new-data-flow.png "新建数据流")

此操作会将你转到 "数据流" 画布，你可以在其中创建转换逻辑。 选择 " **添加源** " 开始配置源转换。 有关详细信息，请参阅 [源转换](data-flow-source.md)。

## <a name="authoring-data-flows"></a>创作数据流

映射数据流具有独特的创作画布，旨在简化生成转换逻辑。 数据流画布分为三部分：顶栏、图形和配置面板。 

![画布](media/data-flow/canvas1.png "画布")

### <a name="graph"></a>图形

关系图显示转换流。 它显示源数据流入一个或多个接收器时的沿袭。 若要添加新源，请选择 " **添加源**"。 若要添加新的转换，请选择现有转换右下方的加号。 了解更多有关如何 [管理数据流图形的](concepts-data-flow-manage-graph.md)信息。

![画布](media/data-flow/canvas2.png "画布")

### <a name="configuration-panel"></a>配置面板

"配置" 面板显示特定于当前选定转换的设置。 如果未选择任何转换，则会显示数据流。 在整个数据流配置中，你可以在 " **常规** " 选项卡下编辑名称和说明，或通过 " **参数** " 选项卡添加参数。有关详细信息，请参阅 [映射数据流参数](parameters-data-flow.md)。

每个转换至少包含四个配置选项卡。

#### <a name="transformation-settings"></a>转换设置

每个转换的配置窗格中的第一个选项卡包含特定于该转换的设置。 有关详细信息，请参阅该转换的文档页。

![源设置选项卡](media/data-flow/source1.png "源设置选项卡")

#### <a name="optimize"></a>优化

" **优化** " 选项卡包含配置分区方案的设置。 若要了解有关如何优化数据流的详细信息，请参阅 [映射数据流性能指南](concepts-data-flow-performance.md)。

![屏幕截图显示 "优化" 选项卡，其中包括分区选项、分区类型和分区数。](media/data-flow/optimize.png)

#### <a name="inspect"></a>检查

" **检查** " 选项卡可用于查看要转换的数据流的元数据。 您可以查看列计数、列更改、添加的列、数据类型、列顺序和列引用。 **检查** 是元数据的只读视图。 不需要启用调试模式即可在 " **检查** " 窗格中查看元数据。

![检查](media/data-flow/inspect1.png "检查")

通过转换更改数据形状时，" **检查** " 窗格中会显示元数据更改流。 如果源转换中没有定义的架构，则元数据将不会显示在 " **检查** " 窗格中。 缺少元数据在架构偏移方案中很常见。

#### <a name="data-preview"></a>数据预览

如果调试模式为打开状态，则 " **数据预览** " 选项卡将在每次转换时提供数据的交互式快照。 有关详细信息，请参阅 [调试模式下的数据预览](concepts-data-flow-debug-mode.md#data-preview)。

### <a name="top-bar"></a>上栏

顶部栏包含影响整个数据流的操作，如保存和验证。 您还可以查看转换逻辑的基础 JSON 代码和数据流脚本。 有关详细信息，请参阅 [数据流脚本](data-flow-script.md)。

## <a name="available-transformations"></a>可用转换

查看 [映射数据流转换概述](data-flow-transformation-overview.md) 以获取可用转换的列表。

## <a name="data-flow-activity"></a>数据流活动

使用数据流 [活动](control-flow-execute-data-flow-activity.md)在 ADF 管道内操作化映射数据流。 用户需要做的就是指定要使用的集成运行时并传入参数值。 有关详细信息，请参阅 [Azure 集成运行时](concepts-integration-runtime.md#azure-integration-runtime)。

## <a name="debug-mode"></a>调试模式

调试模式允许您在生成和调试数据流时以交互方式查看每个转换步骤的结果。 生成数据流逻辑和运行包含数据流活动的管道调试运行时，可以在中使用调试会话。 若要了解详细信息，请参阅 [调试模式文档](concepts-data-flow-debug-mode.md)。

## <a name="monitoring-data-flows"></a>监视数据流

映射数据流与现有的 Azure 数据工厂监视功能集成。 若要了解如何了解数据流监视输出，请参阅 [监视映射数据流](concepts-data-flow-monitoring.md)。

Azure 数据工厂团队已经创建了 [性能优化指南](concepts-data-flow-performance.md) ，可帮助您在生成业务逻辑之后优化数据流的执行时间。

## <a name="available-regions"></a>可用区域

可在以下区域中使用映射数据流：

| Azure 区域 | ADF 中的数据流 | Synapse Studio 中的数据流 |
| ------------ | ----------------- | ---------------------------- |
|  澳大利亚中部 | | |  
| 澳大利亚中部 2 | | |
| 澳大利亚东部 | ✓ |  ✓ |
| Australia Southeast   | ✓ | ✓ |
| Brazil South  | ✓ |  |
| 加拿大中部 | ✓ |  |
| 印度中部 | ✓ |   ✓ |
| 美国中部    | ✓ |   ✓ |
| 中国东部 |      | ✓ |
| 中国东部 2  |   |    |
| 中国非区域 | | |
| 中国北部 |     | |
| 中国北部 2 | |  |
| 东亚 | ✓ | |
| 美国东部   | ✓ | ✓ |
| 美国东部 2 | ✓ | ✓ |
| 法国中部 | ✓ | ✓ |
| 法国南部  | | |
| 德国中部 (主权)  | | |
| 德国（非区域性） (主权)  | | |
|  (公用) 德国北部 | | |
| 德国东北部 (主权)  | | |
|  (公用) 德国中西部 |  | ✓ |
| Japan East | ✓ |  |
| 日本西部 |  | |
| 韩国中部 | ✓ |  |
| 韩国南部 | | |
| 美国中北部  | ✓ | ✓ |
| 北欧  | ✓ |    |
| 挪威东部 | | |
| 挪威西部 | | |
| 南非北部    | ✓ | |
| 南非西部 |  |    |
| 美国中南部  | | ✓ |
| 印度南部 | | |
| Southeast Asia    | ✓ | ✓ |
| 瑞士北部 |   |  |
| 瑞士西部 | | |
| 阿联酋中部 | | |
| 阿拉伯联合酋长国北部 |  |    |
| 英国南部  | ✓ |   | ✓ |
| 英国西部 |     | ✓ |
| US DoD 中部 | |  |
| US DoD 东部 | |  |
| US Gov 亚利桑那州 |      |  |
| US Gov（非区域性） | |  |
| US Gov 德克萨斯州 | |  |
| US Gov 弗吉尼亚州 |     |  |
| 美国中西部 |     | ✓ |
| 西欧   | ✓ |   ✓ |
| 印度西部 | | |
| 美国西部   | ✓ |   |
| 美国西部 2 | ✓ |   ✓ | 

## <a name="next-steps"></a>后续步骤

* 了解如何创建 [源转换](data-flow-source.md)。
* 了解如何在 [调试模式下](concepts-data-flow-debug-mode.md)生成数据流。
