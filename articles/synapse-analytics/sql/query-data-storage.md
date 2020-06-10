---
title: 概述 - 使用 SQL 按需版本（预览版）查询存储中的数据
description: 本部分包含可用于在 Azure Synapse Analytics 中试用 SQL 按需版本（预览版）资源的示例查询。
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 8501f9d07ffa2d04915d4d1a351317cc145f9844
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2020
ms.locfileid: "84118269"
---
# <a name="overview-query-data-in-storage"></a>概述：查询存储中的数据

本部分包含可用于在 Azure Synapse Analytics 中试用 SQL 按需版本（预览版）资源的示例查询。
当前支持的文件格式为：  
- CSV
- Parquet
- JSON

## <a name="prerequisites"></a>先决条件

发出查询所需的工具：

- 所选的 SQL 客户端：
    - Azure Synapse Studio（预览版）
    - Azure Data Studio
    - SQL Server Management Studio

此外，参数如下所示：

| 参数                                 | 说明                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| SQL 按需版本服务终结点地址    | 将用作服务器名称。                                   |
| SQL 按需版本服务终结点区域     | 将用于确定示例中使用的存储。 |
| 用于访问终结点的用户名和密码 | 将用于访问终结点。                               |
| 用于创建视图的数据库     | 在示例中，我们将从此数据库着手。       |

## <a name="first-time-setup"></a>首次设置

第一步是创建将在其中执行查询的数据库。 然后通过对该数据库执行[安装脚本](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql)来初始化这些对象。 此安装脚本将创建数据源、数据库范围的凭据和用于读取这些示例中数据的外部文件格式。

> [!NOTE]
> 数据库仅用于查看元数据，而不用于实际数据。  请记下使用的数据库名称，因为稍后需要用到。

```sql
CREATE DATABASE mydbname;
```

## <a name="provided-demo-data"></a>提供的演示数据

演示数据包含以下数据集：

- 纽约市出租车 - 黄色出租车行程记录 - 纽约市公共数据集的一部分
  - CSV 格式
  - Parquet 格式
- 人口数据集
  - CSV 格式
- 包含嵌套列的 Parquet 文件示例
  - Parquet 格式
- 书籍 JSON
  - JSON 格式

| 文件夹路径                                                  | 说明                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| /csv/                                                        | 采用 CSV 格式的数据的父文件夹                         |
| /csv/population/<br />/csv/population-unix/<br />/csv/population-unix-hdr/<br />/csv/population-unix-hdr-escape<br />/csv/population-unix-hdr-quoted | 采用不同 CSV 格式的人口数据文件的文件夹。 |
| /csv/taxi/                                                   | 采用 CSV 格式的纽约市公共数据文件的文件夹              |
| /parquet/                                                    | 采用 Parquet 格式的数据的父文件夹                     |
| /parquet/taxi                                                | 采用 Parquet 格式的纽约市公共数据文件，已使用 Hive/Hadoop 分区方案按年份和月份分区。 |
| /parquet/nested/                                             | 包含嵌套列的 Parquet 文件示例                     |
| /json/                                                       | 采用 JSON 格式的数据的父文件夹                        |
| /json/books/                                                 | 包含书籍数据的 JSON 文件                                   |

### <a name="sample-query"></a>示例查询

最后一个验证步骤是执行以下查询：

```sql
SELECT
    COUNT_BIG(*)
FROM  
    OPENROWSET(
        BULK 'parquet/taxi/year=2017/month=9/*.parquet',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT='PARQUET'
    ) AS nyc;
```

上述查询应返回以下数字：8945574。

## <a name="next-steps"></a>后续步骤

现在，你已准备好继续学习以下操作指南文章：

- [查询单个 CSV 文件](query-single-csv-file.md)

- [查询文件夹和多个 CSV 文件](query-folders-multiple-csv-files.md)

- [查询特定的文件](query-specific-files.md)

- [查询 Parquet 文件](query-parquet-files.md)

- [查询 Parquet 嵌套类型](query-parquet-nested-types.md)

- [查询 JSON 文件](query-json-files.md)

- [创建和使用视图](create-use-views.md)
