---
title: "通过跳过不兼容行向 Azure 数据工厂复制活动添加容错 | Microsoft Docs"
description: "了解如何在复制时通过跳过不兼容行向 Azure 数据工厂添加容错"
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
ms.date: 07/19/2017
ms.author: jingwang
robots: noindex
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 7a8c866106f7e2c2538a9cf6c44cb34ddfaa2887
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---
# <a name="add-fault-tolerance-in-copy-activity-by-skipping-incompatible-rows"></a>通过跳过不兼容行向复制活动添加容错

在数据源与接收器数据存储之间复制数据时，可通过 Azure 数据工厂[复制活动](data-factory-data-movement-activities.md)提供的两种方式处理不兼容行：

- 遇到不兼容数据时，可以中止复制活动并让其失败（默认行为）。
- 通过添加容错并跳过不兼容数据行，可继续复制所有数据。 此外，还可将不兼容行记录在 Azure Blob 存储中。 然后，可以检查日志了解失败原因，修复数据源上的数据，并重试复制活动。

## <a name="supported-scenarios"></a>支持的方案
复制活动支持三种检测、跳过和记录不兼容数据的方案：

- **源数据类型与接收器本机类型不兼容**

    例如：使用包含三个 INT 类型的列的架构定义，将数据从 Blob 存储中的 CSV 文件复制到 SQL 数据库。 包含数值数据的 CSV 文件行（如 `123,456,789`）会成功复制到接收器存储。 但是，包含非数字值的行（如 `123,456,abc`）会被检测为不兼容，并被跳过。

- **源与接收器之间的列数不匹配**

    例如：使用包含六个列的架构定义，将数据从 Blob 存储中的 CSV 文件复制到 SQL 数据库。 包含六个列的 CSV 文件行会成功复制到接收器存储。 包含多于或少于六个列的 CSV 文件行会被检测为不兼容，并被跳过。

- **写入关系数据库时发生主键冲突**

    例如：将数据从 SQL 服务器复制到 SQL 数据库。 接收器 SQL 数据库中定义了主键，但源 SQL 服务器中未定义此类主键。 源中的重复行无法复制到接收器。 复制活动仅将源数据的第一行复制到接收器。 包含重复主键值的后续源行会被检测为不兼容，并被跳过。

## <a name="configuration"></a>配置
下面的 JSON 定义示例用于配置在复制活动中跳过不兼容行：

```json
"typeProperties": {
    "source": {
        "type": "BlobSource"
    },
    "sink": {
        "type": "SqlSink",
    },         
    "enableSkipIncompatibleRow": true,           
    "redirectIncompatibleRowSettings": {
        "linkedServiceName": "BlobStorage",
        "path": "redirectcontainer/erroroutput"
    }
}
```

| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| **enableSkipIncompatibleRow** | 允许或不允许在复制期间跳过不兼容行。 | True<br/>False（默认值） | 否 |
| **redirectIncompatibleRowSettings** | 若要记录不兼容行，可以指定的一组属性。 | &nbsp; | 否 |
| **linkedServiceName** | Azure 存储的链接服务，用于存储包含跳过的行的记录。 | [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) 或 [AzureStorageSas](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) 链接服务的名称，可指代希望用于存储日志文件的存储实例。 | 否 |
| **路径** | 包含跳过行的日志文件的路径。 | 指定要用于记录不兼容数据的 Blob 存储路径。 如果未提供路径，服务会为用户创建一个容器。 | 否 |

## <a name="monitoring"></a>监视
复制活动运行完成后，可以在监视部分看到跳过的行数：

![监视器跳过的不兼容行](./media/data-factory-copy-activity-fault-tolerance/skip-incompatible-rows-monitoring.png)

如果配置为记录不兼容行，可以在下列路径中找到日志文件：`https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv` 在日志文件中，可以查看跳过的行和导致数据不兼容的根本原因。

文件中记录了原始数据和对应的错误。 下面的示例展示了日志文件内容：
```
data1, data2, data3, UserErrorInvalidDataValue,Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'.,
data4, data5, data6, Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4).
```

## <a name="next-steps"></a>后续步骤
要详细了解 Azure 数据工厂复制活动，请参阅[使用复制活动移动数据](data-factory-data-movement-activities.md)。
