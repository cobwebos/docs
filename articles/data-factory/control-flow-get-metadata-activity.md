---
title: Azure 数据工厂中的获取元数据活动 | Microsoft Docs
description: 了解如何在数据工厂管道中使用 GetMetadata 活动。
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
ms.date: 08/06/2019
ms.author: jingwang
ms.openlocfilehash: b819a990b9f607aaf70bf2e16a5857de3f7306cc
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2019
ms.locfileid: "68827492"
---
# <a name="get-metadata-activity-in-azure-data-factory"></a>Azure 数据工厂中的获取元数据活动

GetMetadata 活动可用于检索 Azure 数据工厂中的任何数据的**元数据**。 此活动可用于以下方案：

- 验证任何数据的元数据信息
- 数据就绪/可用时触发管道

控制流中有以下功能：

- 获取元数据活动的输出可用于条件表达式以执行验证。
- 满足条件时可通过 Do-Until 循环触发管道

## <a name="supported-capabilities"></a>支持的功能

GetMetadata 活动将数据集作为必要输入，并输出可用作活动输出的元数据信息。 目前，支持具有相应可检索元数据的以下连接器，并且支持的最大元数据大小高达 **1MB**。

>[!NOTE]
>如果在自承载 Integration Runtime 上运行 GetMetadata 活动，3.6 或更高版本将支持该最新功能。 

### <a name="supported-connectors"></a>受支持的连接器

**文件存储：**

| 连接器/元数据 | itemName<br>（文件/文件夹） | itemType<br>（文件/文件夹） | 大小<br>（文件） | 已创建<br>（文件/文件夹） | lastModified<br>（文件/文件夹） |childItems<br>（文件夹） |contentMD5<br>（文件） | 结构<br/>（文件） | columnCount<br>（文件） | 存在<br>（文件/文件夹） |
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| [Amazon S3](connector-amazon-simple-storage-service.md) | √/√ | √/√ | √ | x/x | √/√* | √ | X | √ | √ | √/√* |
| [Google Cloud Storage](connector-google-cloud-storage.md) | √/√ | √/√ | √ | x/x | √/√* | √ | X | √ | √ | √/√* |
| [Azure Blob](connector-azure-blob-storage.md) | √/√ | √/√ | √ | x/x | √/√* | √ | √ | √ | √ | √/√ |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | √/√ | √/√ | √ | x/x | √/√ | √ | X | √ | √ | √/√ |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | √/√ | √/√ | √ | x/x | √/√ | √ | X | √ | √ | √/√ |
| [Azure 文件存储](connector-azure-file-storage.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | X | √ | √ | √/√ |
| [文件系统](connector-file-system.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | X | √ | √ | √/√ |
| [SFTP](connector-sftp.md) | √/√ | √/√ | √ | x/x | √/√ | √ | X | √ | √ | √/√ |
| [FTP](connector-ftp.md) | √/√ | √/√ | √ | x/x | √/√ | √ | X | √ | √ | √/√ |

- 对于 Amazon S3 和 Google 云存储, `lastModified`适用于 bucket 和密钥, 但不适用于虚拟文件夹;; `exists`适用于 bucket 和密钥, 但不适用于前缀或虚拟文件夹。
- 对于 Azure Blob，`lastModified` 适用于容器和 blob，但不适用于虚拟文件夹。

**关系数据库：**

| 连接器/元数据 | 结构 | columnCount | 存在 |
|:--- |:--- |:--- |:--- |
| [Azure SQL 数据库](connector-azure-sql-database.md) | √ | √ | √ |
| [Azure SQL 数据库托管实例](connector-azure-sql-database-managed-instance.md) | √ | √ | √ |
| [Azure SQL 数据仓库](connector-azure-sql-data-warehouse.md) | √ | √ | √ |
| [SQL Server](connector-sql-server.md) | √ | √ | √ |

### <a name="metadata-options"></a>元数据选项

可以在 GetMetadata 活动字段列表中指定检索以下元数据类型：

| 元数据类型 | 描述 |
|:--- |:--- |
| itemName | 文件或文件夹的名称。 |
| itemType | 文件或文件夹的类型。 输出值为 `File` 或 `Folder`。 |
| 大小 | 文件大小，以字节为单位。 仅适用于文件。 |
| 已创建 | 文件或文件夹的创建日期时间。 |
| lastModified | 文件或文件夹的上次修改日期时间。 |
| childItems | 给定文件夹内子文件夹和文件的列表。 仅适用于文件夹。 输出值为每个子项的名称和类型的列表。 |
| contentMD5 | 文件的 MD5。 仅适用于文件。 |
| 结构 | 文件或关系数据库表内的数据结构。 输出值为列名称和列类型列表。 |
| columnCount | 文件或关系表内的列数。 |
| 存在| 是否存在某个文件/文件夹/表。 请注意，如果在 GetaMetadata 字段列表中指定了“exists”，那么即使不存在该项（文件/文件夹/表），活动也不会失败，而是在输出中返回 `exists: false`。 |

>[!TIP]
>如果要验证是否存在某个文件/文件夹/表，请在 GetMetadata 活动字段列表中指定 `exists`，然后可以检查活动输出中的 `exists: true/false` 结果。 如果未在该字段列表中配置 `exists`，则当找不到对象时，GetMetadata 活动将失败。

>[!NOTE]
>当从文件存储区中获取元数据`modifiedDatetimeStart`并配置和`modifiedDatetimeEnd`/或`childItems`时, 中的输出只返回在该范围内的最后修改时间 (而不是子文件夹) 下给定路径下的文件。

## <a name="syntax"></a>语法

**GetMetadata 活动：**

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

**数据集：**

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

目前，GetMetadata 活动可以提取以下类型的元数据信息。

属性 | 说明 | 必填
-------- | ----------- | --------
fieldList | 列出了所需元数据信息的类型。 有关受支持的元数据，请参阅[元数据选项](#metadata-options)部分中的详细信息。 | 是 
dataset | 引用数据集，其元数据活动将由获取源数据活动检索。 有关受支持的连接器，请参阅[支持的功能](#supported-capabilities)部分；有关数据集语法详细信息，请参考连接器主题。 | 是
formatSettings | 使用格式类型数据集时应用 (Parquet, DelimitedText)。 | 否
storeSettings | 使用格式类型数据集时应用 (Parquet, DelimitedText)。 | 否

## <a name="sample-output"></a>示例输出

GetMetadata 结果显示在活动输出中。 下面两个示例在字段列表中选择了详尽的元数据选项作为参考。 若要在后续活动中使用该结果，请使用 `@{activity('MyGetMetadataActivity').output.itemName}` 模式。

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
查看数据工厂支持的其他控制流活动： 

- [Execute Pipeline 活动](control-flow-execute-pipeline-activity.md)
- [For Each 活动](control-flow-for-each-activity.md)
- [查找活动](control-flow-lookup-activity.md)
- [Web 活动](control-flow-web-activity.md)
