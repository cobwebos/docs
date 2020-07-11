---
title: 映射数据流中的接收器转换
description: 了解如何在映射数据流中配置接收器转换。
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 06/03/2020
ms.openlocfilehash: 49cfc4899379698cab78a5e22fcffacb60636052
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2020
ms.locfileid: "86223629"
---
# <a name="sink-transformation-in-mapping-data-flow"></a>映射数据流中的接收器转换

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

转换完数据后，使用接收器转换将数据写入目标存储。 每个数据流都需要至少一个接收器转换，但你可以根据需要写入任意多个接收器来完成转换流。 若要写入其他接收器，请通过新的分支和条件拆分创建新的流。

每个接收器转换只与一个 Azure 数据工厂数据集对象或链接服务相关联。 接收器转换确定要写入的数据的形状和位置。

## <a name="inline-datasets"></a>内联数据集

创建接收器转换时，请选择是否在 dataset 对象内或接收器转换内定义接收器信息。 大多数格式仅可用于其中一种格式。 若要了解如何使用特定的连接器，请参阅相应的连接器文档。

当 inline 和 dataset 对象同时支持格式时，这两种方法都有好处。 数据集对象是可重复使用的实体，可在其他数据流和活动（如复制）中利用。 当使用强化的架构时，这些方法特别有用。 数据集并不基于 Spark，有时你可能需要在接收器转换中替代某些设置或架构投影。

使用灵活的架构、一次性接收器实例或参数化接收器时，建议使用内联数据集。 如果接收器的参数化很高，则行内数据集允许你不创建 "虚拟" 对象。 内联数据集基于 spark，其属性是流的本机属性。

若要使用内联数据集，请在**接收器类型**选择器中选择所需的格式。 选择要连接到的链接服务，而不是选择接收器数据集。

![内联数据集](media/data-flow/inline-selector.png "内联数据集")

##  <a name="supported-sink-types"></a><a name="supported-sinks"></a>支持的接收器类型

映射数据流遵循提取、加载、转换 (ELT) 方法，并适用于所有 Azure 中的*临时*数据集。 当前，以下数据集可用于源转换：

| 连接器 | 格式 | 数据集/内联 |
| --------- | ------ | -------------- |
| [Azure Blob 存储](connector-azure-blob-storage.md#mapping-data-flow-properties) | [JSON](format-json.md#mapping-data-flow-properties) <br> [Avro](format-avro.md#mapping-data-flow-properties) <br> [带分隔符的文本](format-delimited-text.md#mapping-data-flow-properties) <br> [增量 (预览) ](format-delta.md) <br> [Parquet](format-parquet.md#mapping-data-flow-properties) | ✓/- <br> ✓/- <br> ✓/- <br> -/✓ <br> ✓/- |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) | [JSON](format-json.md#mapping-data-flow-properties) <br> [Avro](format-avro.md#mapping-data-flow-properties) <br> [带分隔符的文本](format-delimited-text.md#mapping-data-flow-properties) <br> [Parquet](format-parquet.md#mapping-data-flow-properties)  | ✓/- <br> ✓/- <br> ✓/- <br> ✓/- |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) | [JSON](format-json.md#mapping-data-flow-properties) <br> [Avro](format-avro.md#mapping-data-flow-properties) <br> [带分隔符的文本](format-delimited-text.md#mapping-data-flow-properties) <br> [增量 (预览) ](format-delta.md) <br> [Parquet](format-parquet.md#mapping-data-flow-properties)  <br> [通用数据模型 (预览) ](format-common-data-model.md#sink-properties) | ✓/- <br> ✓/- <br> ✓/- <br> -/✓ <br> ✓/- <br> -/✓ |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties) | | ✓/- |
| [Azure SQL 数据库](connector-azure-sql-database.md#mapping-data-flow-properties) | | ✓/- |
| [Azure CosmosDB (SQL API) ](connector-azure-cosmos-db.md#mapping-data-flow-properties) | | ✓/- |

特定于这些连接器的设置位于 "**设置**" 选项卡中。有关这些设置的信息和数据流脚本示例位于连接器文档中。 

Azure 数据工厂可以访问 [90 多个原生连接器](connector-overview.md)。 若要将数据从数据流写入其他源，请使用复制活动从支持的接收器加载数据。

## <a name="sink-settings"></a>接收器设置

添加接收器后，通过 "**接收器**" 选项卡进行配置。可在此处选取或创建接收器写入的数据集。 以下视频介绍了几种用于文本分隔文件类型的不同接收器选项：

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4tf7T]

![接收器设置](media/data-flow/sink-settings.png "接收器设置")

**架构偏差：** [架构偏差](concepts-data-flow-schema-drift.md)是指数据工厂本机处理数据流中的灵活架构的能力，无需显式定义列更改。 启用 "**允许架构偏移**" 可以在接收器数据架构中定义的内容的基础上写入其他列。

**验证架构：** 如果选择了 "验证架构"，则在源投影中找不到传入源架构的任何列或数据类型不匹配时，数据流将失败。 使用此设置可强制源数据满足定义投影的协定。 它在数据库源方案中非常有用，用来指示列名称或类型已更改。

## <a name="field-mapping"></a>字段映射

与选择转换类似，在接收器的 "**映射**" 选项卡中，您可以决定写入哪些传入列。 默认情况下，映射所有输入列，包括偏移列。 这就是所谓的**自动映射**。

关闭自动映射时，可以选择添加基于列的固定映射或基于规则的映射。 基于规则的映射允许您编写具有模式匹配的表达式，而固定映射将映射逻辑列名和物理列名。 有关基于规则的映射的详细信息，请参阅[映射数据流中的列模式](concepts-data-flow-column-pattern.md#rule-based-mapping-in-select-and-sink)。

## <a name="custom-sink-ordering"></a>自定义接收器排序

默认情况下，数据按非确定性顺序写入多个接收器。 当转换逻辑完成并且接收顺序可能因每次运行而异时，执行引擎将并行写入数据。 若要指定和确切的接收顺序，请在数据流的 "常规" 选项卡中启用**自定义接收器排序**。 启用后，将按递增顺序写入接收器。

![自定义接收器排序](media/data-flow/custom-sink-ordering.png "自定义接收器排序")

## <a name="data-preview-in-sink"></a>接收器中的数据预览

在调试群集上提取数据预览时，不会将任何数据写入接收器。 将返回数据的外观的快照，但不会向目标写入任何内容。 若要测试将数据写入接收器，请从管道画布运行管道调试。

## <a name="next-steps"></a>后续步骤
创建数据流后，请将数据流[活动添加到管道](concepts-data-flow-overview.md)。
