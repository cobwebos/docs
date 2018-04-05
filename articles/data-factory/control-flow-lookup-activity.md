---
title: Azure 数据工厂中的查找活动 | Microsoft Docs
description: 了解如何使用查找活动从外部源查找值。 此输出可进一步由后续活动引用。
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: shlo
ms.openlocfilehash: 7d6abb72fca71c213f9810784581a9af2dafb3a2
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2018
---
# <a name="lookup-activity-in-azure-data-factory"></a>Azure 数据工厂中的查找活动
可使用查找活动从任何外部源读取或查找记录、表名称或值。 此输出可进一步由后续活动引用。 

当你想要从配置文件或数据源动态检索文件、记录或表列表时，查找活动会很有帮助。 活动输出可以进一步供其他活动使用，仅对这些项执行特定处理。

> [!NOTE]
> 本文适用于目前处于预览状态的 Azure 数据工厂第 2 版。 如果使用正式版 (GA) 1 版本的数据工厂服务，请参阅 [数据工厂版本 1 文档](v1/data-factory-introduction.md)。

## <a name="supported-capabilities"></a>支持的功能

以下数据源当前支持查找：

- Amazon Redshift
- Azure Blob 存储
- Azure Cosmos DB
- Azure Data Lake Store
- Azure 文件存储
- Azure SQL 数据库
- Azure SQL 数据仓库
- Azure 表存储
- Dynamics 365
- Dynamics CRM
- 文件系统
- PostgreSQL
- Salesforce
- Salesforce 服务云
- SFTP
- SQL Server

查找活动返回的最大行数是 **5000**，最大大小为 **10MB**。

## <a name="syntax"></a>语法

```json
{
    "name": "LookupActivity",
    "type": "Lookup",
    "typeProperties": {
        "source": {
            "type": "<source type>"
            <additional source specific properties (optional)>
        },
        "dataset": { 
            "referenceName": "<source dataset name>",
            "type": "DatasetReference"
        },
        "firstRowOnly": false
    }
}
```

## <a name="type-properties"></a>Type 属性
名称 | 说明 | Type | 必需？
---- | ----------- | ---- | --------
dataset | 为查找提供数据集引用。 从每篇相应的连接器文章的“数据集属性”部分中获取详细信息。 | 键/值对 | 是
源 | 包含特定于数据集的源属性，与复制活动源相同。 从每篇相应的连接器文章的“复制活动属性”部分中获取详细信息。 | 键/值对 | 是
firstRowOnly | 指示仅返回第一行还是返回所有行。 | 布尔 | 不会。 默认为 `true`。

请注意以下几点：

1. 不支持 ByteArray 类型的源列。
2. 数据集定义不支持结构。 对于文本格式化文件，可以使用标头行提供列名。

## <a name="use-the-lookup-activity-result-in-a-subsequent-activity"></a>在后续活动中使用查找活动结果

查找结果会返回到活动运行结果的 `output` 节。

* **当 `firstRowOnly` 设置为 `true`（默认值）时**，输出格式如以下代码所示。 查找结果位于固定的 `firstRow` 键下。 若要在后续活动中使用该结果，请使用 `@{activity('MyLookupActivity').output.firstRow.TableName}` 模式。

    ```json
    {
        "firstRow":
        {
            "Id": "1",
            "TableName" : "Table1"
        }
    }
    ```

* **当 `firstRowOnly` 设置为 `false` 时**，输出格式如以下代码所示。 `count` 字段指示返回多少条记录，详细值显示在固定的 `value` 数组下。 在这种情况下，查找活动后面通常跟随 [Foreach 活动](control-flow-for-each-activity.md)。 可以使用 `@activity('MyLookupActivity').output.value` 模式将 `value` 数组传递到 ForEach 活动 `items` 字段。 若要访问 `value` 数组中的元素，请使用以下语法：`@{activity('lookupActivity').output.value[zero based index].propertyname}`。 此处有一个示例：`@{activity('lookupActivity').output.value[0].tablename}`。

    ```json
    {
        "count": "2",
        "value": [
            {
                "Id": "1",
                "TableName" : "Table1"
            },
            {
                "Id": "2",
                "TableName" : "Table2"
            }
        ]
    } 
    ```

## <a name="example"></a>示例
在本示例中，复制活动将 Azure SQL 数据库实例的 SQL 表中的数据复制到 Azure Blob 存储。 SQL 表的名称存储在 Blob 存储的 JSON 文件中。 查找活动查找运行时的表名称。 此方法允许动态修改 JSON，而不必重新部署管道或数据集。 

本示例演示如何只查找第一行。 若要查找所有行并将结果与 ForEach 活动链接，请参阅[使用 Azure 数据工厂批量复制多个表](tutorial-bulk-copy.md)中的示例。

