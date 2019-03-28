---
title: Azure 数据工厂映射数据流数据集
description: Azure 数据工厂映射数据流有特定的数据集兼容性
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/14/2019
ms.openlocfilehash: efb82c57a5620ef3eace8b39f6f27f2286202f84
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2019
ms.locfileid: "58521833"
---
# <a name="mapping-data-flow-datasets"></a>映射数据流数据集

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

数据集是一个数据工厂构造，它定义在管道中使用的数据的形状。 在数据流中，行级和列级数据需要细致的数据集定义。 在控制流管道中使用的数据集不需要具有与数据理解相同的深度。

在源和接收器的转换中使用数据流中的数据集。 它们用于定义基本数据架构。 如果数据中没有架构，则可以为源和接收器设置架构偏差。 从数据集定义架构后，将具有相关的数据类型、数据格式、文件位置和关联的链接服务的连接信息。 从数据集的元数据将显示在源转换为源"投影"。 在数据集中的架构表示的物理数据类型和形状，而在源转换投影表示数据的流表示形式具有定义的名称和类型的数据。

## <a name="dataset-types"></a>数据集类型

目前，数据流中有四种数据集类型：

* Azure SQL DB
* Azure SQL DW
* Parquet （从 ADLS 和 Blob）
* 带分隔符的文本 （从 ADLS 和 Blob）

数据流数据集单独*源类型*从*链接服务连接类型*。 通常情况下，在数据工厂中，先选择连接类型（Blob、ADLS 等），然后在数据集中定义文件的类型。 在数据流中，将选取源类型，该类型可以与不同的链接服务连接类型关联。

![源转换选项](media/data-flow/dataset1.png "源")

## <a name="data-flow-compatible-datasets"></a>数据流兼容的数据集

创建新的数据集时，面板右上方有一个标记为“数据流兼容”的复选框。 单击该按钮将仅筛选可以与数据流配合使用的数据集。 

## <a name="import-schemas"></a>导入架构

导入数据流数据集的架构时，将看到“导入架构”按钮。 单击该按钮将显示两个选项：从源导入或从本地文件导入。 在大多数情况下，将直接从源导入架构。 但是，如果有现成的架构文件（Parquet 文件或带有标头的 CSV），则可以指向该本地文件，数据工厂将根据该架构文件定义架构。

## <a name="create-new-table"></a>创建新表

在数据流中，可以让 ADF 通过接收器转换具有新的表名称中设置数据集在目标数据库中创建新的表定义。 在 SQL 数据集，表名称下面单击"编辑"并输入新的表名称。 然后，在接收器转换中，打开"允许架构偏差"。 Seth 的"导入架构"设置为无。

![源转换架构](media/data-flow/dataset2.png "SQL 架构")

## <a name="choose-your-type-of-data-first"></a>首先选择您的数据类型

### <a name="delimited-text"></a>带分隔符的文本

在带分隔符的文本数据集，您将设置要处理任一单个分隔符的分隔符 ('\t '为 TSV，'，' csv，' |'...) 或使用多个字符作为分隔符。 将标头行开关设置，然后转到要自动检测数据类型的源转换。 如果使用的接收器的分隔的文本数据集，以便将数据移入，只需选择目标文件夹。 在接收器设置中，可以定义的输出文件的名称。

### <a name="parquet"></a>Parquet

Parquet 用作首选 ADF 数据流中的临时数据集类型。 Parquet 将存储以及数据的丰富的元数据架构。

### <a name="database-types"></a>数据库类型

可以选择 Azure SQL DB 或 Azure SQL DW。

对于其他 ADF 数据集类型，使用复制活动暂存数据。 在模板库，以帮助您构建这种模式中没有的 ADF 模板。

![将暂存复制](media/data-flow/templatedf.png "复制过渡环境")

## <a name="choose-your-connection-type"></a>选择你的连接类型

如果使用 Parquet 或分隔的文本数据集，然后可以为你的数据选择位置：ADLS 或 Blob。

## <a name="next-steps"></a>后续步骤

首先[创建新的数据流动](data-flow-create.md)添加源转换。 然后配置你的源数据集。

使用[复制活动](copy-activity-overview.md)将数据导入任何 ADF 从数据源并将其暂存 ADLS 或 Blob 中的数据流的访问。

