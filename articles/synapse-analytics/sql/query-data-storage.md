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
ms.openlocfilehash: cdad95b1a910a45629e85bcc716218b272afd9de
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81421291"
---
# <a name="overview-query-data-in-storage"></a>概述：查询存储中的数据

本部分包含可用于在 Azure Synapse Analytics 中试用 SQL 按需版本（预览版）资源的示例查询。
当前支持的文件为： 
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

在使用本文稍后提供的示例之前，需要完成两个步骤：

- 为视图创建数据库（若要使用视图）
- 创建供 SQL 按需版本用来访问存储中的文件的凭据

### <a name="create-database"></a>创建数据库

需使用一个数据库来创建视图。 将对本文档中的某些示例查询使用此数据库。

> [!NOTE]
> 数据库仅用于查看元数据，而不用于实际数据。  请记下使用的数据库名称，因为稍后需要用到。

```sql
CREATE DATABASE mydbname;
```

### <a name="create-credentials"></a>创建凭据

必须先创建凭据，然后才能运行查询。 SQL 按需版本服务将使用此凭据访问存储中的文件。

> [!NOTE]
> 若要成功运行本部分中的操作步骤，必须使用 SAS 令牌。
>
> 若要开始使用 SAS 令牌，必须删除[此文](develop-storage-files-storage-access-control.md#disable-forcing-azure-ad-pass-through)中所述的 UserIdentity。
>
> 默认情况下，SQL 按需版本始终使用 AAD 直通身份验证。

有关如何管理存储访问控制的详细信息，请查看[此链接](develop-storage-files-storage-access-control.md)。

> [!WARNING]
> 需要为终结点区域中的存储帐户创建凭据。 尽管 SQL 按需版本可以访问不同区域中的存储，但将存储和终结点置于同一区域可提供性能更好的体验。

若要创建 CSV、JSON 和 Parquet 容器的凭据，请运行以下代码：

```sql
-- create credentials for CSV container in our demo storage account
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://sqlondemandstorage.blob.core.windows.net/csv')
DROP CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/csv];
GO

CREATE CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/csv]
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D';
GO

-- create credentials for JSON container in our demo storage account
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://sqlondemandstorage.blob.core.windows.net/json')
DROP CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/json];
GO

CREATE CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/json]
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D';
GO

-- create credentials for PARQUET container in our demo storage account
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://sqlondemandstorage.blob.core.windows.net/parquet')
DROP CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/parquet];
GO

CREATE CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/parquet]
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D';
GO
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

## <a name="validation"></a>验证

执行以下三个查询，并检查是否正确创建了凭据。

> [!NOTE]
> 示例查询中的所有 URI 使用位于“北欧”Azure 区域中的存储帐户。 请确保已创建相应的凭据。 运行以下查询，确保列出了存储帐户。

```sql
SELECT name
FROM sys.credentials
WHERE
     name IN ( 'https://sqlondemandstorage.blob.core.windows.net/csv',
     'https://sqlondemandstorage.blob.core.windows.net/parquet',
     'https://sqlondemandstorage.blob.core.windows.net/json');
```

如果找不到相应的凭据，请查看[首次设置](#first-time-setup)。

### <a name="sample-query"></a>示例查询

最后一个验证步骤是执行以下查询：

```sql
SELECT
    COUNT_BIG(*)
FROM  
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet',
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
