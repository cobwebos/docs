---
title: Azure 数据工厂映射数据流数据集
description: Azure 数据工厂映射数据流有特定的数据集兼容性
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/14/2019
ms.openlocfilehash: 36ca5e07adf79de77ac4ab4149ff8e96a1dece8d
ms.sourcegitcommit: 4bf542eeb2dcdf60dcdccb331e0a336a39ce7ab3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/19/2019
ms.locfileid: "56408742"
---
# <a name="mapping-data-flow-datasets"></a>映射数据流数据集

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

数据集是一个数据工厂构造，它定义在管道中使用的数据的形状。 在数据流中，行级和列级数据需要细致的数据集定义。 在控制流管道中使用的数据集不需要具有与数据理解相同的深度。

数据流源和接收器转换中的数据集用于定义基本数据架构。 如果数据中没有架构，则可以为源和接收器设置架构偏差。 从数据集定义架构后，将具有相关的数据类型、数据格式、文件位置和关联的链接服务的连接信息。

## <a name="dataset-types"></a>数据集类型

目前，数据流中有四种数据集类型：

* Azure SQL 数据库
* Azure SQL DW
* Parquet
* 带分隔符的文本

数据流数据集将源*类型*与链接的服务连接类型进行区分。 通常情况下，在数据工厂中，先选择连接类型（Blob、ADLS 等），然后在数据集中定义文件的类型。 在数据流中，将选取源类型，该类型可以与不同的链接服务连接类型关联。

![源转换选项](media/data-flow/dataset1.png "源")

## <a name="data-flow-compatible-datasets"></a>数据流兼容的数据集

创建新的数据集时，面板右上方有一个标记为“数据流兼容”的复选框。 单击该按钮将仅筛选可以与数据流配合使用的数据集。 

## <a name="import-schemas"></a>导入架构

导入数据流数据集的架构时，将看到“导入架构”按钮。 单击该按钮将显示两个选项：从源导入或从本地文件导入。 在大多数情况下，将直接从源导入架构。 但是，如果有现成的架构文件（Parquet 文件或带有标头的 CSV），则可以指向该本地文件，数据工厂将根据该架构文件定义架构。

