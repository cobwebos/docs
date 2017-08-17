---
title: "Azure 数据工厂复制活动容错 - 跳过不兼容行 | Microsoft Docs"
description: "了解容错，即如何使用 Azure 数据工厂在复制过程中跳过不兼容行"
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
ms.translationtype: HT
ms.sourcegitcommit: 0425da20f3f0abcfa3ed5c04cec32184210546bb
ms.openlocfilehash: d613537657af3bbe379a53e92532bf6b184d762b
ms.contentlocale: zh-cn
ms.lasthandoff: 07/20/2017

---
# <a name="copy-activity-fault-tolerance---skip-incompatible-rows"></a>复制活动容错 - 跳过不兼容行

通过[复制活动](data-factory-data-movement-activities.md)，可以在数据源与接收器数据存储之间复制数据时，通过两种不同的方式来处理不兼容行。 可以选择在遇到不兼容数据时中止复制活动并显示故障（默认行为），也可以选择通过跳过不兼容行继续复制所有数据。 此外，还可以选择在 Azure Blob 中记录不兼容行，从而探究失败原因，修复数据源中的数据，然后再重试。

## <a name="supported-scenarios"></a>支持的方案
目前，复制活动支持在复制期间检测、跳过和记录以下不兼容情形：

- **数据源与接收器的本机数据类型不兼容**

    例如，要将 Azure Blob 中的 CSV 文件复制到 Azure SQL 数据库，而 Azure SQL 数据库中定义的架构包含三个 INT 类型列。 源 CSV 文件中包含数字数据的行（例如，`123,456,789`）复制成功，而包含非数字值的行（例如，`123,456,abc`）则作为不兼容行被跳过。

- **数据源与接收器的列数不一致**

    例如，要将 Azure Blob 中的 CSV 文件复制到 Azure SQL 数据库，而 SQL Azure 中定义的架构包含六列。 源 CSV 文件中包含六列的行复制成功，而包含其他列数的行则作为不兼容行被跳过。

- **写入关系数据库时发生主键冲突**

    例如，要将数据从 SQL Server 复制到 Azure SQL 数据库，接收器 Azure SQL 数据库中定义了主键，而数据源 SQL Server 中未定义此类主键。 源中可以有重复行，但写入接收器时则不允许。 复制活动仅将第一行复制到接收器，并跳过将数据源中包含重复主键值的第二行或其他行复制到接收器。

## <a name="configuration"></a>配置
下面的 JSON 定义示例展示了如何配置在复制活动中跳过不兼容行：

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
| enableSkipIncompatibleRow | 允许或不允许在复制期间跳过不兼容行。 | True<br/>False（默认值） | 否 |
| redirectIncompatibleRowSettings | 若要记录不兼容行，可以指定的一组属性。 | &nbsp; | 否 |
| linkedServiceName | Azure 存储的链接服务，用于存储包含所有跳过行的记录。 | 指定 [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) 或 [AzureStorageSas](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) 链接服务的名称，以指代用于存储日志文件的存储实例。 | 否 |
| path | 包含所有跳过行的日志文件的路径。 | 指定要用于记录不兼容数据的 Blob 存储路径。 如果未提供路径，服务会为用户创建一个容器。 | 否 |

## <a name="monitoring"></a>监视
复制活动运行完成后，可以在监视部分看到跳过的行数，如下所示：

![监视跳过的不兼容行](./media/data-factory-copy-activity-fault-tolerance/skip-incompatible-rows-monitoring.png)

如果配置记录不兼容行，可以在下列路径中找到日志文件，从而查看跳过行和导致数据不兼容的根本原因：`https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv`。

文件中记录了原始数据和对应的错误。 下面的示例展示了日志文件内容：
```
data1, data2, data3, UserErrorInvalidDataValue,Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'.,
data4, data5, data6, Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4).,
```

## <a name="next-steps"></a>后续步骤
若要详细了解 Azure 数据工厂复制活动，请参阅[使用复制活动移动数据](data-factory-data-movement-activities.md)。
