---
title: Azure 数据工厂中的查找活动
description: 了解如何使用查找活动从外部源查找值。 此输出可进一步由后续活动引用。
services: data-factory
documentationcenter: ''
author: linda33wj
ms.author: jingwang
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: 371afbd9380ed1ecf28d0b26e2b4c5cd16ae6317
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92044065"
---
# <a name="lookup-activity-in-azure-data-factory"></a>Azure 数据工厂中的查找活动

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

查找活动可以从任何 Azure 数据工厂支持的数据源检索数据集。 在以下方案中使用它：
- 动态确定哪些对象在后续活动中工作，而不是针对对象名称进行硬编码。 一些对象示例包括文件和表。

查找活动读取并返回配置文件或表的内容。 它还返回执行查询或存储过程的结果。 查找活动的输出可用于后续复制或转换活动（如果它是单一实例值）。 该输出可用于 ForEach 活动（如果它是属性数组）。

## <a name="supported-capabilities"></a>支持的功能

查找活动支持以下数据源。 

查找活动最多可以返回 5000 行；如果结果集包含的记录超过此范围，将返回前 5000 行。 查找活动输出支持的大小最大为 4 MB，如果大小超过限制，则活动将失败。 目前，在超时前，查找活动的最长持续时间为24小时。

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores-for-lookup-activity.md)]

## <a name="syntax"></a>语法

```json
{
    "name":"LookupActivity",
    "type":"Lookup",
    "typeProperties":{
        "source":{
            "type":"<source type>"
        },
        "dataset":{
            "referenceName":"<source dataset name>",
            "type":"DatasetReference"
        },
        "firstRowOnly":<true or false>
    }
}
```

## <a name="type-properties"></a>Type 属性

名称 | 说明 | 类型 | 必需？
---- | ----------- | ---- | --------
dataset | 为查找提供数据集引用。 从每篇相应的连接器文章的“数据集属性”部分中获取详细信息。 | 键/值对 | 是
source | 包含特定于数据集的源属性，与复制活动源相同。 从每篇相应的连接器文章的“复制活动属性”部分中获取详细信息。 | 键/值对 | 是
firstRowOnly | 指示仅返回第一行还是返回所有行。 | Boolean | 不是。 默认为 `true`。

> [!NOTE]
> 
> * 不支持 **ByteArray** 类型的源列。
> * 数据集定义不支持**结构**。 对于文本格式化文件，使用标头行提供列名。
> * 如果查找源是 JSON 文件，则不支持用于重塑 JSON 对象的 `jsonPathDefinition` 设置。 将检索整个对象。

## <a name="use-the-lookup-activity-result"></a>使用查找活动结果

查找结果会返回到活动运行结果的 `output` 节。

* **当 `firstRowOnly` 设置为 `true`（默认值）时**，输出格式如以下代码所示。 查找结果位于固定的 `firstRow` 键下。 若要在后续活动中使用该结果，请使用 `@{activity('LookupActivity').output.firstRow.table` 模式。

    ```json
    {
        "firstRow":
        {
            "Id": "1",
            "schema":"dbo",
            "table":"Table1"
        }
    }
    ```

* **当 `firstRowOnly` 设置为 `false` 时**，输出格式如以下代码所示。 `count` 字段指示返回的记录数。 详细值显示在固定的 `value` 数组下。 在这种情况下，查找活动后跟 [Foreach 活动](control-flow-for-each-activity.md)。 使用 `@activity('MyLookupActivity').output.value` 模式将 `value` 数组传递给 ForEach 活动 `items` 字段。 若要访问 `value` 数组中的元素，请使用以下语法：`@{activity('lookupActivity').output.value[zero based index].propertyname}`。 示例为 `@{activity('lookupActivity').output.value[0].schema}`。

    ```json
    {
        "count": "2",
        "value": [
            {
                "Id": "1",
                "schema":"dbo",
                "table":"Table1"
            },
            {
                "Id": "2",
                "schema":"dbo",
                "table":"Table2"
            }
        ]
    } 
    ```

## <a name="example"></a>示例

在此示例中，管道包含两个活动：查找和复制 。 复制活动将 Azure SQL 数据库实例的 SQL 表中的数据复制到 Azure Blob 存储。 SQL 表的名称存储在 Blob 存储的 JSON 文件中。 查找活动在运行时查找表名。 使用此方法动态修改 JSON。 不需要重新部署管道或数据集。 

本示例演示如何只查找第一行。 若要查找所有行并将结果与 ForEach 活动链接，请参阅[使用 Azure 数据工厂批量复制多个表](tutorial-bulk-copy.md)中的示例。


### <a name="pipeline"></a>管道

- 查找活动配置为使用 **LookupDataset**，该项引用 Azure Blob 存储中的一个位置。 查找活动在此位置从 JSON 文件读取 SQL 表名称。 
- 复制活动使用查找活动的输出，即 SQL 表的名称。 **SourceDataset** 中的 **tableName** 属性配置为使用查找活动的输出。 复制活动将数据从 SQL 表复制到 Azure Blob 存储中的一个位置。 该位置由 **SinkDataset** 属性指定。 

