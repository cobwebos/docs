---
title: Azure 数据工厂映射数据流数据集
description: Azure 数据工厂映射数据流有 sepecific 数据集兼容性
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/14/2019
ms.openlocfilehash: ad6cfdad519ab3901c58979970ea07439b3106e9
ms.sourcegitcommit: 235cd1c4f003a7f8459b9761a623f000dd9e50ef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2019
ms.locfileid: "57726917"
---
# <a name="mapping-data-flow-datasets"></a>映射数据流数据集

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

数据集是一个数据工厂构造，它定义在管道中使用的数据的形状。 在数据流中，行级和列级数据需要细致的数据集定义。 在控制流管道中使用的数据集不需要具有与数据理解相同的深度。

在源和接收器的转换中使用数据流中的数据集。 它们用于定义基本数据架构。 如果数据中没有架构，则可以为源和接收器设置架构偏差。 从数据集定义架构后，将具有相关的数据类型、数据格式、文件位置和关联的链接服务的连接信息。 从数据集的元数据将显示在源转换为源"投影"。 在数据集中的架构表示的物理数据类型和形状，而在源转换投影表示数据的流表示形式具有定义的名称和类型的数据。

## <a name="dataset-types"></a>数据集类型

目前，数据流中有四种数据集类型：

* Azure SQL 数据库
* Azure SQL DW
* Parquet （从 ADLS 和 Blob）
* 带分隔符的文本 （从 ADLS 和 Blob）

数据流数据集单独*源类型*从*链接服务连接类型*。 通常情况下，在数据工厂中，先选择连接类型（Blob、ADLS 等），然后在数据集中定义文件的类型。 在数据流中，将选取源类型，该类型可以与不同的链接服务连接类型关联。

![源转换选项](media/data-flow/dataset1.png "源")

## <a name="data-flow-compatible-datasets"></a>数据流兼容的数据集

创建新的数据集时，面板右上方有一个标记为“数据流兼容”的复选框。 单击该按钮将仅筛选可以与数据流配合使用的数据集。 

## <a name="import-schemas"></a>导入架构

导入数据流数据集的架构时，将看到“导入架构”按钮。 单击该按钮将显示两个选项：从源导入或从本地文件导入。 在大多数情况下，将直接从源导入架构。 但是，如果有现成的架构文件（Parquet 文件或带有标头的 CSV），则可以指向该本地文件，数据工厂将根据该架构文件定义架构。

## <a name="delimited-text-dataset"></a>带分隔符的文本数据集

在带分隔符的文本数据集，您将设置要处理任一单个分隔符的分隔符 ('\t '为 TSV，'，' csv，' |'...) 或使用多个字符作为分隔符。 将标头行开关设置，然后转到要自动检测数据类型的源转换。

## <a name="next-steps"></a>后续步骤

首先[创建新的数据流动](data-flow-create.md)添加源转换。 然后配置你的源数据集。

使用[复制活动](copy-activity-overview.md)将数据导入任何 ADF 从数据源并将其暂存 ADLS 或 Blob 中的数据流的访问。

