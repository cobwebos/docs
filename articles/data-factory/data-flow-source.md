---
title: 映射数据流中的源转换
description: 了解如何在映射数据流中设置源转换。
author: kromerm
ms.author: makromer
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/27/2020
ms.openlocfilehash: 22f524ed4f01ad5b3be110008a82cd31c5c476c5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91827855"
---
# <a name="source-transformation-in-mapping-data-flow"></a>映射数据流中的源转换 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

源转换为数据流配置数据源。 在设计数据流时，第一步将始终配置源转换。 若要添加源，请在数据流画布中单击 " **添加源** " 框。

每个数据流需要至少一个源转换，但你可以根据需要添加任意多个源来完成数据转换。 您可以将这些源与联接、查找或联合转换一起联接。

每个源转换只与一个数据集或链接服务相关联。 数据集定义要写入或读取的数据的形状和位置。 如果使用基于文件的数据集，则可以使用源中的通配符和文件列表一次处理多个文件。

## <a name="inline-datasets"></a>内联数据集

创建源转换时所做的第一次决定是在 dataset 对象内还是在源转换内定义源信息。 大多数格式仅可用于其中一种格式。 若要了解如何使用特定的连接器，请参阅相应的连接器文档。

当 inline 和 dataset 对象同时支持格式时，这两种方法都有好处。 数据集对象是可重复使用的实体，可在其他数据流和活动（如复制）中利用。 当使用强化的架构时，这些方法特别有用。 数据集并不基于 Spark，有时你可能需要在源转换中替代某些设置或架构投影。

使用灵活的架构、一次性源实例或参数化源时，建议使用内联数据集。 如果源的数据集很大，则行内数据集允许你不创建 "虚拟" 对象。 内联数据集基于 spark，其属性是流的本机属性。

若要使用内联数据集，请在 " **源类型** 选择器" 中选择所需的格式。 不选择源数据集，而是选择要连接到的链接服务。

![内联数据集](media/data-flow/inline-selector.png "内联数据集")

##  <a name="supported-source-types"></a><a name="supported-sources"></a> 支持的源类型

映射数据流遵循提取、加载、转换 (ELT) 方法，并适用于所有 Azure 中的 *临时* 数据集。 当前，以下数据集可用于源转换：

