---
title: "适用于 Azure 机器学习数据准备的受支持数据源 | Microsoft Docs"
description: "本文档提供适用于 Azure 机器学习数据准备的受支持数据源的完整列表"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/12/2017
ms.openlocfilehash: 1ef4c5c33d98cfeb566e8fe23bda9e0d3f041781
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2017
---
# <a name="supported-data-sources-for-this-release"></a>适用于本版本的受支持数据源 
以下文档概述了 Azure 机器学习数据准备中当前受支持的数据源列表。

适用于本版本的受支持数据源如下。

## <a name="types"></a>类型 
### <a name="directory-versus-file"></a>目录与文件
文件/目录：选择一个文件并在数据准备中读入。 分析文件类型，确定下一屏幕上文件连接的默认参数。 选择目录或目录中的文件集（文件选取器可多选）。 使用任一方法都会以单一数据流的形式读取文件，且文件会相互追加（根据需要去除标头）。

支持的文件类型如下：
- 分隔值文件（.csv、.tsv、.txt 等） 
- 固定宽度
- 纯文本
- JSON 文件

### <a name="csv-file"></a>CSV 文件
从存储中读取 CSV 文件。

#### <a name="options"></a>选项
- 分隔符
- 注释
- 标头
- 十进制符号
- 文件编码
- 要跳过的行

### <a name="tsv-file"></a>TSV 文件
从存储中读取 TSV 文件。

#### <a name="options"></a>选项
- 注释
- 标头
- 文件编码
- 要跳过的行

### <a name="excel-xlsxlsx"></a>Excel (.xls/.xlsx)
通过指定工作表名称或编号，读取 Excel 文件（一次读取一个工作表）。

#### <a name="options"></a>选项
- 工作表名称/编号
- 标头
- 要跳过的行

### <a name="json-file"></a>JSON 文件
从存储中读取 JSON 文件。 请注意，文件在读取时处于“平展”状态。

#### <a name="options"></a>选项
无

### <a name="parquet"></a>Parquet
读取 Parquet 数据集（单个文件或文件夹）。

Parquet 格式在存储中可采用各种形式。 对于较小的数据集，有时会使用单个 .parquet 文件。 各种 Python 库均支持读取/写入单个 .parquet 文件。 目前，Azure Machine Learning Workbench 依靠 PyArrow Python 库在本地交互使用过程中读取 Parquet。 它支持单个 .parquet 文件（只要它们是以这种形式写入的，而不是较大数据集的一部分）。 以及 Parquet 数据集。 

Parquet 数据集是多个 .parquet 文件的集合，其中每个文件代表较大数据集的较小分区。 数据集通常包含在文件夹中。 这是 Spark 和 Hive 等常见平台的默认 Parquet 输出格式。

>[!NOTE]
>读取包含多个 .parquet 文件的文件夹中的 Parquet 数据时，最安全的做法是选择要读取的目录，并选中“Parquet 数据集”选项。 这会使 PyArrow 读取整个文件夹，而不是单个文件。 这样可以确保支持读取以更复杂的方式在磁盘上存储的 Parquet（比如文件夹分区）

横向扩展的执行依赖于 Spark 的 Parquet 读取功能，并支持文件和文件夹。

#### <a name="options"></a>选项
*Parquet 数据集*：此选项可确定 Azure Machine Learning Workbench 使用未选中的模式还是选中的模式。 未选中的模式将展开给定目录，并尝试单独读取其中每个文件。 选中的模式将目录视为完整的数据集，并让 PyArrow 找出解读文件的最佳方式。


## <a name="locations"></a>位置
### <a name="local"></a>Local
本地硬盘或映射网络存储位置。

### <a name="azure-blob-storage"></a>Azure Blob 存储
需要 Azure 订阅。

