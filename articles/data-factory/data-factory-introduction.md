---
title: 数据工厂（数据集成服务）简介 | Microsoft Docs
description: 了解 Azure 数据工厂的定义：一种云数据集成服务，可对数据的移动和转换进行协调和自动化。
keywords: 数据集成, 云数据集成, 什么是 azure 数据工厂
services: data-factory
documentationcenter: ''
author: spelluru
manager: jhubbard
editor: monicar

ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/08/2016
ms.author: spelluru

---
# Azure 数据工厂服务（云中的数据集成服务）简介
## 什么是 Azure 数据工厂？
数据工厂是一项基于云的数据集成服务，可对数据移动和转换进行安排并使其实现自动化。如同制造厂运转设备将原材料转换为成品一样，数据工厂可协调现有的服务，收集原始数据并将其转换为随时可用的信息。

数据工厂跨本地、云数据源和 SaaS 运行，可以引入、准备、转换、分析和发布数据。可以在 [Azure HDInsight (Hadoop)](http://azure.microsoft.com/documentation/services/hdinsight/) 和 [Azure Batch](https://azure.microsoft.com/documentation/services/batch/) 等服务中，使用数据工厂将服务纳入托管的数据流管道来满足大数据计算需求，此外还可以使用 [Azure 机器学习](https://azure.microsoft.com/documentation/services/machine-learning/)操作分析解决方案。数据工厂不仅提供表格式监视视图，还提供丰富的可视化效果，快速显示数据管道之间的沿袭和依赖关系。通过一个中心视图即可监视所有数据流管道，轻松找出问题和设置监视警报。

![示意图：数据工厂（数据集成服务）概述](./media/data-factory-introduction/what-is-azure-data-factory.png)

**图 1.** 从许多不同的本地数据源收集数据、引入、准备、转换和分析数据，然后发布随时可用的数据。

随时可以根据需要使用数据工厂收集各种形状和大小数据、对其进行转换，然后将其发布以获取深入见解，一切都按可靠的计划进行。使用数据工厂可以针对不同行业的各种方案创建高度可用的数据流管道，满足其分析管道的需求。网上零售商可以使用数据工厂，根据客户的浏览行为生成个性化的[产品建议](data-factory-product-reco-usecase.md)。游戏工作室可以使用数据工厂了解其[营销活动的有效性](data-factory-customer-profiling-usecase.md)。查看 [Customer Case Studies](data-factory-customer-case-studies.md)（客户案例研究），直接从我们客户那里了解他们使用数据工厂的方式和原因。

> [!VIDEO https://channel9.msdn.com/Blogs/Windows-Azure/Azure-Data-Factory-Overview/player]
> 
> 

## 关键概念
数据工厂使用几个协作的主要实体来定义输入和输出数据、处理事件，以及执行所需数据流而要使用的计划和资源。

![示意图：数据工厂（云数据集成服务）- 重要概念](./media/data-factory-introduction/data-integration-service-key-concepts.png)

**图 2.** 数据集、活动、管道与链接服务之间的关系

### 管道
[管道](data-factory-create-pipelines.md)是活动的逻辑分组。它们用于将活动分组成一个单位，共同执行某项任务。例如，清理日志文件数据可能需要按顺序执行多个转换活动。这种顺序可能涉及到复杂的计划和依赖关系，需要对整个过程进行协调和自动化。所有这些活动可以分组成名为“CleanLogFiles”的单个管道。然后，即可以单个单位的形式部署、计划或删除“CleanLogFiles”，而无需单独管理每个活动。

### 活动
活动定义对数据执行的操作。每个活动使用零个或多个[数据集](data-factory-create-datasets.md)作为输入，生成一个或多个数据集作为输出。活动是 Azure 数据工厂中的协调单位。例如，可以使用[复制活动](data-factory-data-movement-activities.md)来协调将数据从一个数据集复制到另一个数据集的过程。同样，可能使用在 Azure HDInsight 群集上运行 Hive 查询的 [Hive 活动](data-factory-data-transformation-activities.md)来转换或分析数据。Azure 数据工厂提供多种数据转换、分析和数据移动活动。

### 数据集
[数据集](data-factory-create-datasets.md)是命名的引用/指针，指向要用作活动输入或输出的数据。数据集可识别不同数据存储（包括表、文件、文件夹和文档）中的数据结构。

### 链接服务
链接服务定义数据工厂连接到外部资源时所需的信息。数据工厂中的链接服务有两个用途：

* 代表数据存储，包括但不限于本地 SQL Server、Oracle DB、文件共享或 Azure Blob 存储帐户。如前所述，数据集代表通过链接服务连接到数据工厂的数据存储中的数据结构。
* 代表可托管活动执行的计算资源。例如，“HDInsightHive Activity”在 HDInsight Hadoop 群集上执行。

了解数据集、活动、管道和链接服务这四个简单的概念之后，便可以开始使用数据工厂了！ 可以[构建第一个管道](data-factory-build-your-first-pipeline.md)，或者根据 [Data Factory Samples](data-factory-samples.md)（数据工厂示例）一文中的说明部署现成的示例。

## 支持的区域
目前可在**美国西部**、**美国东部**和**北欧**区域创建数据工厂。但是，数据工厂可以访问其他 Azure 区域的数据存储和计算数据，在数据存储之间移动数据或使用计算服务处理数据。

Azure 数据工厂本身不存储任何数据。它允许创建数据驱动的流程，协调[受支持数据存储](data-factory-data-movement-activities.md#supported-data-stores)之间的数据移动，以及使用[计算服务](data-factory-compute-linked-services.md)在其他区域或本地环境中处理数据。它还允许使用编程方式及 UI 机制来[监视和管理工作流](data-factory-monitor-manage-pipelines.md)。

尽管 Azure 数据工厂只能在**美国西部**、**美国东部**和**北欧**区域使用，但数据工厂中支持数据移动的服务可在[全球](data-factory-data-movement-activities.md#global)多个区域使用。如果数据存储位于防火墙后面，可以使用本地环境中安装的[数据管理网关](data-factory-move-data-between-onprem-and-cloud.md)移动数据。

例如，假设计算环境（例如 Azure HDInsight 群集和 Azure 机器学习）即将耗尽西欧区域的资源。可以在北欧创建并使用一个 Azure 数据工厂实例来安排西欧计算环境中的作业。只需几毫秒时间，数据工厂就能触发计算环境上的作业，但在计算环境上运行作业所需的时间不会改变。

将来，Azure 支持的每个地理区域都有望部署 Azure 数据工厂。

## 后续步骤
若要了解如何构建包含数据管道的数据工厂，请遵循以下教程中的分步说明。

| 教程 | 说明 |
| --- | --- |
| [Build a data pipeline that processes data using Hadoop cluster](data-factory-build-your-first-pipeline.md)（构建使用 Hadoop 群集处理数据的数据管道） |此教程介绍如何构建第一个包含数据管道的 Azure 数据工厂，该数据管道可以通过在 Azure HDInsight (Hadoop) 群集上运行 Hive 脚本来**处理数据**。 |
| [Build a data pipeline to move data between two cloud data stores](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)（构建可在两个云数据存储之间移动数据的数据管道） |此教程介绍如何创建一个包含管道的数据工厂，该管理可将数据从 Blob 存储**移动**到 SQL 数据库。 |
| [Build a data pipeline to move data between an on-premises data store and a cloud data store using Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md)（构建可以使用数据管理网关在本地数据存储与云数据存储之间移动数据的数据管道） |此教程介绍如何构建一个包含管道的数据工厂，该管道可将数据从**本地** SQL Server 数据库**移动**到 Azure Blob。在演练过程中，需要在计算机上安装并配置数据管理网关。 |

<!---HONumber=AcomDC_0921_2016-->