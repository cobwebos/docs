---
title: Azure 数据工厂中的获取元数据活动 | Microsoft Docs
description: 了解如何使用 SQL Server 存储过程活动从数据工厂管道调用 Azure SQL 数据库或 Azure SQL 数据仓库中的存储过程。
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/10/2018
ms.author: shlo
ms.openlocfilehash: c07199887faf073d19007f1ef410c193bbdbf3ee
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37049360"
---
# <a name="get-metadata-activity-in-azure-data-factory"></a>Azure 数据工厂中的获取元数据活动
GetMetadata 活动可用于检索 Azure 数据工厂中的任何数据的**元数据**。 此活动可用于以下方案：

- 验证任何数据的元数据信息
- 数据就绪/可用时触发管道

控制流中有以下功能：

- 获取元数据活动的输出可用于条件表达式以执行验证。
- 满足条件时可通过 Do-Until 循环触发管道

## <a name="supported-capabilities"></a>支持的功能

GetMetadata 活动将数据集作为必要输入，并输出可用作活动输出的元数据信息。 目前支持以下连接器，这些连接器具有可检索的相应元数据：

>[!NOTE]
>如果在自承载 Integration Runtime 上运行 GetMetadata 活动，3.6 或更高版本将支持该最新功能。 

### <a name="supported-connectors"></a>受支持的连接器

**文件存储：**

| 连接器/元数据 | itemName<br>（文件/文件夹） | itemType<br>（文件/文件夹） | size<br>（文件） | created<br>（文件/文件夹） | lastModified<br>（文件/文件夹） |childItems<br>（文件夹） |contentMD5<br>（文件） | structure<br/>（文件） | columnCount<br>（文件） | exists<br>（文件/文件夹） |
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| Amazon S3 | √/√ | √/√ | √ | x/x | √/√* | √ | x | √ | √ | √/√* |
| Azure Blob | √/√ | √/√ | √ | x/x | √/√* | √ | √ | √ | √ | √/√ |
| Azure Data Lake Store | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| Azure 文件存储 | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| 文件系统 | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| SFTP | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| FTP | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |

- 对于 Amazon S3，`lastModified` 适用于 Bucket 和密钥，但不适用于虚拟文件夹；而 `exists` 适用于 Bucket 和密钥，但不适用于前缀或虚拟文件夹。
- 对于 Azure Blob，`lastModified` 适用于容器和 blob，但不适用于虚拟文件夹。

**关系数据库：**

| 连接器/元数据 | structure | columnCount | exists |
|:--- |:--- |:--- |:--- |
| Azure SQL 数据库 | √ | √ | √ |
| Azure SQL 数据仓库 | √ | √ | √ |
| SQL Server | √ | √ | √ |

### <a name="metadata-options"></a>元数据选项

可以在 GetMetadata 活动字段列表中指定检索以下元数据类型：

| 元数据类型 | 说明 |
|:--- |:--- |
| itemName | 文件或文件夹的名称。 |
| itemType | 文件或文件夹的类型。 输出值为 `File` 或 `Folder`。 |
| size | 文件大小，以字节为单位。 仅适用于文件。 |
| created | 文件或文件夹的创建日期时间。 |
| lastModified | 文件或文件夹的上次修改日期时间。 |
| childItems | 给定文件夹内子文件夹和文件的列表。 仅适用于文件夹。 输出值为每个子项的名称和类型的列表。 |
| contentMD5 | 文件的 MD5。 仅适用于文件。 |
| structure | 文件或关系数据库表内的数据结构。 输出值为列名称和列类型列表。 |
| columnCount | 文件或关系表内的列数。 |
| exists| 是否存在某个文件/文件夹/表。 请注意，如果在 GetaMetadata 字段列表中指定了“exists”，那么即使不存在该项（文件/文件夹/表），活动也不会失败，而是在输出中返回 `exists: false`。 |

>[!TIP]
>如果要验证是否存在某个文件/文件夹/表，请在 GetMetadata 活动字段列表中指定 `exists`，然后可以检查活动输出中的 `exists: true/false` 结果。 如果未在该字段列表中配置 `exists`，则当找不到对象时，GetMetadata 活动将失败。

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

属性 | 说明 | 必选
-------- | ----------- | --------
fieldList | 列出了所需元数据信息的类型。 有关受支持的元数据，请参阅[元数据选项](#metadata-options)部分中的详细信息。 | 是 
dataset | 引用数据集，其元数据活动将由获取源数据活动检索。 有关受支持的连接器，请参阅[支持的功能](#supported-capabilities)部分；有关数据集语法详细信息，请参考连接器主题。 | 是

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
