---
title: Azure Synapse Analytics 共享数据库
description: Azure Synapse Analytics 提供一个共享的元数据模型，通过该模型在 Apache Spark 中创建一个数据库后，可以从该数据库的 SQL 按需版本（预览版）和 SQL 池引擎访问该数据库。
services: synapse-analytics
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 05/01/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.openlocfilehash: da1bd9c812c20f60264d1a5ee1f8821128900618
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83698854"
---
# <a name="azure-synapse-analytics-shared-database"></a>Azure Synapse Analytics 共享数据库

Azure Synapse Analytics 允许不同的计算工作区引擎在其 Spark 池（预览版）和 SQL 按需版本（预览版）引擎之间共享数据库和表。

[!INCLUDE [synapse-analytics-preview-terms](../../../includes/synapse-analytics-preview-terms.md)]

使用 Spark 作业创建的数据库将以上述数据库的名称向工作区中所有当前和未来 Spark 池（预览版）以及 SQL 按需版本引擎显示。

Spark 默认数据库（名为 `default`）还会在 SQL 按需版本上下文中显示为名为 `default` 的数据库。

由于数据库以异步方式同步到 SQL 按需版本，因此它们出现的时间会有延迟。

## <a name="manage-a-spark-created-database"></a>管理 Spark 创建的数据库

使用 Spark 管理 Spark 创建的数据库。 例如，通过 Spark 池作业删除数据库，通过 Spark 在数据库中创建表。

如果使用 SQL 按需版本在 Spark 创建的数据库中创建对象，或尝试删除数据库，该操作会成功。 但是，原始 Spark 数据库不会更改。

## <a name="handling-of-name-conflicts"></a>名称冲突的处理

如果某个 Spark 数据库的名称与现有 SQL 按需版本数据库的名称冲突，则在 SQL 按需版本中，会将一个后缀追加到这个 Spark 数据库。 SQL 按需版本中的后缀是 `_<workspace name>-ondemand-DefaultSparkConnector`。

例如，如果在 Azure Synapse 工作区 `myws` 中创建了名为 `mydb` 的 Spark 数据库，并且已存在同名的 SQL 按需版本数据库，则必须使用名称 `mydb_myws-ondemand-DefaultSparkConnector` 引用 SQL 按需版本中的 Spark 数据库。

> [!CAUTION]
> 警告：不应依赖于此行为。

## <a name="security-model"></a>安全模型

Spark 数据库和表及其在 SQL 引擎中的已同步表示形式将在基础存储级别受到保护。

创建数据库的安全主体被视为该数据库的所有者，对该数据库及其对象拥有所有权限。

若要向安全主体（例如用户或安全组）授予对数据库的访问权限，请提供对 `warehouse` 目录中基础文件夹和文件的相应 POSIX 文件夹和文件权限。 

例如，要使某个安全主体能够读取数据库中的某个表，需要向该安全主体分配对 `warehouse` 目录中从数据库文件夹开始的所有文件夹的 `X` 和 `R` 权限。 此外，文件（例如表的基础数据文件）需要 `R` 权限。 

如果安全主体需要能够在数据库中创建对象或删除对象，则需要对 `warehouse` 文件夹中的文件夹和文件的附加 `W` 权限。

## <a name="examples"></a>示例

### <a name="create--connect-to-spark-database---sql-on-demand"></a>创建和连接 Spark 数据库 - SQL 按需版本

首先使用已在工作区中创建的 Spark 群集创建名为 `mytestdb` 的新 Spark 数据库。 例如，可以将 Spark C# 笔记本和以下 .NET for Spark 语句配合使用以实现此目的：

```csharp
spark.Sql("CREATE DATABASE mytestdb")
```

在短暂的延迟后，就能在 SQL 按需版本中看到该数据库。 例如，在 SQL 按需版本中运行以下语句。

```sql
SELECT * FROM sys.databases;
```

请验证结果中是否包含 `mytestdb`。

## <a name="next-steps"></a>后续步骤

- [详细了解 Azure Synapse Analytics 的共享元数据](overview.md)
- [详细了解 Azure Synapse Analytics 的共享元数据表](table.md)