### <a name="pipeline"></a>管道
此管道包含两个活动：*查找*和*复制*。 

- 查找活动配置为使用 LookupDataset，它指的是 Azure Blob 存储中的一个位置。 查找活动在此位置从 JSON 文件读取 SQL 表名称。 
- 复制活动使用查找活动的输出（SQL 表名称）。 源数据集 (SourceDataset) 中的 tableName 属性配置为使用查找活动的输出。 复制活动将数据从 SQL 表复制到由 SinkDataset 属性指定的 Azure Blob 存储中的一个位置。 


```json
{
    "name": "LookupPipelineDemo",
    "properties": {
        "activities": [
            {
                "name": "LookupActivity",
                "type": "Lookup",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "dataset": { 
                        "referenceName": "LookupDataset", 
                        "type": "DatasetReference" 
                    }
                }
            },
            {
                "name": "CopyActivity",
                "type": "Copy",
                "typeProperties": {
                    "source": { 
                        "type": "SqlSource", 
                        "sqlReaderQuery": "select * from @{activity('LookupActivity').output.firstRow.tableName}" 
                    },
                    "sink": { 
                        "type": "BlobSink" 
                    }
                },                
                "dependsOn": [ 
                    { 
                        "activity": "LookupActivity", 
                        "dependencyConditions": [ "Succeeded" ] 
                    }
                 ],
                "inputs": [ 
                    { 
                        "referenceName": "SourceDataset", 
                        "type": "DatasetReference" 
                    } 
                ],
                "outputs": [ 
                    { 
                        "referenceName": "SinkDataset", 
                        "type": "DatasetReference" 
                    } 
                ]
            }
        ]
    }
}
```

### <a name="lookup-dataset"></a>查找数据集
查找数据集是指由 AzureStorageLinkedService 类型指定的 Azure 存储查找文件夹中的 *sourcetable.json* 文件。 

```json
{
    "name": "LookupDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "lookup",
            "fileName": "sourcetable.json",
            "format": {
                "type": "JsonFormat",
                "filePattern": "SetOfObjects"
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="source-dataset-for-the-copy-activity"></a>复制活动的源数据集
源数据集使用的是查找活动的输出，即 SQL 表名称。 复制活动将数据从此 SQL 表复制到由接收器数据集指定的 Azure Blob 存储中的一个位置。 

```json
{
    "name": "SourceDataset",
    "properties": {
        "type": "AzureSqlTable",
        "typeProperties":{
            "tableName": "@{activity('LookupActivity').output.firstRow.tableName}"
        },
        "linkedServiceName": {
            "referenceName": "AzureSqlLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="sink-dataset-for-the-copy-activity"></a>复制活动的接收器数据集
复制活动将数据从 SQL 表复制到由 AzureStorageLinkedService 属性指定的 Azure 存储中 *csv* 文件夹下的 *filebylookup.csv* 文件。 

```json
{
    "name": "SinkDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "csv",
            "fileName": "filebylookup.csv",
            "format": {
                "type": "TextFormat"                                                                    
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Azure 存储链接服务
此存储帐户包含 JSON 文件和 SQL 表名称。 

```json
{
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "value": "DefaultEndpointsProtocol=https;AccountName=<StorageAccountName>;AccountKey=<StorageAccountKey>",
                "type": "SecureString"
            }
        }
    },
        "name": "AzureStorageLinkedService"
}
```

### <a name="azure-sql-database-linked-service"></a>Azure SQL 数据库链接服务
此 Azure SQL 数据库实例包含要复制到 Blob 存储的数据。 

```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "description": "",
        "typeProperties": {
            "connectionString": {
                "value": "Server=<server>;Initial Catalog=<database>;User ID=<user>;Password=<password>;",
                "type": "SecureString"
            }
        }
    }
}
```

### <a name="sourcetablejson"></a>sourcetable.json

#### <a name="set-of-objects"></a>对象集

```json
{
  "Id": "1",
  "tableName": "Table1",
}
{
   "Id": "2",
  "tableName": "Table2",
}
```

#### <a name="array-of-objects"></a>对象数组

```json
[ 
    {
        "Id": "1",
          "tableName": "Table1",
    }
    {
        "Id": "2",
        "tableName": "Table2",
    }
]
```

## <a name="next-steps"></a>后续步骤
查看数据工厂支持的其他控制流活动： 

- [执行管道活动](control-flow-execute-pipeline-activity.md)
- [For Each 活动](control-flow-for-each-activity.md)
- [获取元数据活动](control-flow-get-metadata-activity.md)
- [Web 活动](control-flow-web-activity.md)
