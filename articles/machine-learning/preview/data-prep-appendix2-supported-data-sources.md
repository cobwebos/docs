---
title: "适用于 Azure 机器学习数据准备的受支持数据源 | Microsoft Docs"
description: "本文档提供适用于 Azure 机器学习数据准备的受支持数据源的完整列表。"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/12/2017
ms.openlocfilehash: 32707a8089abef6caebedea168f5891161b1b480
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/18/2017
---
# <a name="supported-data-sources-for-azure-machine-learning-data-preparation"></a>适用于 Azure 机器学习数据准备的受支持数据源 
本文概述了当前适用于 Azure 机器学习数据准备的受支持数据源。

适用于本版本的受支持数据源如下。

## <a name="types"></a>类型 

### <a name="sql-server"></a>SQL Server
从本地 SQL Server 或 Azure SQL 数据库读取。

#### <a name="options"></a>选项
- 服务器地址
- 信任服务器（即使服务器上的证书无效时也是， 请谨慎使用）
- 身份验证类型（Windows、服务器）
- 用户名
- 密码
- 要连接到的数据库
- SQL 查询

#### <a name="notes"></a>说明
- 不支持 Sql 变体列
- 通过将数据库中的时间追加到日期 1970/1/1 来将时间列转换为日期时间
- 在 Spark 群集上执行时，所有数据相关列（date、datetime、datetime2、datetimeoffset）将评估早于 1583 年的不正确日期值
- 由于转换为十进制，十进制列中的值可能会丢失精度

### <a name="directory-vs-file"></a>目录与文件
选择一个文件并在数据准备中读入。 分析文件类型，确定下一屏幕上文件连接的默认参数。

选择目录或目录中的文件集（文件选取器可多选）。 无论采用哪种方法，都会以单一数据流的形式读取文件，且文件会相互追加，同时会根据需要去除标头。

支持的文件类型：
- 分隔值文件（.csv、.tsv、.txt 等）
- 固定宽度
- 纯文本
- JSON 文件

### <a name="csv-file"></a>CSV 文件
从存储中读取逗号分隔值文件。

#### <a name="options"></a>选项
- 分隔符
- 注释
- 标头
- 十进制符号
- 文件编码
- 要跳过的行

### <a name="tsv-file"></a>TSV 文件
从存储中读取制表符分隔值文件。

#### <a name="options"></a>选项
- 注释
- 标头
- 文件编码
- 要跳过的行

### <a name="excel-xlsxlsx"></a>Excel (.xls/.xlsx)
读取 Excel 文件，通过指定工作表名称或编号，一次读取一个工作表。

#### <a name="options"></a>选项
- 工作表名称或编号
- 标头
- 要跳过的行

### <a name="json-file"></a>JSON 文件
从存储中读取 JSON 文件。 文件在读取时处于“平展”状态。

#### <a name="options"></a>选项
- 无

### <a name="parquet"></a>Parquet
读取 Parquet 数据集，可以是单个文件或文件夹。

Parquet 格式在存储中可采用各种形式。 对于较小的数据集，有时会使用单个 .parquet 文件。 各种 Python 库均支持读取或写入单个 .parquet 文件。 目前，Azure 机器学习数据准备依靠 PyArrow Python 库在本地交互使用过程中读取 Parquet。 它支持单个 .parquet 文件（只要它们以这种形式写入，且不是作为较大数据集的一部分写入）以及 Parquet 数据集。

Parquet 数据集是多个 .parquet 文件的集合，其中每个文件代表较大数据集的较小分区。 数据集通常包含在文件夹中，是 Spark 和 Hive 等平台的默认 parquet 输出格式。

>[!NOTE]
>读取包含多个 .parquet 文件的文件夹中的 Parquet 数据时，最安全的做法是选择要读取的目录，并选择“Parquet 数据集”选项。 这会使 PyArrow 读取整个文件夹，而不是单个文件。 这样可以确保支持读取以更复杂的方式在磁盘上存储的 Parquet，比如文件夹分区。

横向扩展执行依赖于 Spark 的 Parquet 读取功能，并支持单个文件以及文件夹，与本地交互使用类似。

#### <a name="options"></a>选项
- Parquet 数据集。 此选项可确定 Azure 机器学习数据准备是否扩展了给定目录，是否尝试单独读取每个文件（未选定的模式），或者是否将目录视为整个数据集（所选模式）。 使用所选模式，PyArrow 选择最佳方式来解释文件。


## <a name="locations"></a>位置
### <a name="local"></a>Local
本地硬盘或映射网络存储位置。

### <a name="sql-server"></a>SQL Server
本地 SQL Sever 或 Azure SQL 数据库。

### <a name="azure-blob-storage"></a>Azure Blob 存储
Azure Blob 存储需要 Azure 订阅。

