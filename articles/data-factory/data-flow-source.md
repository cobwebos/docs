---
title: 映射数据流中的源转换
description: 了解如何在映射数据流中设置源转换。
author: kromerm
ms.author: makromer
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/12/2019
ms.openlocfilehash: 128b15bd5b3ba3c3ac891719bf5c3ec8e5137cce
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2020
ms.locfileid: "77023508"
---
# <a name="source-transformation-in-mapping-data-flow"></a>映射数据流中的源转换 

源转换为数据流配置数据源。 在设计数据流时，第一步将始终配置源转换。 若要添加源，请在数据流画布中单击 "**添加源**" 框。

每个数据流需要至少一个源转换，但你可以根据需要添加任意多个源来完成数据转换。 您可以将这些源与联接、查找或联合转换一起联接。

每个源转换只与一个数据工厂数据集相关联。 数据集定义要写入或读取的数据的形状和位置。 如果使用基于文件的数据集，则可以使用源中的通配符和文件列表一次处理多个文件。

## <a name="supported-source-connectors-in-mapping-data-flow"></a>映射数据流中支持的源连接器

映射数据流遵循提取、加载和转换（ELT）方法，并且适用于所有 Azure 中的*临时*数据集。 当前，以下数据集可用于源转换：
    
* [Azure Blob 存储](connector-azure-blob-storage.md#mapping-data-flow-properties)（JSON、Avro、Text、Parquet）
* [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) （JSON，Avro，Text，Parquet）
* [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) （JSON，Avro，Text，Parquet）
* [Azure Synapse 分析](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties)
* [Azure SQL 数据库](connector-azure-sql-database.md#mapping-data-flow-properties)
* [Azure CosmosDB](connector-azure-cosmos-db.md#mapping-data-flow-properties)

特定于这些连接器的设置位于 "**源选项**" 选项卡中。有关这些设置的信息位于连接器文档中。 

Azure 数据工厂可访问超过[90 个本机连接器](connector-overview.md)。 若要在数据流中包含其他源中的数据，请使用复制活动将该数据加载到某个支持的暂存区域。

## <a name="source-settings"></a>源设置

添加源后，请通过 "**源设置**" 选项卡进行配置。可在此处选取或创建源指向的数据集。 还可以选择数据的架构和采样选项。

![源设置选项卡](media/data-flow/source1.png "源设置选项卡")

**测试连接：** 测试数据流的 spark 服务是否可以成功连接到源数据集中使用的链接服务。 要启用此功能，调试模式必须为 on。

**架构偏差：** [架构偏差](concepts-data-flow-schema-drift.md)是指数据工厂本机处理数据流中的灵活架构的能力，无需显式定义列更改。

* 如果源列经常更改，请选中 "**允许架构偏差**" 框。 此设置允许所有传入的源字段流过到接收器的转换。

* 选择 "**推断偏移列类型**" 会指示数据工厂检测并定义发现的每个新列的数据类型。 关闭此功能后，所有偏移列都将为字符串类型。

**验证架构：** 如果选择了 "验证架构"，则当传入的源数据与数据集的已定义架构不匹配时，数据流将无法运行。

**跳过行计数：** "跳过行计数" 字段指定在数据集的开头要忽略多少行。

**采样：** 启用采样以限制源中的行数。 当你在源中测试数据或对数据进行采样以便进行调试时，请使用此设置。

**多行行：** 如果源文本文件包含跨多行的字符串值（即值中的换行符），请选择多行行。 此设置仅可用于 DelimitedText 数据集。

若要验证是否正确配置了源，请打开调试模式并提取数据预览。 有关详细信息，请参阅[调试模式](concepts-data-flow-debug-mode.md)。

> [!NOTE]
> 当调试模式处于打开状态时，"调试" 设置中的行限制配置将覆盖数据预览期间源中的采样设置。

## <a name="projection"></a>投影

与数据集中的架构一样，源中的投影定义源数据中的数据列、类型和格式。 对于大多数数据集类型（例如 SQL 和 Parquet），源中的投影是固定的，以反映数据集中定义的架构。 如果源文件不是强类型的（例如，平面 csv 文件而不是 Parquet 文件），则可以定义源转换中每个字段的数据类型。

!["投影" 选项卡上的设置](media/data-flow/source3.png "投影")

如果文本文件没有定义的架构，请选择 "**检测数据类型**"，以便数据工厂将采样并推断数据类型。 选择 "**定义默认格式**" 以自动检测默认数据格式。

**Reset schema**将投影重置为在引用的数据集中定义的内容。

可以在流下派生列转换中修改列数据类型。 使用 select 转换来修改列名称。

### <a name="import-schema"></a>导入架构

使用 "**投影**" 选项卡上的 "**导入架构**" 按钮，可以使用活动调试群集创建架构投影。 在每个源类型中提供，在此处导入架构将覆盖数据集中定义的投影。 数据集对象将不会更改。

这在支持复杂数据结构的数据集中很有用，因为这些数据集不需要架构定义存在于数据集中。

## <a name="optimize-the-source-transformation"></a>优化源转换

在源转换的 "**优化**" 选项卡上，可能会看到**源**分区类型。 仅当你的源是 Azure SQL 数据库时，此选项才可用。 这是因为数据工厂会尝试建立并行连接，以针对 SQL 数据库源运行大型查询。

![源分区设置](media/data-flow/sourcepart3.png "partitioning")

不需要对 SQL 数据库源上的数据进行分区，但分区对于大型查询很有用。 您可以将分区基于列或查询。

### <a name="use-a-column-to-partition-data"></a>使用列对数据进行分区

从源表中，选择要进行分区的列。 同时设置分区数。

### <a name="use-a-query-to-partition-data"></a>使用查询对数据进行分区

您可以选择基于查询对连接进行分区。 输入 WHERE 谓词的内容。 例如，输入 year > 1980。

有关映射数据流中的优化的详细信息，请参阅 "[优化" 选项卡](concepts-data-flow-overview.md#optimize)。

## <a name="next-steps"></a>后续步骤

开始生成[派生列转换](data-flow-derived-column.md)和[select 转换](data-flow-select.md)。
