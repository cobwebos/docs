---
title: 从 Azure 的 Jupyter Notebook 访问数据资源
description: 如何从 Jupyter Notebook 中访问文件、REST API、数据库和不同的 Azure 存储资源。
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: ee867303-a5e5-4686-b2da-8a0108247d18
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: 3dd1dde067a2ec745d7d60a78a50ab34ea30d0dd
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359800"
---
# <a name="access-cloud-data-in-a-notebook"></a>在笔记本中访问云数据

在 Jupyter Notebook 中进行一些有趣的工作需要数据。 实际上，数据是 Notebook 的命脉。

当然可[将数据文件导入项目](work-with-project-data-files.md)，甚至可以使用 Notebook 中的 `curl` 等命令直接下载文件。 但极有可能需要使用非文件源（如 REST API、关系数据库和 Azure 存储（如 Azure 表））提供的更广泛的数据。

本文简要概述了这些不同的选项。 由于在操作中最容易看到数据访问，因此可在 [Azure Notebooks 示例 -访问数据](https://github.com/Microsoft/AzureNotebooks/blob/master/Samples/Access%20your%20data%20in%20Azure%20Notebooks.ipynb)中找到可运行的代码。

## <a name="rest-apis"></a>REST API

一般来说，从 Internet 上获取的大量数据不是通过文件访问的，而是通过 REST API 访问的。 幸运的是，由于 Notebook 单元可以包含任何你喜欢的代码，因此可使用代码发送请求和接收 JSON 数据。 然后，可将该 JSON 转换为要使用的任何格式，例如 pandas 数据帧。

要使用 REST API 访问数据，请在任何其他应用程序中使用的 Notebook 的代码单元中使用相同的代码。 使用请求库的一般结构如下：

```python
import pandas
import requests

# New York City taxi data for 2014
data_url = 'https://data.cityofnewyork.us/resource/gkne-dk5s.json'

# General data request; include other API keys and credentials as needed in the data argument
response = requests.get(data_url, data={"limit": "20"})

if response.status_code == 200:
    dataframe_rest2 = pandas.DataFrame.from_records(response.json())
    print(dataframe_rest2)
```

## <a name="azure-sql-databases"></a>Azure SQL 数据库

可借助 pyodbc 或 pymssql 库访问 SQL Server 数据库。

[使用 Python 查询 Azure SQL 数据库](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-python)介绍了如何创建包含 AdventureWorks 数据的数据库，并演示了如何查询该数据。 本文的示例 Notebook 中显示了相同的代码。

## <a name="azure-storage"></a>Azure 存储

Azure 存储提供了几种不同类型的非关系存储，具体取决于你拥有的数据类型以及所需的访问方式：

- 表存储：为表格数据提供低成本、高容量的存储，如收集的传感器日志、诊断日志等。
- Blob 存储：为任何类型的数据提供类似文件的存储。

示例 Notebook 演示了如何使用表和 blob，包括如何使用共享访问签名来允许对 blob 的只读访问。

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

Azure Cosmos DB 为 JSON 文档提供了完全索引的 NoSQL 存储。 以下文章提供了许多使用 Python 的 Cosmos DB 的不同方法：

- [使用 Python 生成 SQL API 应用](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-python)
- [使用 Azure Cosmos DB 的 API for MongoDB 构建 Flask 应用](https://docs.microsoft.com/azure/cosmos-db/create-mongodb-flask)
- [使用 Python 和 Gremlin API 创建图形数据库](https://docs.microsoft.com/azure/cosmos-db/create-graph-python)
- [使用 Python 和 Azure Cosmos DB 生成 Cassandra 应用](https://docs.microsoft.com/azure/cosmos-db/create-cassandra-python)
- [使用 Python 和 Azure Cosmos DB 生成表 API 应用](https://docs.microsoft.com/azure/cosmos-db/create-table-python)

使用 Cosmos DB 时，可以使用 [azure-cosmosdb-table](https://pypi.org/project/azure-cosmosdb-table/) 库。

## <a name="other-azure-databases"></a>其他 Azure 数据库

Azure 提供了许多可以使用的其他数据库类型。 以下文章提供了从 Python 访问这些数据库的指导：

- [Azure Database for PostgreSQL：使用 Python 连接和查询数据](https://docs.microsoft.com/azure/postgresql/connect-python)
- [快速入门：将 Azure Redis 缓存与 Python 配合使用](https://docs.microsoft.com/azure/redis-cache/cache-python-get-started)
- [Azure Database for MySQL：使用 Python 连接和查询数据](https://docs.microsoft.com/azure/mysql/connect-python)
- [Azure 数据工厂](https://azure.microsoft.com/services/data-factory/)
  - [Azure 数据工厂的复制向导](https://azure.microsoft.com/updates/code-free-copy-wizard-for-azure-data-factory/)

## <a name="next-steps"></a>后续步骤

- [如何：使用项目数据文件](work-with-project-data-files.md)