| 连接器 | 格式 | 数据集/内联 |
| --------- | ------ | -------------- |
| [Azure Blob 存储](connector-azure-blob-storage.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties)<br>[带分隔符的文本](format-delimited-text.md#mapping-data-flow-properties)<br>[增量 (预览) ](format-delta.md)<br>[Excel](format-excel.md#mapping-data-flow-properties)<br>[JSON](format-json.md#mapping-data-flow-properties) <br>[ORC](format-orc.md#mapping-data-flow-properties)<br/>[Parquet](format-parquet.md#mapping-data-flow-properties)<br>[XML](format-xml.md#mapping-data-flow-properties) | ✓/-<br>✓/-<br>-/✓<br>✓/✓<br/>✓/-<br>✓/✓<br/>✓/-<br>✓/✓ |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties)<br>[带分隔符的文本](format-delimited-text.md#mapping-data-flow-properties)<br>[Excel](format-excel.md#mapping-data-flow-properties)<br>[JSON](format-json.md#mapping-data-flow-properties)<br>[ORC](format-orc.md#mapping-data-flow-properties)<br/>[Parquet](format-parquet.md#mapping-data-flow-properties)<br>[XML](format-xml.md#mapping-data-flow-properties) | ✓/-<br>✓/-<br>✓/✓<br/>✓/-<br>✓/✓<br/>✓/-<br>✓/✓ |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties)<br>[通用数据模型 (预览) ](format-common-data-model.md#source-properties)<br>[带分隔符的文本](format-delimited-text.md#mapping-data-flow-properties)<br>[增量 (预览) ](format-delta.md)<br>[Excel](format-excel.md#mapping-data-flow-properties)<br>[JSON](format-json.md#mapping-data-flow-properties)<br>[ORC](format-orc.md#mapping-data-flow-properties)<br/>[Parquet](format-parquet.md#mapping-data-flow-properties)<br>[XML](format-xml.md#mapping-data-flow-properties) | ✓/-<br/>-/✓<br>✓/-<br>-/✓<br>✓/✓<br>✓/-<br/>✓/✓<br/>✓/-<br>✓/✓ |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties) | | ✓/- |
| [Azure SQL 数据库](connector-azure-sql-database.md#mapping-data-flow-properties) | | ✓/- |
| [Azure Cosmos DB (SQL API)](connector-azure-cosmos-db.md#mapping-data-flow-properties) | | ✓/- |
| [Snowflake](connector-snowflake.md) | | ✓/✓ |

特定于这些连接器的设置位于 " **源选项** " 选项卡中。有关这些设置的信息和数据流脚本示例位于连接器文档中。 

Azure 数据工厂可以访问 [90 多个原生连接器](connector-overview.md)。 若要在数据流中包含其他源中的数据，请使用复制活动将该数据加载到某个支持的暂存区域。

## <a name="source-settings"></a>源设置

添加源后，请通过 " **源设置** " 选项卡进行配置。可在此处选取或创建源指向的数据集。 还可以选择数据的架构和采样选项。 

可以在 " [调试" 设置](concepts-data-flow-debug-mode.md) 中配置数据集参数的开发值 (需要打开调试模式) 。

![源设置选项卡](media/data-flow/source1.png "源设置选项卡")

**输出流名称：** 源转换的名称。

**源类型：** 选择是要使用内联数据集还是现有数据集对象。

**测试连接：** 测试数据流的 spark 服务是否可以成功连接到源数据集中使用的链接服务。 要启用此功能，调试模式必须为 on。

**架构偏差：** [架构偏差](concepts-data-flow-schema-drift.md) 是指数据工厂本机处理数据流中的灵活架构的能力，无需显式定义列更改。

* 如果源列经常更改，请选中 " **允许架构偏差** " 框。 此设置允许所有传入的源字段流过到接收器的转换。

* 选择 " **推断偏移列类型** " 会指示数据工厂检测并定义发现的每个新列的数据类型。 关闭此功能后，所有偏移列都将为字符串类型。

**验证架构：** 如果选择了 "验证架构"，则当传入的源数据与数据集的已定义架构不匹配时，数据流将无法运行。

**跳过行计数：** "跳过行计数" 字段指定在数据集的开头要忽略多少行。

**采样：** 启用采样以限制源中的行数。 当你在源中测试数据或对数据进行采样以便进行调试时，请使用此设置。

若要验证是否正确配置了源，请打开调试模式并提取数据预览。 有关详细信息，请参阅 [调试模式](concepts-data-flow-debug-mode.md)。

> [!NOTE]
> 当调试模式处于打开状态时，"调试" 设置中的行限制配置将覆盖数据预览期间源中的采样设置。

## <a name="source-options"></a>源选项

"源选项" 选项卡包含特定于所选连接器和格式的设置。 有关详细信息和示例，请参阅相关 [连接器文档](#supported-sources)。

## <a name="projection"></a>投影

与数据集中的架构一样，源中的投影定义源数据中的数据列、类型和格式。 对于大多数数据集类型（例如 SQL 和 Parquet），源中的投影是固定的，以反映数据集中定义的架构。 如果源文件不是强类型的 (例如，平面 csv 文件而不是 Parquet files) ，则可以定义源转换中每个字段的数据类型。

!["投影" 选项卡上的设置](media/data-flow/source3.png "投影")

如果文本文件没有定义的架构，请选择 " **检测数据类型** "，以便数据工厂将采样并推断数据类型。 选择 " **定义默认格式** " 以自动检测默认数据格式。

**Reset schema** 将投影重置为在引用的数据集中定义的内容。

可以在流下派生列转换中修改列数据类型。 使用 select 转换来修改列名称。

### <a name="import-schema"></a>导入架构

使用 "**投影**" 选项卡上的 "**导入架构**" 按钮，可以使用活动调试群集创建架构投影。 在每个源类型中提供，在此处导入架构将覆盖数据集中定义的投影。 数据集对象将不会更改。

这在支持复杂数据结构的数据集（如 Avro 和 Azure Cosmos DB）中非常有用，不需要架构定义存在于数据集中。 对于内联数据集，这是引用列元数据而不会有架构偏移的唯一方法。

## <a name="optimize-the-source-transformation"></a>优化源转换

" **优化** " 选项卡允许在每个转换步骤中编辑分区信息。 在大多数情况下， **使用当前分区** 会优化源的理想分区结构。

如果要从 Azure SQL 数据库源读取数据，自定义 **源** 分区可能会以最快的速度读取数据。 ADF 通过建立与数据库的并行连接来读取大型查询。 可以对列或使用查询执行此源分区。

![源分区设置](media/data-flow/sourcepart3.png "partitioning")

有关映射数据流中的优化的详细信息，请参阅 " [优化" 选项卡](concepts-data-flow-overview.md#optimize)。

## <a name="next-steps"></a>后续步骤

开始使用 [派生列转换](data-flow-derived-column.md) 和 [select 转换](data-flow-select.md)生成数据流。
