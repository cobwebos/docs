---
title: 使用 Azure 数据工厂模板从数据库大容量复制到 Azure 数据资源管理器
description: 本主题介绍如何使用 Azure 数据工厂模板从数据库大容量复制到 Azure 数据资源管理器
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/08/2019
ms.openlocfilehash: 5a6aebd276ef8658da9ca763be7da5c38a9c772a
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2019
ms.locfileid: "70873428"
---
# <a name="use-azure-data-factory-template-for-bulk-copy-from-database-to-azure-data-explorer"></a>使用 Azure 数据工厂模板从数据库大容量复制到 Azure 数据资源管理器

Azure 数据资源管理器是一个快速、完全托管的数据分析服务，用于实时分析从应用程序、网站和 IoT 设备等许多源流式传输的大量数据。 Azure 数据工厂是一个完全托管的基于云的数据集成服务。 你可以使用 Azure 数据工厂服务，使用现有系统中的数据填充 Azure 数据资源管理器数据库，并在生成分析解决方案时节省时间。 

[Azure 数据工厂模板](/azure/data-factory/solution-templates-introduction)是预定义的 Azure 数据工厂管道，使你能够快速开始处理数据工厂，并缩短构建数据集成项目的开发时间。 **从数据库大容量复制到 Azure 数据资源管理器**模板是使用**查找**和**ForEach**活动创建的。 您可以使用模板为每个数据库或表创建多个管道，以便更快地复制数据。 

> [!IMPORTANT]
> * 使用 "**从数据库大容量复制到 azure 数据资源管理器**模板" 可将大量数据从数据库（例如 SQL server）和 Google BigQuery 复制到 azure 数据资源管理器。 
> * 使用[复制工具](data-factory-load-data.md)将少量或中等数量的数据复制到 Azure 数据资源管理器中。 

## <a name="prerequisites"></a>先决条件

* 如果还没有 Azure 订阅，可以在开始前创建一个[免费 Azure 帐户](https://azure.microsoft.com/free/)。
* [Azure 数据资源管理器群集和数据库](create-cluster-database-portal.md)
* [创建数据工厂](data-factory-load-data.md#create-a-data-factory)
* 数据库中的数据源

## <a name="create-controltabledataset"></a>创建 ControlTableDataset

**ControlTableDataset**指示将在管道中从源复制到目标的数据。 行数指示复制数据所需的管道总数。 应将**ControlTableDataset**定义为源数据库的一部分。

SQL Server 源表格式的示例：
    
```sql   
CREATE TABLE control_table (
PartitionId int,
SourceQuery varchar(255),
ADXTableName varchar(255)
);
```
    
|属性  |描述  | 示例
|---------|---------| ---------|
|PartitionId   |   复制顺序 | 1  |  
|SourceQuery   |   指示将在管道运行时中复制哪些数据的查询 | <br>`select * from table where lastmodifiedtime  LastModifytime >= ''2015-01-01 00:00:00''>` </br>    
|ADXTableName  |  目标表名称 | MyAdxTable       |  

如果你的**ControlTableDataset**采用不同的格式，请为你的格式创建一个类似的**ControlTableDataset** 。

## <a name="use-bulk-copy-from-database-to-azure-data-explorer-template"></a>使用从数据库到 Azure 数据资源管理器模板的大容量复制

1. 在 "**开始**" 页中，选择 "**从模板中创建管道**" 磁贴，或**从模板**> **+**  > "管道" 中选择铅笔图标（作者选项卡），以打开模板库。

    ![Azure 数据工厂入门](media/data-factory-template/adf-get-started.png)

1. 选择 "模板" "**从数据库大容量复制到 Azure" 数据资源管理器**。
 
    ![从模板中选择管道](media/data-factory-template/pipeline-from-template.png)

1.  在 "**从数据库大容量复制到 Azure 数据资源管理器**" 窗口中，从下拉端选择现有数据集。 

    * **ControlTableDataset** -链接服务到控制表，用于指示从源复制到目标的数据以及将数据放置在目标中的位置。 
    * **SourceDataset** –将服务链接到源数据库。 
    * **AzureDataExplorerTable** -Azure 数据资源管理器表。 如果数据集不存在，则[创建 Azure 数据资源管理器链接服务](data-factory-load-data.md#create-the-azure-data-explorer-linked-service)以添加数据集。
    * 选择“使用此模板”。

    ![配置大容量复制 Azure 数据资源管理器模板](media/data-factory-template/configure-bulk-copy-adx-template.png)

1. 选择画布中活动外部的区域，以访问模板管道。 选择 "**参数**" 可输入表的参数，其中包括**名称**（控制表名称）和**默认值**（列名称）。

    ![管道参数](media/data-factory-template/pipeline-parameters.png)

1.  选择查找活动**GetPartitionList**，查看默认设置。 将自动创建查询。
1.  选择命令活动**ForEachPartition**，选择**设置**
    * **批次计数**：选择1-50 中的数字。 此选择将确定在达到**ControlTableDataset**行数之前并行运行的管道数。 
    * 不要选择 "**顺序**" 复选框，以便管道批处理以并行方式运行。

    ![ForEachPartition 设置](media/data-factory-template/foreach-partition-settings.png)

    > [!TIP]
    > 最佳做法是并行运行多个管道，以便可以更快地复制数据。 将源表中的数据分区，并根据日期和表为每个管道分配分区，以提高效率。

1. 选择 "**全部验证**" 以验证 ADF 管道。 请参阅**管道验证输出**。

    ![验证模板管道](media/data-factory-template/validate-template-pipelines.png)

1. 如果需要，请选择 "**调试**"，然后**添加 "触发器**" 来运行管道。

    ![运行管道](media/data-factory-template/trigger-run-of-pipeline.png)    


你现在可以使用 "**从数据库大容量复制到 Azure 数据资源管理器**" 模板来有效地从数据库和表中复制大量数据。

## <a name="next-steps"></a>后续步骤

* 了解[使用 Azure 数据工厂将数据复制到 Azure 数据资源管理器](data-factory-load-data.md)的过程。

* 了解 Azure 数据工厂中的 [Azure 数据资源管理器连接器](/azure/data-factory/connector-azure-data-explorer)。

* 了解用于查询数据的 [Azure 数据资源管理器查询](/azure/data-explorer/web-query-data)。






