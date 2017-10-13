---
title: "数据工厂（数据集成服务）简介 | Microsoft 文档"
description: "了解 Azure 数据工厂的定义：一种云数据集成服务，可对数据的移动和转换进行协调和自动化。"
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
ms.date: 08/14/2017
ms.author: shlo
robots: noindex
ms.openlocfilehash: 8d4d574dee4b993d8a464482b244e1f63ade8a57
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-azure-data-factory"></a>Azure 数据工厂简介 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [版本 1 - GA](data-factory-introduction.md)
> * [版本 2 - 预览版](../introduction.md)

> [!NOTE]
> 本文适用于数据工厂版本 1（即正式版 (GA)）。 如果使用数据工厂服务版本 2（预览版），请参阅[数据工厂 V2 简介](../introduction.md)。


## <a name="what-is-azure-data-factory"></a>什么是 Azure 数据工厂？
在大数据时代，如何充分利用现有的商业数据？ 是否可以使用本地数据源或其他不同数据源中的引用数据来扩充云中生成的数据？ 例如，游戏公司会收集云中的游戏所生成的许多日志。 该公司需要对这些日志进行分析，以便了解客户的偏好、人口统计信息、使用行为等情况，发掘追加销售和交叉销售的机会，通过开发新的具有竞争力的功能来驱动业务增长，为客户提供更好的体验。 

为了分析这些日志，该公司需要使用参考数据，例如位于本地数据存储中的客户信息、游戏信息、市场营销活动信息。 因此，该公司希望引入云数据存储中的日志数据以及本地数据存储中的参考数据， 然后使用云中的 Hadoop (Azure HDInsight) 处理数据并将结果数据发布到云数据仓库（例如 Azure SQL 数据仓库）或本地数据存储（例如 SQL Server）。 该公司希望每周运行一次此工作流。 

这就需要一个平台，让该公司通过其创建一个工作流，以便从本地数据存储和云数据存储引入数据，通过现有的计算服务（例如 Hadoop）对数据进行转换或处理，然后将结果发布到本地数据存储或云数据存储，供 BI 应用程序使用。 

![数据工厂概述](media/data-factory-introduction/what-is-azure-data-factory.png) 

Azure 数据工厂是适合此类方案的平台。 它是**基于云的数据集成服务，用于在云中创建数据驱动型工作流，以便协调和自动完成数据移动和数据转换**。 使用 Azure 数据工厂，可以创建和计划数据驱动型工作流（称为管道），以便从不同的数据存储引入数据，通过各种计算服务（例如 Azure HDInsight Hadoop、Spark、Azure Data Lake Analytics 和 Azure 机器学习）处理/转换数据，将输出数据发布到数据存储（例如 Azure SQL 数据仓库），供商业智能 (BI) 应用程序使用。  

它更多是一种先提取和加载 (EL) 后转换和加载 (TL) 型平台，而不是一种传统的提取、转换和加载 (ETL) 型平台。 所执行的转换是使用计算服务来转换/处理数据，而不是执行添加派生的列、对行计数、对数据排序这样的转换。 

目前在 Azure 数据工厂中，工作流所使用和生成的数据为**时间切片数据**（每小时、每天、每周等）。 例如，管道可能会每天一次地读取输入数据、处理数据以及生成输出数据。 也可一次性运行某个工作流。  
  

## <a name="how-does-it-work"></a>工作原理 
Azure 数据工厂中的管道（数据驱动型工作流）通常执行以下三个步骤：

![Azure 数据工厂的三个阶段](media/data-factory-introduction/three-information-production-stages.png)

### <a name="connect-and-collect"></a>连接和收集
企业有不同类型的数据，位于不同的源中。 构建信息生产系统时，第一步是连接到所有必需的数据源（例如 SaaS 服务、文件共享、FTP、Web 服务），然后根据需要将数据移至中央位置进行后续处理。