```json
{
    "name": "LookupPipelineDemo",
    "properties": {
        "activities": [
            {
                "name": "LookupActivity",
                "type": "Lookup",
                "dependsOn": [],
                "policy": {
                    "timeout": "7.00:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30,
                    "secureOutput": false,
                    "secureInput": false
                },
                "userProperties": [],
                "typeProperties": {
                    "source": {
                        "type": "JsonSource",
                        "storeSettings": {
                            "type": "AzureBlobStorageReadSettings",
                            "recursive": true
                        },
                        "formatSettings": {
                            "type": "JsonReadSettings"
                        }
                    },
                    "dataset": {
                        "referenceName": "LookupDataset",
                        "type": "DatasetReference"
                    },
                    "firstRowOnly": true
                }
            },
            {
                "name": "CopyActivity",
                "type": "Copy",
                "dependsOn": [
                    {
                        "activity": "LookupActivity",
                        "dependencyConditions": [
                            "Succeeded"
                        ]
                    }
                ],
                "policy": {
                    "timeout": "7.00:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30,
                    "secureOutput": false,
                    "secureInput": false
                },
                "userProperties": [],
                "typeProperties": {
                    "source": {
                        "type": "AzureSqlSource",
                        "sqlReaderQuery": {
                            "value": "select * from [@{activity('LookupActivity').output.firstRow.schema}].[@{activity('LookupActivity').output.firstRow.table}]",
                            "type": "Expression"
                        },
                        "queryTimeout": "02:00:00",
                        "partitionOption": "None"
                    },
                    "sink": {
                        "type": "DelimitedTextSink",
                        "storeSettings": {
                            "type": "AzureBlobStorageWriteSettings"
                        },
                        "formatSettings": {
                            "type": "DelimitedTextWriteSettings",
                            "quoteAllText": true,
                            "fileExtension": ".txt"
                        }
                    },
                    "enableStaging": false,
                    "translator": {
                        "type": "TabularTranslator",
                        "typeConversion": true,
                        "typeConversionSettings": {
                            "allowDataTruncation": true,
                            "treatBooleanAsNumber": false
                        }
                    }
                },
                "inputs": [
                    {
                        "referenceName": "SourceDataset",
                        "type": "DatasetReference",
                        "parameters": {
                            "schemaName": {
                                "value": "@activity('LookupActivity').output.firstRow.schema",
                                "type": "Expression"
                            },
                            "tableName": {
                                "value": "@activity('LookupActivity').output.firstRow.table",
                                "type": "Expression"
                            }
                        }
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "SinkDataset",
                        "type": "DatasetReference",
                        "parameters": {
                            "schema": {
                                "value": "@activity('LookupActivity').output.firstRow.schema",
                                "type": "Expression"
                            },
                            "table": {
                                "value": "@activity('LookupActivity').output.firstRow.table",
                                "type": "Expression"
                            }
                        }
                    }
                ]
            }
        ],
        "annotations": [],
        "lastPublishTime": "2020-08-17T10:48:25Z"
    }
}
```

### <a name="lookup-dataset"></a>查找数据集

查找数据集是指由 AzureBlobStorageLinkedService 类型指定的 Azure 存储查找文件夹中的 sourcetable.json 文件  。 

```json
{
    "name": "LookupDataset",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureBlobStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "annotations": [],
        "type": "Json",
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "fileName": "sourcetable.json",
                "container": "lookup"
            }
        }
    }
}
```

### <a name="source-dataset-for-copy-activity"></a>复制活动的**源**数据集

**源**数据集使用查找活动的输出，即 SQL 表名称。 复制活动将数据从此 SQL 表复制到 Azure Blob 存储中的一个位置。 该位置由**接收器**数据集指定。 

```json
{
    "name": "SourceDataset",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureSqlDatabase",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "schemaName": {
                "type": "string"
            },
            "tableName": {
                "type": "string"
            }
        },
        "annotations": [],
        "type": "AzureSqlTable",
        "schema": [],
        "typeProperties": {
            "schema": {
                "value": "@dataset().schemaName",
                "type": "Expression"
            },
            "table": {
                "value": "@dataset().tableName",
                "type": "Expression"
            }
        }
    }
}
```

### <a name="sink-dataset-for-copy-activity"></a>复制活动的**接收器**数据集

复制活动将数据从 SQL 表复制到 Azure 存储中 **csv** 文件夹下的 **filebylookup.csv** 文件。 该文件由 AzureBlobStorageLinkedService 属性指定。 

```json
{
    "name": "SinkDataset",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureBlobStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "schema": {
                "type": "string"
            },
            "table": {
                "type": "string"
            }
        },
        "annotations": [],
        "type": "DelimitedText",
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "fileName": {
                    "value": "@{dataset().schema}_@{dataset().table}.csv",
                    "type": "Expression"
                },
                "container": "csv"
            },
            "columnDelimiter": ",",
            "escapeChar": "\\",
            "quoteChar": "\""
        },
        "schema": []
    }
}
```

### <a name="sourcetablejson"></a>sourcetable.json

可将以下两种格式用于 sourcetable.json 文件。

#### <a name="set-of-objects"></a>对象集

```json
{
   "Id":"1",
   "schema":"dbo",
   "table":"Table1"
}
{
   "Id":"2",
   "schema":"dbo",
   "table":"Table2"
}
```

#### <a name="array-of-objects"></a>对象数组

```json
[ 
    {
        "Id": "1",
        "schema":"dbo",
        "table":"Table1"
    },
    {
        "Id": "2",
        "schema":"dbo",
        "table":"Table2"
    }
]
```

## <a name="limitations-and-workarounds"></a>限制和解决方法

以下是 Lookup 活动的一些限制以及建议的解决方法。

| 限制 | 解决方法 |
|---|---|
| Lookup 活动最多有 5,000 行，最大大小为 2 MB。 | 设计一个外部管道对内部管道进行迭代的两级管道，该管道会检索不超过最大行数或大小的数据。 |
| | |

## <a name="next-steps"></a>后续步骤
查看数据工厂支持的其他控制流活动： 

- [执行管道活动](control-flow-execute-pipeline-activity.md)
- [ForEach 活动](control-flow-for-each-activity.md)
- [GetMetadata 活动](control-flow-get-metadata-activity.md)
- [Web 活动](control-flow-web-activity.md)
