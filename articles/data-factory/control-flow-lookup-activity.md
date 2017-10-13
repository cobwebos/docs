---
title: "Azure 数据工厂中的查找活动 | Microsoft Docs"
description: "了解如何使用查找活动从外部源查找值。 此输出可进一步由后续活动引用。"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: shlo
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: spelluru
ms.openlocfilehash: 30173f8eea2ccbbcd44018596cf34b3769a64b50
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="lookup-activity-in-azure-data-factory"></a>Azure 数据工厂中的查找活动
查找活动可用于从任何外部源读取或查找记录/表名称/值。 此输出可进一步由后续活动引用。 

查找当前支持以下数据源：
- Azure Blob 中的 JSON 文件
- 本地 JSON 文件
- Azure SQL 数据库（从查询转换为 JSON 数据）
- Azure 表存储（从查询转换为 JSON 数据）

当你想要从配置文件或数据源动态检索文件/记录/表列表时，查找活动会很有帮助。 活动输出可以进一步供其他活动使用，仅对这些项执行特定处理。

> [!NOTE]
> 本文适用于目前处于预览状态的版本 2 的数据工厂。 如果使用数据工厂服务版本 1（即正式版 (GA），请参阅[数据工厂 V1 文档](v1/data-factory-introduction.md)。


## <a name="example"></a>示例
在本示例中，复制活动将 Azure SQL 数据库 SQL 表中的数据复制到 Azure Blob 存储。 SQL 表的名称存储在 Blob 存储中的 JSON 文件中。 查找活动查找运行时的表名称。 此方法可以在无需重新部署管道/数据集的情况下对 JSON 进行动态修改。 

### <a name="pipeline"></a>管道
此管道包含两个活动：查找 和复制。 

- 查找活动配置为使用 LookupDataset，它指的是 Azure Blob 存储中的一个位置。 查找活动在此位置从 JSON 文件读取 SQL 表名称。 
- 复制活动使用查找活动的输出（SQL 表名称）。 源数据集 (SourceDataset) 中的 tableName 配置为使用查找活动的输出。 复制活动将数据从 SQL 表复制到由 SinkDataset 指定的 Azure Blob 存储中的一个位置。 


```json
{
    "name": "LookupPipelineDemo",
    "properties": {
        "activities": [
            {
                "name": "LookupActivity",
                "type": "Lookup",
                "typeProperties": {
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
                        "sqlReaderQuery": "select * from @{activity('LookupActivity').output.tableName}" 
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
查找数据集是指由 AzureStorageLinkedService 指定的 Azure 存储中查找文件夹下的 sourcetable.json 文件。 

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
            "tableName": "@{activity('LookupActivity').output.tableName}"
        },
        "linkedServiceName": {
            "referenceName": "AzureSqlLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="sink-dataset-for-the-copy-activity"></a>复制活动的接收器数据集
复制活动将数据从 SQL 表复制到由 AzureStorageLinkedService 指定的 Azure 存储中 csv 文件夹下的 filebylookup.csv 文件。 

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
此 Azure SQL 数据库包含要复制到 blob 存储的数据。 

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



## <a name="type-properties"></a>Type 属性
Name | 说明 | 类型 | 必选
---- | ----------- | ---- | --------
dataset | 该数据集属性为查找提供数据集引用。 目前，支持的数据集类型包括：<ul><li>FileShareDataset</li><li>AzureBlobDataset</li><li>AzureSqlTableDataset</li><li>AzureTableDataset</li> | 键/值对 | 是
源 | 特定于数据集的源属性，与复制活动源相同 | 键/值对 | 否
firstRowOnly | 返回第一行或所有行。 | 布尔值 | 否

## <a name="next-steps"></a>后续步骤
查看数据工厂支持的其他控制流活动： 

- [Execute Pipeline 活动](control-flow-execute-pipeline-activity.md)
- [For Each 活动](control-flow-for-each-activity.md)
- [Get Metadata 活动](control-flow-get-metadata-activity.md)
- [Web 活动](control-flow-web-activity.md)
