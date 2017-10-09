---
title: "适用于 Azure 机器学习数据准备的受支持数据源 | Microsoft Docs"
description: "本文档提供可用于 Azure ML 数据准备的受支持数据源的完整列表"
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
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 0a77b31e07f118155094fab4d3393ffdb37c9c6f
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---

# <a name="supported-data-sources-for-this-release"></a>适用于本版本的受支持数据源 
以下文档概要列出目前在数据准备中受到支持的数据源。

下面列出了适用于本版本的受支持数据源。

## <a name="types"></a>类型 
### <a name="directory-vs-file"></a>目录与文件
文件/目录 - 选择一个文件并在数据准备中读入，文件类型经过分析后，可用于确定下一屏幕上的文件连接的默认参数。 选择一个目录或目录中的文件集（文件选取器可多选），使用任一方法都可将文件作为单一数据流读入，且文件会拼接在一起（去除标头，如果需要）

受支持的文件类型包括：
- 分隔值文件（csv、tsv、txt 等） 
- 宽度固定文件
- 纯文本
- JSON 文件

### <a name="csv-file"></a>CSV 文件
从存储中读取逗号分隔值文件

#### <a name="options"></a>选项
- 分隔符
- 注释
- 标头
- 十进制符号
- 文件编码
- 要跳过的行

### <a name="tsv-file"></a>TSV 文件
从存储中读取制表符分隔值文件

#### <a name="options"></a>选项
- 注释
- 标头
- 文件编码
- 要跳过的行

### <a name="excel-xlsxlsx"></a>Excel (.xls/xlsx)
读取 Excel 文件，通过指定工作表名称或编号，一次读取一个工作表

#### <a name="options"></a>选项
- 工作表名称/编号
- 标头
- 要跳过的行

### <a name="json-file"></a>JSON 文件
从存储中读取 JSON 文件，请注意，文件在读取时处于“平展”状态

#### <a name="options"></a>选项
- 无

### <a name="parquet"></a>Parquet
读取 Parquet 数据集，可读取单个文件或文件夹。

Parquet 格式在存储中可采用各种形式。 对于较小的数据集，有时会使用单个“.parquet”文件，各种 python 库均支持读取/写入到单个“.parquet”文件。 目前，AMLWB 依靠 PyArrow python 库在本地“交互式”使用过程中读取 Parquet。 它支持单个“.parquet”文件（只要它们以这种形式写入，而不是作为较大数据集的一部分写入）以及 Parquet 数据集。 Parquet 数据集是多个“.parquet”文件的集合，其中每个文件代表较大数据集的较小分区。 数据集通常包含在文件夹中，是常见平台（比如 Spark 和 Hive）的默认 parquet 输出格式。

>[!NOTE]
>读取包含多个“.parquet”文件的文件夹中的 Parquet 数据时，最安全的做法是选择要读取的目录，并标记“Parquet 数据集”选项。 这样一来，PyArrow 会读取整个文件夹，而不读取单个文件，从而确保支持读取以更复杂的方式在磁盘上存储的 Parquet（比如文件夹分区）。**

横向扩展执行依赖于 Spark 的 Parquet 读取功能，并支持单个文件以及文件夹，类似于本地交互。

#### <a name="options"></a>选项
- Parquet 数据集
  - 此选项可确定 AMLWB 是会展开给定目录并尝试单独读取其中包含的每个文件（未标记模式），还是会将目录视为完整的数据集并让 PyArrow 找出解释文件的最佳方式（标记模式）。


## <a name="locations"></a>位置
### <a name="local"></a>Local
本地硬盘或映射网络存储位置

### <a name="azure-blob"></a>Azure BLOB
Azure 存储 (BLOB)，需要 Azure 订阅


