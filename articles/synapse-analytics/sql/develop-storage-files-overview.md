---
title: 使用 SQL 按需版本（预览版）在 Synapse SQL 中访问存储中的文件
description: 介绍如何使用 SQL 按需版本（预览版）资源在 Synapse SQL 中查询存储文件。
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/19/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: c251b70d1988be82821f1e133151dae1ac6d1bc9
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85921295"
---
# <a name="accessing-external-storage-in-synapse-sql-on-demand"></a>访问 Synapse SQL 中的外部存储（按需版本）

本文档介绍用户如何从 Synapse SQL（按需版本）中 Azure 存储中存储的文件读取数据。 用户具有以下用于访问存储的选项：

- [OPENROWSET](develop-openrowset.md) 函数，可对 Azure 存储中的文件进行即席查询。
- [外部表](develop-tables-external-tables.md)，它是基于一组外部文件生成的预定义数据结构。

用户可以使用[不同的身份验证方法](develop-storage-files-storage-access-control.md)，例如 Azure AD 传递身份验证（Azure AD 主体的默认方法）和 SAS 身份验证（SQL 主体的默认方法）。

## <a name="openrowset"></a>OPENROWSET

[OPENROWSET](develop-openrowset.md) 函数，使用户能够读取 Azure 存储中的文件。

### <a name="query-files-using-openrowset"></a>使用 OPENROWSET 查询文件

借助 OPENROWSET，用户可以查询 Azure 存储上的外部文件，前提是他们具有对存储的访问权限。 连接到 Synapse SQL 按需版本终结点的用户应使用以下查询来读取 Azure 存储中文件的内容：

```sql
SELECT * FROM
 OPENROWSET(BULK 'http://storage...com/container/file/path/*.csv', format= 'parquet') as rows
```

用户可以使用以下访问规则来访问存储：

- Azure AD 用户 - OPENROWSET 将使用调用方的 Azure AD 标识来访问 Azure 存储或通过匿名访问来访问存储。
- SQL 用户 - OPENROWSET 将通过匿名访问来访问存储。

SQL 主体也可以使用 OPENROWSET 直接查询受 SAS 令牌保护的文件或工作区的托管标识。 如果 SQL 用户执行此函数，则具有 ALTER ANY CREDENTIAL 权限的 Power User 必须创建服务器范围的凭据，该凭据与该函数（使用存储名称和容器）中的 URL 匹配，并向 OPENROWSET 函数的调用方授予此凭据的 REFERENCES 权限：

```sql
EXECUTE AS somepoweruser

CREATE CREDENTIAL [http://storage.dfs.com/container]
 WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 'sas token';

GRANT REFERENCES CREDENTIAL::[http://storage.dfs.com/container] TO sqluser
```

如果没有与 URL 匹配的服务器级凭据或 SQL 用户没有此凭据的引用权限，则将返回错误。 SQL 主体无法使用某些 Azure AD 标识进行模拟。

> [!NOTE]
> 此版本的 OPENROWSET 旨在使用默认的身份验证快速轻松地浏览数据。 若要利用模拟或托管标识，请将 OPENROWSET 与下一部分中所述的 DATASOURCE 一起使用。

### <a name="querying-data-sources-using-openrowset"></a>使用 OPENROWSET 查询数据源

借助 OPENROWSET，用户可以查询放置在某些外部数据源上的文件：

```sql
SELECT * FROM
 OPENROWSET(BULK 'file/path/*.parquet',
 DATASOURCE = MyAzureInvoices,
 FORMAT= 'parquet') as rows
```

具有 CONTROL DATABASE 权限的 Power User 将需要创建 DATABASE SCOPED CREDENTIAL（将用于访问存储）和 EXTERNAL DATA SOURCE（指定应使用的数据源和凭据的 URL）：

```sql
CREATE DATABASE SCOPED CREDENTIAL AccessAzureInvoices
 WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
 SECRET = '******srt=sco&amp;sp=rwac&amp;se=2017-02-01T00:55:34Z&amp;st=201********' ;

CREATE EXTERNAL DATA SOURCE MyAzureInvoices
 WITH ( LOCATION = 'https://newinvoices.blob.core.windows.net/week3' ,
 CREDENTIAL = AccessAzureInvoices) ;
```

DATABASE SCOPED CREDENTIAL 指定如何访问参考数据源（当前为 SAS 和托管标识）上的文件。

调用方必须具有以下权限之一才能执行 OPENROWSET 函数：

- 执行 OPENROWSET 所需的权限之一：
  - ADMINISTER BULK OPERATION，使登录名可以执行 OPENROWSET 函数。
  - ADMINISTER DATABASE BULK OPERATION，使数据库范围内的用户可以执行 OPENROWSET 函数。
- EXTERNAL DATA SOURCE 中引用的凭据的 REFERENCES DATABASE SCOPED CREDENTIAL

#### <a name="accessing-anonymous-data-sources"></a>访问匿名数据源