没有数据工厂，企业就必须生成自定义数据移动组件或编写自定义服务，以便集成这些数据源并进行处理。 集成和维护此类系统代价高昂且困难很多，通常缺乏企业级监视和报警，也没有完全托管型服务所能提供的控制。

而有了数据工厂，便可以在数据管道中使用复制活动，将数据从本地和云的源数据存储移到云的集中数据存储进行进一步的分析。 例如，可以先将数据收集在 Azure Data Lake Store 中，在以后再使用 Azure Data Lake Analytics 计算服务对数据进行转换。 也可以将数据收集在 Azure Blob 存储中，在以后再使用 Azure HDInsight Hadoop 群集对数据进行转换。

### <a name="transform-and-enrich"></a>转换和扩充
将数据集中到云中的数据存储以后，需使用计算服务（例如 HDInsight Hadoop、Spark、Data Lake Analytics、机器学习）对收集的数据进行处理或转换。 需要按可以维护和控制的计划以可靠方式生成转换的数据，为生产环境提供可信数据。 

### <a name="publish"></a>发布 
将转换的数据从云传输到本地源（如 SQL Server），或将它保留在云存储源中，供商业智能 (BI) 和分析工具以及其他应用程序使用。

## <a name="key-components"></a>关键组件
一个 Azure 订阅可能有多个数据工厂，或者有一个或多个 Azure 数据工厂实例。 Azure 数据工厂由四个关键组件组成，这些组件组合起来提供一个平台，供你在上面编写数据驱动型工作流（其中包含用来移动和转换数据的步骤）。 

### <a name="pipeline"></a>管道
数据工厂可以包含一个或多个管道。 管道是一组活动。 管道中的活动可以共同执行一项任务。 例如，一个管道可能包含一组活动，这些活动从 Azure Blob 引入数据，并在 HDInsight 群集上运行 Hive 查询，以便对数据分区。 这样做的好处是，可以通过管道以集而非单独的形式管理活动。 例如，可以部署和计划管道，而不需单独对活动进行操作。 

### <a name="activity"></a>活动
管道可以包含一个或多个活动。 活动定义对数据执行的操作。 例如，可以使用“复制”活动将数据从一个数据存储复制到另一个数据存储。 同样，可能使用在 Azure HDInsight 群集上运行 Hive 查询的“Hive”活动来转换或分析数据。 数据工厂支持两种类型的活动：数据移动活动和数据转换活动。

### <a name="data-movement-activities"></a>数据移动活动
数据工厂中的复制活动可以将数据从源数据存储复制到接收器数据存储。 数据工厂支持以下数据存储。 来自任何源的数据都可以写入到任何接收器。 单击某个数据存储即可了解如何将数据复制到该存储，以及如何从该存储复制数据。

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

有关详细信息，请参阅[数据移动活动](data-factory-data-movement-activities.md)一文。

### <a name="data-transformation-activities"></a>数据转换活动
[!INCLUDE [data-factory-transformation-activities](../../../includes/data-factory-transformation-activities.md)]

有关详细信息，请参阅[数据转换活动](data-factory-data-transformation-activities.md)一文。

### <a name="custom-net-activities"></a>自定义 .NET 活动
如果需要将数据移入/移出复制活动不支持的数据存储，或要使用自己的逻辑转换数据，可以创建**自定义 .NET 活动**。 有关创建和使用自定义活动的详细信息，请参阅[在 Azure数据工厂管道中使用自定义活动](data-factory-use-custom-activities.md)。

### <a name="datasets"></a>数据集
一个活动使用零个或多个数据集作为输入，一个或多个数据集作为输出。 数据集代表数据存储中的数据结构，这些结构直接指向需要在活动中使用的数据，或者将其作为输入或输出引用。 例如，Azure Blob 数据集可在 Azure Blob 存储中指定供管道读取数据的 Blob 容器和文件夹。 也可由 Azure SQL 表数据集指定一个表，允许活动向该表写入输出数据。 

