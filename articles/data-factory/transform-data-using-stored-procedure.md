---
title: 在 Azure 数据工厂中使用存储过程活动转换数据 | Microsoft Docs
description: 说明如何使用 SQL Server 存储过程活动从数据工厂管道调用 Azure SQL 数据库/数据仓库中的存储过程。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/27/2018
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: 806654b7586895b62b014a49b8b3a00fb18f008f
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2019
ms.locfileid: "57575883"
---
# <a name="transform-data-by-using-the-sql-server-stored-procedure-activity-in-azure-data-factory"></a>在 Azure 数据工厂中使用 SQL Server 存储过程活动转换数据
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [版本 1](v1/data-factory-stored-proc-activity.md)
> * [当前版本](transform-data-using-stored-procedure.md)

可使用数据工厂[管道](concepts-pipelines-activities.md)中的数据转换活动将原始数据转换和处理为预测和见解。 存储过程活动是数据工厂支持的转换活动之一。 本文基于[转换数据](transform-data.md)一文编写，它概述了数据转换和数据工厂中支持的转换活动。

> [!NOTE]
> 如果不熟悉 Azure 数据工厂，请在阅读本文之前，先通读 [Azure 数据工厂简介](introduction.md)，并学习以下教程：[教程：转换数据](tutorial-transform-data-spark-powershell.md)。 

可以使用存储过程活动调用企业或 Azure 虚拟机 (VM) 中以下数据存储中的存储过程： 

- Azure SQL 数据库
- Azure SQL 数据仓库
- SQL Server 数据库。  如果使用 SQL Server，请在托管数据库的同一计算机上或在可以访问数据库的单独计算机上安装自托管集成运行时。 自托管集成运行时是一种以安全托管方式将本地/Azure VM 上的数据源与云服务进行连接的组件。 有关详细信息，请参阅[自托管集成运行时](create-self-hosted-integration-runtime.md)一文。

> [!IMPORTANT]
> 将数据复制到 Azure SQL 数据库或 SQL Server 中时，可以使用 sqlWriterStoredProcedureName 属性将复制活动中的 SqlSink 配置为调用存储过程。 有关属性的详细信息，请参阅以下连接器文章：[Azure SQL 数据库](connector-azure-sql-database.md)、[SQL Server](connector-sql-server.md)。 不支持在使用复制活动将数据复制到 Azure SQL 数据仓库时调用存储过程。 但是，可使用存储过程活动来调用 SQL 数据仓库中的存储过程。 
>
> 从 Azure SQL 数据库、SQL Server 或 Azure SQL 数据仓库复制数据时，可以使用 sqlReaderStoredProcedureName 属性将复制活动中的 SqlSource 配置为调用存储过程，以便从源数据库读取数据。 有关详细信息，请参阅以下连接器文章：[Azure SQL 数据库](connector-azure-sql-database.md)、[SQL Server](connector-sql-server.md)、[Azure SQL 数据仓库](connector-azure-sql-data-warehouse.md)          

 

## <a name="syntax-details"></a>语法详细信息
下面是用于定义存储过程活动的 JSON 格式：

```json
{
    "name": "Stored Procedure Activity",
    "description":"Description",
    "type": "SqlServerStoredProcedure",
    "linkedServiceName": {
        "referenceName": "AzureSqlLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "storedProcedureName": "usp_sample",
        "storedProcedureParameters": {
            "identifier": { "value": "1", "type": "Int" },
            "stringData": { "value": "str1" }

        }
    }
}
```

下表描述了其中的 JSON 属性：

| 属性                  | 说明                              | 需要 |
| ------------------------- | ---------------------------------------- | -------- |
| 名称                      | 活动名称                     | 是      |
| description               | 描述活动用途的文本 | 否       |
| type                      | 对于存储过程活动，活动类型是 SqlServerStoredProcedure | 是      |
| linkedServiceName         | 引用注册为数据工厂中的链接服务的 Azure SQL 数据库或 Azure SQL 数据仓库或 SQL Server。 若要了解此链接服务，请参阅[计算链接服务](compute-linked-services.md)一文。 | 是      |
| storedProcedureName       | 指定要调用的存储过程的名称。 | 是      |
| storedProcedureParameters | 指定存储过程的参数值。 使用 `"param1": { "value": "param1Value","type":"param1Type" }` 传递数据源支持的参数值及其类型。 如果需要为参数传递 null，请使用 `"param1": { "value": null }`（全部小写）。 | 否       |

## <a name="error-info"></a>错误信息

当存储过程失败并返回错误详细信息时，你无法直接在活动输出中捕获错误信息。 但是，数据工厂会将其所有活动运行事件抽取到 Azure Monitor。 在数据工厂抽取到 Azure Monitor 的事件中，它会在那里推送错误详细信息。 例如，可以通过这些事件设置电子邮件警报。 有关详细信息，请参阅[使用 Azure Monitor 发警报和监视数据工厂](monitor-using-azure-monitor.md)。

## <a name="next-steps"></a>后续步骤
参阅以下文章了解如何以其他方式转换数据： 

* [U-SQL 活动](transform-data-using-data-lake-analytics.md)
* [Hive 活动](transform-data-using-hadoop-hive.md)
* [Pig 活动](transform-data-using-hadoop-pig.md)
* [MapReduce 活动](transform-data-using-hadoop-map-reduce.md)
* [Hadoop 流式处理活动](transform-data-using-hadoop-streaming.md)
* [Spark 活动](transform-data-using-spark.md)
* [.NET 自定义活动](transform-data-using-dotnet-custom-activity.md)
* [机器学习批处理执行活动](transform-data-using-machine-learning.md)
* [存储过程活动](transform-data-using-stored-procedure.md)
