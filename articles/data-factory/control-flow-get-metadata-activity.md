---
title: Azure 数据工厂中的“获取元数据”活动
description: 了解如何在数据工厂管道中使用“获取元数据”活动。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: ''
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: e891f6675920e7bb90d2a6d007676cdd65f19917
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73679890"
---
# <a name="get-metadata-activity-in-azure-data-factory"></a>Azure 数据工厂中的“获取元数据”活动

可以使用“获取元数据”活动来检索 Azure 数据工厂中任何数据的元数据。 可在以下方案中使用此活动：

- 验证任何数据的元数据。
- 数据就绪/可用时触发管道。

控制流中有以下功能：

- 可以在条件表达式中使用“获取元数据”活动的输出来执行验证。
- 可以在满足条件时通过 Do Until 循环触发管道。

## <a name="capabilities"></a>功能

“获取元数据”活动采用数据集作为输入，并返回元数据信息作为输出。 目前支持以下连接器以及对应的可检索元数据。 返回的元数据的最大大小为 1 MB。

>[!NOTE]
>如果在自承载集成运行时中运行“获取元数据”活动，3.6 或更高版本将支持最新的功能。

### <a name="supported-connectors"></a>受支持的连接器

**文件存储**

| 连接器/元数据 | itemName<br>（文件/文件夹） | itemType<br>（文件/文件夹） | size<br>（文件） | created<br>（文件/文件夹） | lastModified<br>（文件/文件夹） |childItems<br>（文件夹） |contentMD5<br>（文件） | structure<br/>（文件） | columnCount<br>（文件） | exists<br>（文件/文件夹） |
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| [Amazon S3](connector-amazon-simple-storage-service.md) | √/√ | √/√ | √ | x/x | √/√* | √ | x | √ | √ | √/√* |
| [Google Cloud Storage](connector-google-cloud-storage.md) | √/√ | √/√ | √ | x/x | √/√* | √ | x | √ | √ | √/√* |
| [Azure Blob 存储](connector-azure-blob-storage.md) | √/√ | √/√ | √ | x/x | √/√* | √ | √ | √ | √ | √/√ |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Azure 文件](connector-azure-file-storage.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [文件系统](connector-file-system.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [SFTP](connector-sftp.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [FTP](connector-ftp.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |

- 对于 Amazon S3 和 Google 云存储，`lastModified` 适用于桶和键，但不适用于虚拟文件夹；而 `exists` 适用于桶和键，但不适用于前缀或虚拟文件夹。
- 对于 Azure Blob 存储，`lastModified` 适用于容器和 Blob，但不适用于虚拟文件夹。

**关系数据库**

| 连接器/元数据 | structure | columnCount | exists |
|:--- |:--- |:--- |:--- |
| [Azure SQL 数据库](connector-azure-sql-database.md) | √ | √ | √ |
| [Azure SQL 数据库托管实例](connector-azure-sql-database-managed-instance.md) | √ | √ | √ |
| [Azure SQL 数据仓库](connector-azure-sql-data-warehouse.md) | √ | √ | √ |
| [SQL Server](connector-sql-server.md) | √ | √ | √ |

### <a name="metadata-options"></a>元数据选项

可以在“获取元数据”活动字段列表中指定以下元数据类型，以检索相应的信息：

| 元数据类型 | 说明 |
|:--- |:--- |
| itemName | 文件或文件夹的名称。 |
| itemType | 文件或文件夹的类型。 返回的值为 `File` 或 `Folder`。 |
| size | 文件大小，以字节为单位。 仅适用于文件。 |
| created | 文件或文件夹的创建日期时间。 |
| lastModified | 文件或文件夹的上次修改日期时间。 |
| childItems | 给定文件夹中的子文件夹和文件列表。 仅适用于文件夹。 返回的值为每个子项的名称和类型列表。 |
| contentMD5 | 文件的 MD5。 仅适用于文件。 |
| structure | 文件或关系数据库表的数据结构。 返回的值为列名称和列类型列表。 |
| columnCount | 文件或关系表中的列数。 |
| exists| 是否存在某个文件、文件夹或表。 请注意，如果在“获取元数据”字段列表中指定了 `exists`，那么，即使不存在该文件、文件夹或表，该活动也不会失败， 而是在输出中返回 `exists: false`。 |

>[!TIP]
>若要验证是否存在某个文件、文件夹或表，请在“获取元数据”活动字段列表中指定 `exists`。 然后可以检查活动输出中的 `exists: true/false` 结果。 如果未在该字段列表中指定 `exists`，那么，在找不到对象时，“获取元数据”活动将会失败。

>[!NOTE]
>从文件存储获取元数据以及配置 `modifiedDatetimeStart` 或 `modifiedDatetimeEnd` 时，输出中的 `childItems` 只包含给定路径中其最近修改时间在指定范围内的文件。 它不包含子文件夹中的项。

## <a name="syntax"></a>语法

**获取元数据活动**

```json
{
    "name": "MyActivity",
    "type": "GetMetadata",
    "typeProperties": {
        "fieldList" : ["size", "lastModified", "structure"],
        "dataset": {
            "referenceName": "MyDataset",
            "type": "DatasetReference"
        }
    }
}
```

**Dataset**

```json
{
    "name": "MyDataset",
    "properties": {
    "type": "AzureBlob",
        "linkedService": {
            "referenceName": "StorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath":"container/folder",
            "filename": "file.json",
            "format":{
                "type":"JsonFormat"
            }
        }
    }
}
```

## <a name="type-properties"></a>Type 属性

目前，“获取元数据”活动可以返回以下类型的元数据信息：

属性 | 说明 | 必选
-------- | ----------- | --------
fieldList | 所需元数据信息的类型。 有关支持的元数据的详细信息，请参阅本文的[元数据选项](#metadata-options)部分。 | 是 
dataset | 引用数据集，其元数据将由“获取元数据”活动检索。 有关支持的连接器的信息，请参阅[功能](#capabilities)部分。 有关数据集语法详细信息，请参阅特定的连接器主题。 | 是
formatSettings | 使用格式类型数据集时适用。 | 否
storeSettings | 使用格式类型数据集时适用。 | 否

## <a name="sample-output"></a>示例输出

“获取元数据”的结果显示在活动输出中。 以下两个示例演示了大量的元数据选项。 若要在后续活动中使用这些结果，请使用以下模式：`@{activity('MyGetMetadataActivity').output.itemName}`。

### <a name="get-a-files-metadata"></a>获取文件的元数据

```json
{
  "exists": true,
  "itemName": "test.csv",
  "itemType": "File",
  "size": 104857600,
  "lastModified": "2017-02-23T06:17:09Z",
  "created": "2017-02-23T06:17:09Z",
  "contentMD5": "cMauY+Kz5zDm3eWa9VpoyQ==",
  "structure": [
    {
        "name": "id",
        "type": "Int64"
    },
    {
        "name": "name",
        "type": "String"
    }
  ],
  "columnCount": 2
}
```

### <a name="get-a-folders-metadata"></a>获取文件夹的元数据

```json
{
  "exists": true,
  "itemName": "testFolder",
  "itemType": "Folder",
  "lastModified": "2017-02-23T06:17:09Z",
  "created": "2017-02-23T06:17:09Z",
  "childItems": [
    {
      "name": "test.avro",
      "type": "File"
    },
    {
      "name": "folder hello",
      "type": "Folder"
    }
  ]
}
```

## <a name="next-steps"></a>后续步骤
了解数据工厂支持的其他控制流活动：

- [执行管道活动](control-flow-execute-pipeline-activity.md)
- [ForEach 活动](control-flow-for-each-activity.md)
- [Lookup 活动](control-flow-lookup-activity.md)
- [Web 活动](control-flow-web-activity.md)
