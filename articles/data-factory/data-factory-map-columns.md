---
title: "映射 Azure 数据工厂中的数据集列 | Microsoft Docs"
description: "了解如何将源列映射到目标列。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2017
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 444ac59b0b21795228e08f79a25a638d72403399
ms.lasthandoff: 03/30/2017


---

# <a name="map-source-dataset-columns-to-destination-dataset-columns"></a>将源数据集列映射到目标数据集列
使用列映射可以指定如何将源表“结构”中指定的列映射到接收器表“结构”中指定的列。 可以在复制活动的 **typeProperties** 节中使用 **ColumnMapping** 属性。

列映射支持以下方案：

* 将源数据集结构中的所有列映射到接收器数据集结构中的所有列。
* 将源数据集结构中的列子集映射到接收器数据集结构中的所有列。

以下是导致异常的错误条件：

* 接收器表“结构”中的列数量多于或少于映射中指定的数量。
* 重复的映射。
* SQL 查询结果中没有映射中指定的列名。

> [!NOTE]
> 以下示例适用于 Azure SQL 和 Azure Blob，但也适用于任何支持矩形数据集的数据存储。 调整示例中的数据集与链接服务定义，使其指向相关数据源中的数据。

## <a name="sample-1--column-mapping-from-azure-sql-to-azure-blob"></a>示例 1 – 从 Azure SQL 到 Azure Blob 的列映射
在本示例中，输入表有一个结构，并指向 Azure SQL 数据库中的 SQL 表。

```json
{
    "name": "AzureSQLInput",
    "properties": {
        "structure": 
         [
           { "name": "userid"},
           { "name": "name"},
           { "name": "group"}
         ],
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

在本示例中，输出表有一个结构，并指向 Azure Blob 存储中的 Blob。

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
         "structure": 
          [
                { "name": "myuserid"},
                { "name": "myname" },
                { "name": "mygroup"}
          ],
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
            "fileName":"myfile.csv",
            "format":
            {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

以下 JSON 定义管道中的复制活动。 使用 **Translator** 属性将源中的列映射到接收器中的列 (**columnMappings**)。

```json
{
    "name": "CopyActivity",
    "description": "description", 
    "type": "Copy",
    "inputs":  [ { "name": "AzureSQLInput"  } ],
    "outputs":  [ { "name": "AzureBlobOutput" } ],
    "typeProperties":    {
        "source":
        {
            "type": "SqlSource"
        },
        "sink":
        {
            "type": "BlobSink"
        },
        "translator": 
        {
            "type": "TabularTranslator",
            "ColumnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"
        }
    },
   "scheduler": {
          "frequency": "Hour",
          "interval": 1
        }
}
```
**列映射流：**

![列映射流](./media/data-factory-map-columns/column-mapping-flow.png)

## <a name="sample-2--column-mapping-with-sql-query-from-azure-sql-to-azure-blob"></a>示例 2 – 使用 SQL 查询从 Azure SQL 到 Azure Blob 的列映射
本示例使用 SQL 查询从 Azure SQL 提取数据，而不是只在“结构”部分中指定表名和列名。 

```json
{
    "name": "CopyActivity",
    "description": "description", 
    "type": "CopyActivity",
    "inputs":  [ { "name": " AzureSQLInput"  } ],
    "outputs":  [ { "name": " AzureBlobOutput" } ],
    "typeProperties":
    {
        "source":
        {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartDateTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
        },
        "sink":
        {
            "type": "BlobSink"
        },
        "Translator": 
        {
            "type": "TabularTranslator",
            "ColumnMappings": "UserId: MyUserId, Group: MyGroup,Name: MyName"
        }
    },
    "scheduler": {
          "frequency": "Hour",
          "interval": 1
        }
}
```
在此情况下，查询结果先映射到源“结构”所指定的列。 接下来，源“结构”中的列使用 columnMappings 中指定的规则映射到接收器“结构”中的列。  假设该查询返回 5 个列，比源的“结构”中指定的多了 2 个列。

**列映射流**

![列映射流-2](./media/data-factory-map-columns/column-mapping-flow-2.png)

## <a name="next-steps"></a>后续步骤
有关使用复制活动的教程，请参阅以下文章： 

- [将数据从 Blob 存储复制到 SQL 数据库](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)

