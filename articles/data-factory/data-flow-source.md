---
title: 映射数据流的源转换
description: 了解如何在映射数据流中设置源转换。
author: kromerm
ms.author: makromer
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/12/2019
ms.openlocfilehash: 128b15bd5b3ba3c3ac891719bf5c3ec8e5137cce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023508"
---
# <a name="source-transformation-in-mapping-data-flow"></a>映射数据流的源转换 

源转换为数据流配置数据源。 设计数据流时，您的第一步将始终配置源转换。 要添加源，请单击数据流画布中的"**添加源**"框。

每个数据流至少需要一个源转换，但您可以根据需要添加尽可能多的源来完成数据转换。 您可以将这些源与联接、查找或联合转换联接在一起。

每个源转换都与一个数据工厂数据集关联。 数据集定义要写入或读取的数据的形状和位置。 如果使用基于文件的数据集，则可以使用源中的通配符和文件列表一次处理多个文件。

## <a name="supported-source-connectors-in-mapping-data-flow"></a>映射数据流中支持源连接器

映射数据流遵循数据提取、加载、转换 （ELT） 方法，并适用于 Azure 中所有*暂存*数据集。 目前，以下数据集可用于源转换：
    
* [Azure Blob 存储](connector-azure-blob-storage.md#mapping-data-flow-properties)（JSON、Avro、文本、镶木地板）
* [Azure 数据存储湖存储第 1 代](connector-azure-data-lake-store.md#mapping-data-flow-properties)（JSON、Avro、文本、镶木地板）
* [Azure 数据存储第 2 代](connector-azure-data-lake-storage.md#mapping-data-flow-properties)（JSON、Avro、文本、镶木地板）
* [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties)
* [Azure SQL 数据库](connector-azure-sql-database.md#mapping-data-flow-properties)
* [Azure 宇宙DB](connector-azure-cosmos-db.md#mapping-data-flow-properties)

特定于这些连接器的设置位于 **"源"选项**选项卡中。有关这些设置的信息位于连接器文档中。 

Azure 数据工厂可以访问[90 多个本机连接器](connector-overview.md)。 要将数据包括来自这些其他源的数据，请使用复制活动将数据加载到受支持的过渡区域之一。

## <a name="source-settings"></a>源设置

添加源后，请通过 **"源设置"** 选项卡进行配置。在这里，您可以选取或创建源点的数据集。 您还可以为数据选择架构和采样选项。

![源设置选项卡](media/data-flow/source1.png "源设置选项卡")

**测试连接：** 测试数据流的 spark 服务是否可以成功连接到源数据集中使用的链接服务。 必须启用调试模式才能启用此功能。

**架构漂移：**[架构漂移](concepts-data-flow-schema-drift.md)是数据工厂在无需显式定义列更改的情况下，在数据流中本机处理灵活架构的能力。

* 如果源列经常更改，请选中 **"允许架构漂移**框"。 此设置允许所有传入源字段通过转换流向接收器。

* 选择**推断漂移列类型**将指示数据工厂检测和定义发现的每个新列的数据类型。 关闭此功能后，所有漂移列都将为类型字符串。

**验证架构：** 如果选择了验证架构，则如果传入源数据与数据集的已定义架构不匹配，则数据流将无法运行。

**跳过行数：** 跳过行计数字段指定数据集开始时要忽略的行数。

**抽样：** 启用采样以限制源中的行数。 出于调试目的，在测试或从源中测试数据或采样数据时，请使用此设置。

**多行行：** 如果源文本文件包含跨越多个行（即值内的新线）的字符串值，请选择多行。 此设置仅在分隔文本数据集中可用。

要验证源配置正确，请打开调试模式并获取数据预览。 有关详细信息，请参阅[调试模式](concepts-data-flow-debug-mode.md)。

> [!NOTE]
> 启用调试模式时，调试设置中的行限制配置将在数据预览期间覆盖源中的采样设置。

## <a name="projection"></a>投影

与数据集中的架构一样，源中的投影从源数据定义数据列、类型和格式。 对于大多数数据集类型（如 SQL 和 Parquet），源中的投影是固定的，以反映在数据集中定义的架构。 当源文件未进行强类型时（例如，平面 csv 文件而不是 Parquet 文件），则可以在源转换中定义每个字段的数据类型。

!["投影"选项卡上的设置](media/data-flow/source3.png "投影")

如果文本文件没有定义的架构，请选择 **"检测数据类型**"，以便数据工厂对数据类型进行采样和推断。 选择 **"定义默认格式**"以自动检测默认数据格式。

**重置架构**将投影重置为引用的数据集中定义的内容。

您可以在下游派生列转换中修改列数据类型。 使用选择转换修改列名称。

### <a name="import-schema"></a>导入架构

**"投影"** 选项卡上的 **"导入架构"** 按钮允许您使用活动调试群集创建架构投影。 每个源类型都可用，在此处导入架构将覆盖数据集中定义的投影。 不会更改数据集对象。

这在支持复杂数据结构的 Avro 和 CosmosDB 等数据集中非常有用，不需要在数据集中存在架构定义。

## <a name="optimize-the-source-transformation"></a>优化源转换

在源转换的 **"优化**"选项卡上，您可能会看到**源**分区类型。 仅当源是 Azure SQL 数据库时，此选项才可用。 这是因为数据工厂尝试使连接并行以针对 SQL 数据库源运行大型查询。

![源分区设置](media/data-flow/sourcepart3.png "partitioning")

不必在 SQL 数据库源上对数据进行分区，但分区对于大型查询很有用。 可以将分区基于列或查询。

### <a name="use-a-column-to-partition-data"></a>使用列对数据进行分区

从源表中选择要分区的列。 还设置分区数。

### <a name="use-a-query-to-partition-data"></a>使用查询对数据进行分区

您可以选择根据查询对连接进行分区。 输入 WHERE 谓词的内容。 例如，输入 1980 >年份。

有关映射数据流中的优化的详细信息，请参阅[优化选项卡](concepts-data-flow-overview.md#optimize)。

## <a name="next-steps"></a>后续步骤

开始构建[派生列转换](data-flow-derived-column.md)和[选择转换](data-flow-select.md)。
