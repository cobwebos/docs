---
title: "数据工厂（数据集成服务）简介 | Microsoft 文档"
description: "了解 Azure 数据工厂的定义：一种云数据集成服务，可对数据的移动和转换进行协调和自动化。"
keywords: "数据集成, 云数据集成, 什么是 azure 数据工厂"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: cec68cb5-ca0d-473b-8ae8-35de949a009e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/22/2016
ms.author: shlo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 0d53dfea0ebaae4f8080718d84e2d26ecae37681


---
# <a name="introduction-to-azure-data-factory-service-a-data-integration-service-in-the-cloud"></a>Azure 数据工厂服务（云中的数据集成服务）简介
## <a name="what-is-azure-data-factory"></a>什么是 Azure 数据工厂？
数据工厂是一项基于云的数据集成服务，可对数据**移动**和**转换**进行安排并使其自动化。 可以使用数据工厂服务创建数据集成解决方案，从各种数据存储引入数据、转换/处理数据，并将结果数据发布到数据存储。 

在数据工厂服务中可以创建数据管道来移动和转换数据，然后按指定的计划（每小时、每日、每周等）运行管道。 该服务还提供丰富的可视化效果来显示数据管道之间的历史记录和依赖项，用户可以通过单个统一视图监视所有数据管道，轻松查明问题和设置监视警报。

![示意图：数据工厂（数据集成服务）概览](./media/data-factory-introduction/what-is-azure-data-factory.png)
**图 1.** 从各种数据源引入数据，准备、转换和分析数据，然后发布随时可用的数据。

## <a name="pipelines-and-activities"></a>管道和活动
在数据工厂解决方案中，可以创建一个或多个数据**管道**。 管道是活动的逻辑分组。 它们用于将活动分组成一个单位，共同执行某项任务。 

**活动**定义对数据执行的操作。 例如，可以使用“复制”活动将数据从一个数据存储复制到另一个数据存储。 同样，可能使用在 Azure HDInsight 群集上运行 Hive 查询的“Hive”活动来转换或分析数据。 数据工厂支持两种类型的活动：数据移动活动和数据转换活动。 

## <a name="data-movement-activities"></a>数据移动活动
[!INCLUDE [data-factory-supported-data-stores](../../includes/data-factory-supported-data-stores.md)]

有关详细信息，请参阅[数据移动活动](data-factory-data-movement-activities.md)。 

## <a name="data-transformation-activities"></a>数据转换活动
[!INCLUDE [data-factory-transformation-activities](../../includes/data-factory-transformation-activities.md)]

有关详细信息，请参阅[数据转换活动](data-factory-data-transformation-activities.md)。

如果需要将数据移入/移出复制活动不支持的数据存储，或要使用自己的逻辑转换数据，可以创建**自定义 .NET 活动**。 有关创建和使用自定义活动的详细信息，请参阅[在 Azure数据工厂管道中使用自定义活动](data-factory-use-custom-activities.md)。

## <a name="linked-services"></a>链接服务
链接服务定义数据工厂连接到外部资源时所需的信息（示例：Azure 存储、本地 SQL Server、Azure HDInsight）。 数据工厂中的链接服务有两个用途：

* 代表**数据存储**，包括但不限于本地 SQL Server、Oracle 数据库、文件共享或 Azure Blob 存储帐户。 有关支持的数据存储列表，请参阅[数据移动活动](data-factory-data-movement-activities.md)部分。 
* 代表可托管活动执行的**计算资源**。 例如，HDInsightHive 活动在 HDInsight Hadoop 群集上运行。 有关支持的计算环境列表，请参阅[数据转换活动](data-factory-data-transformation-activities.md)部分。 

## <a name="datasets"></a>数据集
链接服务将数据存储链接到 Azure 数据工厂。 数据集代表数据存储中的数据结构。 例如，Azure 存储链接服务提供连接信息让数据工厂连接到 Azure 存储帐户。 Azure Blob 数据集在 Azure Blob 存储中指定管道要从中读取数据的 Blob 容器和文件夹。 同理，Azure SQL 链接服务提供 Azure SQL 数据库的连接信息，Azure SQL 数据集指定包含数据的表。   

## <a name="relationship-between-data-factory-entities"></a>数据工厂实体之间的关系
数据工厂使用几个协作的主要实体来定义输入和输出数据、处理事件，以及执行所需数据流而要使用的计划和资源。

![示意图：数据工厂（云数据集成服务）- 重要概念](./media/data-factory-introduction/data-integration-service-key-concepts.png)
**图 2.**  数据集、活动、管道与链接服务之间的关系

了解链接服务、数据集、活动和管道这四个简单的概念之后，便可以开始实践了！ [构建第一个管道](data-factory-build-your-first-pipeline.md)。 

## <a name="supported-regions"></a>支持的区域
目前可在**美国西部**、**美国东部**和**北欧**区域创建数据工厂。 但是，数据工厂可以访问其他 Azure 区域的数据存储和计算数据，在数据存储之间移动数据或使用计算服务处理数据。 

Azure 数据工厂本身不存储任何数据。 它允许创建数据驱动的流程，协调[受支持数据存储](data-factory-data-movement-activities.md#supported-data-stores)之间的数据移动，以及使用[计算服务](data-factory-compute-linked-services.md)在其他区域或本地环境中处理数据。 它还允许使用编程方式及 UI 机制来 [监视和管理工作流](data-factory-monitor-manage-pipelines.md) 。 

尽管 Azure 数据工厂只能在**美国西部**、**美国东部**和**北欧**区域使用，但数据工厂中支持数据移动的服务可在[全球](data-factory-data-movement-activities.md#global)多个区域使用。 如果数据存储位于防火墙后面，可以使用本地环境中安装的 [数据管理网关](data-factory-move-data-between-onprem-and-cloud.md) 移动数据。 

例如，假设计算环境（例如 Azure HDInsight 群集和 Azure 机器学习）即将耗尽西欧区域的资源。 可以在北欧创建并使用一个 Azure 数据工厂实例来安排西欧计算环境中的作业。 只需几毫秒时间，数据工厂就能触发计算环境上的作业，但在计算环境上运行作业所需的时间不会改变。

将来，Azure 支持的每个地理区域都有望部署 Azure 数据工厂。

## <a name="next-steps"></a>后续步骤
若要了解如何构建包含数据管道的数据工厂，请遵循以下教程中的分步说明。 

| 教程 | 说明 |
| --- | --- |
| [Build a data pipeline that processes data using Hadoop cluster](data-factory-build-your-first-pipeline.md) |此教程介绍如何构建第一个包含数据管道的 Azure 数据工厂，该数据管道可以通过在 Azure HDInsight (Hadoop) 群集上运行 Hive 脚本来 **处理数据** 。 |
| [Build a data pipeline to move data between two cloud data stores](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) |此教程介绍如何创建一个包含管道的数据工厂，该管理可将数据从 Blob 存储 **移动** 到 SQL 数据库。 |
| [Build a data pipeline to move data between an on-premises data store and a cloud data store using Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md) |此教程介绍如何构建一个包含管道的数据工厂，该管道可将数据从**本地** SQL Server 数据库**移动**到 Azure Blob。 在演练过程中，需要在计算机上安装并配置数据管理网关。 |




<!--HONumber=Nov16_HO2-->