用户可以创建不带 CREDENTIAL 的 EXTERNAL DATA SOURCE 来引用公共访问存储，也可以使用 Azure AD 传递身份验证：

```sql
CREATE EXTERNAL DATA SOURCE MyAzureInvoices
 WITH ( LOCATION = 'https://newinvoices.blob.core.windows.net/week3') ;
```

## <a name="external-table"></a>EXTERNAL TABLE

具有读取表权限的用户可以使用基于一组 Azure 存储文件夹和文件创建的 EXTERNAL TABLE 来访问外部文件。

[具有创建外部表权限](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql?view=sql-server-ver15#permissions)（例如 CREATE TABLE 和 ALTER ANY CREDENTIAL 或 REFERENCES DATABASE SCOPED CREDENTIAL）的用户可以使用以下脚本基于 Azure 存储数据源创建表：

```sql
CREATE EXTERNAL TABLE [dbo].[DimProductexternal]
( ProductKey int, ProductLabel nvarchar, ProductName nvarchar )
WITH
(
LOCATION='/DimProduct/year=*/month=*' ,
DATA_SOURCE = AzureDataLakeStore ,
FILE_FORMAT = TextFileFormat
) ;
```

拥有 CONTROL DATABASE 权限的用户将需要创建 DATABASE SCOPED CREDENTIAL（将用于访问存储）和 EXTERNAL DATA SOURCE（指定应使用的数据源和凭据的 URL）：

```sql
CREATE DATABASE SCOPED CREDENTIAL cred
 WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
 SECRET = '******srt=sco&sp=rwac&se=2017-02-01T00:55:34Z&st=201********' ;

CREATE EXTERNAL DATA SOURCE AzureDataLakeStore
 WITH ( LOCATION = 'https://samples.blob.core.windows.net/products' ,
 CREDENTIAL = cred
 ) ;
```

DATABASE SCOPED CREDENTIAL 指定如何访问引用的数据源上的文件。

### <a name="reading-external-files-with-external-table"></a>使用 EXTERNAL TABLE 读取外部文件

借助 EXTERNAL TABLE，你可以读取使用标准 SQL SELECT 语句通过数据源引用的文件中的数据：

```sql
SELECT *
FROM dbo.DimProductsExternal
```

调用方必须具有以下权限才能读取数据：
- 对外部表的 `SELECT` 权限
- `REFERENCES DATABASE SCOPED CREDENTIAL` 权限（如果 `DATA SOURCE` 具有 `CREDENTIAL`）

## <a name="permissions"></a>权限

下表列出了上面列出的操作所需的权限。

| 查询 | 所需的权限|
| --- | --- |
| OPENROWSET(BULK)，不包含数据源 | `ADMINISTER BULK ADMIN`、`ADMINISTER DATABASE BULK ADMIN` 或 SQL 登录名必须对受 SAS 保护的存储具有 REFERENCES CREDENTIAL::\<URL> |
| OPENROWSET(BULK)，包含不带凭据的数据源 | `ADMINISTER BULK ADMIN` 或 `ADMINISTER DATABASE BULK ADMIN`， |
| OPENROWSET(BULK)，包含带凭据的数据源 | `ADMINISTER BULK ADMIN`、`ADMINISTER DATABASE BULK ADMIN` 或 `REFERENCES DATABASE SCOPED CREDENTIAL` |
| CREATE EXTERNAL DATA SOURCE | `ALTER ANY EXTERNAL DATA SOURCE` 和 `REFERENCES DATABASE SCOPED CREDENTIAL` |
| CREATE EXTERNAL TABLE | `CREATE TABLE`、`ALTER ANY SCHEMA`、`ALTER ANY EXTERNAL FILE FORMAT` 和 `ALTER ANY EXTERNAL DATA SOURCE` |
| SELECT FROM EXTERNAL TABLE | `SELECT TABLE` 和 `REFERENCES DATABASE SCOPED CREDENTIAL` |
| CETAS | 创建表：`CREATE TABLE`、`ALTER ANY SCHEMA`、`ALTER ANY DATA SOURCE` 和 `ALTER ANY EXTERNAL FILE FORMAT`。 读取数据：查询中每个表/视图/函数的 `ADMIN BULK OPERATIONS` 或 `REFERENCES CREDENTIAL` 或 `SELECT TABLE` + 对存储的 R/W 权限 |

## <a name="next-steps"></a>后续步骤

现在，你已准备好继续学习以下操作指南文章：

- [查询存储中的数据](query-data-storage.md)

- [查询 CSV 文件](query-single-csv-file.md)

- [查询文件夹和多个文件](query-folders-multiple-csv-files.md)

- [查询特定的文件](query-specific-files.md)

- [查询 Parquet 文件](query-parquet-files.md)

- [查询嵌套类型](query-parquet-nested-types.md)

- [查询 JSON 文件](query-json-files.md)

- [创建和使用视图](create-use-views.md)