### <a name="linked-services"></a>链接服务
链接的服务类似于连接字符串，它定义数据工厂连接到外部资源时所需的连接信息。 不妨这样考虑：链接服务定义到数据源的连接，而数据集则代表数据的结构。 例如，Azure 存储链接服务指定连接到 Azure 存储帐户所需的连接字符串。 Azure Blob 数据集指定 Blob 容器以及包含数据的文件夹。   

数据工厂中的链接服务有两个用途：

* 代表**数据存储**，包括但不限于本地 SQL Server、Oracle 数据库、文件共享或 Azure Blob 存储帐户。 有关支持的数据存储列表，请参阅[数据移动活动](#data-movement-activities)部分。
* 代表可托管活动执行的**计算资源**。 例如，HDInsightHive 活动在 HDInsight Hadoop 群集上运行。 有关支持的计算环境列表，请参阅[数据转换活动](#data-transformation-activities)部分。

### <a name="relationship-between-data-factory-entities"></a>数据工厂实体之间的关系
![示意图：数据工厂（云数据集成服务）- 重要概念](./media/data-factory-introduction/data-integration-service-key-concepts.png)
**图 2.** 数据集、活动、管道与链接服务之间的关系

## <a name="supported-regions"></a>支持的区域
目前可在**美国西部**、**美国东部**和**北欧**区域创建数据工厂。 但是，数据工厂可以访问其他 Azure 区域的数据存储和计算数据，在数据存储之间移动数据或使用计算服务处理数据。

Azure 数据工厂本身不存储任何数据。 它允许创建数据驱动型工作流，协调[受支持数据存储](#data-movement-activities)之间的数据移动，以及使用[计算服务](#data-transformation-activities)在其他区域或本地环境中处理数据。 它还允许使用编程方式及 UI 机制来 [监视和管理工作流](data-factory-monitor-manage-pipelines.md) 。

尽管数据工厂只能在**美国西部**、**美国东部**和**北欧**区域使用，但数据工厂中支持数据移动的服务可在[全球](data-factory-data-movement-activities.md#global)多个区域使用。 如果数据存储位于防火墙后面，可以改用本地环境中安装的[数据管理网关](data-factory-move-data-between-onprem-and-cloud.md)移动数据。

例如，假设计算环境（例如 Azure HDInsight 群集和 Azure 机器学习）即将耗尽西欧区域的资源。 可以在北欧创建并使用一个 Azure 数据工厂实例来安排西欧计算环境中的作业。 只需几毫秒时间，数据工厂就能触发计算环境上的作业，但在计算环境上运行作业所需的时间不会改变。

## <a name="get-started-with-creating-a-pipeline"></a>创建管道入门
可以使用以下工具或 API 之一，在 Azure 数据工厂中创建数据管道： 

- Azure 门户
- Visual Studio
- PowerShell
- .NET API
- REST API
- Azure Resource Manager 模板。 

若要了解如何构建包含数据管道的数据工厂，请遵循以下教程中的分步说明：

| 教程 | 说明 |
| --- | --- |
| [在两个云数据存储之间移动数据](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) |此教程介绍如何创建一个包含管道的数据工厂，该管理可将数据从 Blob 存储 **移动** 到 SQL 数据库。 |
| [使用 Hadoop 群集转换数据](data-factory-build-your-first-pipeline.md) |此教程介绍如何构建第一个包含数据管道的 Azure 数据工厂，该数据管道可以通过在 Azure HDInsight (Hadoop) 群集上运行 Hive 脚本来 **处理数据** 。 |
| [使用数据管理网关在本地数据存储与云数据存储之间移动数据](data-factory-move-data-between-onprem-and-cloud.md) |此教程介绍如何构建一个包含管道的数据工厂，该管道可将数据从**本地** SQL Server 数据库**移动**到 Azure Blob。 在演练过程中，需要在计算机上安装并配置数据管理网关。 |
