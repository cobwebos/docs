---
title: "Azure 数据工厂中的获取元数据活动 | Microsoft Docs"
description: "了解如何使用 SQL Server 存储过程活动从数据工厂管道调用 Azure SQL 数据库或 Azure SQL 数据仓库中的存储过程。"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: shlo
ms.openlocfilehash: 99182b2ed91f6d60f499be0078077bf52fe8b366
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="get-metadata-activity-in-azure-data-factory"></a>Azure 数据工厂中的获取元数据活动
获取元数据活动可用于检索 Azure 数据工厂中任何数据的元数据。 仅版本 2 的数据工厂支持此活动。 它可用于以下方案：

- 验证任何数据的元数据信息
- 数据就绪/可用时触发管道

控制流中有以下功能：
- 获取元数据活动的输出可用于条件表达式以执行验证。
- 满足条件时可通过 Do-Until 循环触发管道

获取元数据活动将数据集作为必要输入，并输出可用作输出的元数据信息。 目前，仅支持 Azure Blob 数据集。 支持的元数据字段为大小、结构和最后修改时间。  

> [!NOTE]
> 本文适用于目前处于预览状态的版本 2 数据工厂。 如果使用数据工厂服务版本 1（即正式版 (GA），请参阅[数据工厂 V1 文档](v1/data-factory-introduction.md)。


## <a name="syntax"></a>语法

### <a name="get-metadata-activity-definition"></a>获取元数据活动定义：
在以下示例中，获取元数据活动将返回 MyDataset 所表示的数据的元数据。 

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
### <a name="dataset-definition"></a>数据集定义：

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
            "Filename": "file.json",
            "format":{
                "type":"JsonFormat"
                "nestedSeperator": ","
            }
        }
    }
}
```

### <a name="output"></a>输出
```json
{
    "size": 1024,
    "structure": [
        {
            "name": "id",
            "type": "Int64"
        }, 
    ],
    "lastModified": "2016-07-12T00:00:00Z"
}
```

## <a name="type-properties"></a>Type 属性
目前，获取元数据活动可以从 Azure 存储数据集中提取以下类型的元数据信息。

属性 | 说明 | 允许值 | 必选
-------- | ----------- | -------------- | --------
fieldList | 列出了所需元数据信息的类型。  | <ul><li>size</li><li>structure</li><li>lastModified</li></ul> |    否<br/>如果为空，则活动将返回所有支持的 3 个元数据信息。 
dataset | 引用数据集，其元数据活动将由获取源数据活动检索。 <br/><br/>当前支持的数据集类型是 Azure Blob。 两个子属性为： <ul><li><b>referenceName</b>：引用现有的 Azure Blob 数据集</li><li><b>type</b>：由于数据集用于引用，它的类型为“DatasetReference”</li></ul> |    <ul><li>String</li><li>DatasetReference</li></ul> | 是

## <a name="next-steps"></a>后续步骤
查看数据工厂支持的其他控制流活动： 

- [Execute Pipeline 活动](control-flow-execute-pipeline-activity.md)
- [For Each 活动](control-flow-for-each-activity.md)
- [查找活动](control-flow-lookup-activity.md)
- [Web 活动](control-flow-web-activity.md)