---
title: 使用 Azure 数据工厂模板从数据库批量复制到 Azure 数据资源管理器
description: 本文介绍如何使用 Azure 数据工厂模板从数据库批量复制到 Azure 数据资源管理器
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/08/2019
ms.openlocfilehash: 884f4e956b37c2def6c25d0acdf20f15eddf7767
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76293549"
---
# <a name="copy-in-bulk-from-a-database-to-azure-data-explorer-by-using-the-azure-data-factory-template"></a>使用 Azure 数据工厂模板从数据库批量复制到 Azure 数据资源管理器 

Azure 数据资源管理器是一个快速、完全托管的数据分析服务。 它可以实时分析从应用程序、网站和 IoT 设备等许多源流式传输的大量数据。 

若要将数据从 Oracle 服务器、Netezza、Teradata 或 SQL Server 中的数据库复制到 Azure 数据资源管理器，你必须从多个表加载大量数据。 通常情况下，必须在每个表中对数据分区，以便使用多个线程从单个表并行加载行。 本文介绍一个可以在这些情况下使用的模板。

[Azure 数据工厂模板](/azure/data-factory/solution-templates-introduction)是预定义的数据工厂管道。 这些模板可以帮助你快速开始使用数据工厂，缩短数据集成项目的开发时间。 

可以使用 *Lookup* 和 *ForEach* 活动创建“从数据库批量复制到 Azure 数据资源管理器”模板。** 为了更快地复制数据，可以使用模板针对每个数据库或每个表创建多个管道。 

> [!IMPORTANT]
> 请务必使用与所要复制的数据量相适应的工具。
> * 使用“从数据库批量复制到 Azure 数据资源管理器”模板可将 SQL Server 和 Google BigQuery 等数据库中的大量数据复制到 Azure 数据资源管理器。** 
> * 使用[数据工厂复制数据工具](data-factory-load-data.md)可将少量或中等数量的数据复制到 Azure 数据资源管理器。** 

## <a name="prerequisites"></a>先决条件

* 如果还没有 Azure 订阅，可以在开始前创建一个[免费 Azure 帐户](https://azure.microsoft.com/free/)。
* [Azure 数据资源管理器群集和数据库](create-cluster-database-portal.md)。
* [创建数据工厂](data-factory-load-data.md#create-a-data-factory)。
* 数据库中的数据源。

## <a name="create-controltabledataset"></a>创建 ControlTableDataset

*ControlTableDataset* 指示要将源中的哪些数据复制到管道中的目标。 行数指示复制这些数据所需的管道总数。 应将 ControlTableDataset 定义为源数据库的一部分。

以下代码演示了 SQL Server 源表格式的示例：
    
```sql   
CREATE TABLE control_table (
PartitionId int,
SourceQuery varchar(255),
ADXTableName varchar(255)
);
```

下表描述了代码元素：

|properties  |说明  | 示例
|---------|---------| ---------|
|PartitionId   |  复制顺序 | 1  |  
|SourceQuery   |  指示在管道运行时期间要复制哪些数据的查询 | <br>`select * from table where lastmodifiedtime  LastModifytime >= ''2015-01-01 00:00:00''>` </br>    
|ADXTableName  |  目标表名称 | MyAdxTable       |  

如果你的 ControlTableDataset 采用不同的格式，请根据自己的格式创建相应的 ControlTableDataset。

## <a name="use-the-bulk-copy-from-database-to-azure-data-explorer-template"></a>使用“从数据库批量复制到 Azure 数据资源管理器”模板

1. 在“开始”窗格中，选择“从模板创建管道”打开“模板库”窗格。************

    ![Azure 数据工厂的“开始”窗格](media/data-factory-template/adf-get-started.png)

1. 选择“从数据库批量复制到 Azure 数据资源管理器”模板。****
 
    ![“从数据库批量复制到 Azure 数据资源管理器”模板](media/data-factory-template/pipeline-from-template.png)

1.  在“从数据库批量复制到 Azure 数据资源管理器”窗格中的“用户输入”下，按如下所述指定数据集：******** 

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“ControlTableDataset”下拉列表中，选择控制表对应的链接服务，该表指示要将源中的哪些数据复制到目标，以及要将数据放在目标中的哪个位置。**** 

    b.保留“数据库类型”设置，即设置为“共享”。 在“SourceDataset”下拉列表中，选择源数据库对应的链接服务。**** 

    c. 在“AzureDataExplorerTable”下拉列表中，选择“Azure 数据资源管理器”表。**** 如果该数据集不存在，请[创建 Azure 数据资源管理器链接服务](data-factory-load-data.md#create-the-azure-data-explorer-linked-service)以添加该数据集。

    d.单击“下一步”。 选择“使用此模板”****。

    ![“从数据库批量复制到 Azure 数据资源管理器”窗格](media/data-factory-template/configure-bulk-copy-adx-template.png)

1. 在画布中选择活动外部的某个区域，以访问模板管道。 选择“参数”选项卡并输入表的参数，包括“名称”（控制表名称）和“默认值”（列名称）。************

    ![管道参数](media/data-factory-template/pipeline-parameters.png)

1.  在“Lookup”下，选择“GetPartitionList”以查看默认设置。******** 系统会自动创建查询。
1.  选择命令活动“ForEachPartition”，选择“设置”选项卡，然后执行以下操作：********

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“批计数”中，输入从 1 到 50 的数字。**** 此数字确定了在达到 *ControlTableDataset* 行数之前，可并行运行的管道数。 

    b.保留“数据库类型”设置，即设置为“共享”。 为确保管道批并行运行，请不要选中“顺序”复选框。******

    ![ForEachPartition 设置](media/data-factory-template/foreach-partition-settings.png)

    > [!TIP]
    > 最佳做法是并行运行多个管道，以加快数据复制速度。 若要提高效率，请将源表中的数据分区，并根据日期和表为每个管道分配一个分区。

1. 选择“全部验证”以验证 Azure 数据工厂管道，然后在“管道验证输出”窗格中查看结果。********

    ![验证模板管道](media/data-factory-template/validate-template-pipelines.png)

1. 如果需要，请选择“调试”，然后选择“添加触发器”以运行该管道。********

    ![“调试”和“运行管道”按钮](media/data-factory-template/trigger-run-of-pipeline.png)    

现在可以使用该模板有效地从数据库和表中复制大量数据。

## <a name="next-steps"></a>后续步骤

* 了解如何[使用 Azure 数据工厂将数据复制到 Azure 数据资源管理器](data-factory-load-data.md)。
* 了解 Azure 数据工厂中的 [Azure 数据资源管理器连接器](/azure/data-factory/connector-azure-data-explorer)。
* 了解用于查询数据的 [Azure 数据资源管理器查询](/azure/data-explorer/web-query-data)。






