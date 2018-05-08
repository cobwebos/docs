---
title: Team Data Science Process 生命周期的数据采集和理解阶段 - Azure | Microsoft Docs
description: 你的数据科学项目数据采集和理解阶段的目标、任务和可交付结果
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: deguhath
ms.openlocfilehash: 9ed793ac6a2efbd682bb739626c4d3a5f3551a3f
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2018
---
# <a name="data-acquisition-and-understanding"></a>数据获取和理解

本文概述了与 Team Data Science Process (TDSP) 的数据采集和理解阶段相关联的目标、任务和可交付结果。 此过程提供可用于构建数据科学项目的建议生命周期。 该生命周期概述了项目通常执行的主要阶段（通常以迭代方式进行）：

   1. **了解业务**
   2. **数据采集和理解**
   3. **建模**
   4. **部署**
   5. **客户验收**

此处直观地展示了 TDSP 生命周期： 

![TDSP 生命周期](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>目标
* 生成一个干净的、高质量的数据集，了解其与目标变量的关系。 在合适的分析环境中找到数据集，以便进行建模。
* 开发数据管道的一个解决方案体系结构，以定期对数据进行刷新和评分。

## <a name="how-to-do-it"></a>如何执行
在此阶段中解决了三个主要任务：

   * **将数据引入**到目标分析环境中。
   * **浏览数据**以确定数据质量是否足以回答问题。 
   * **设置数据管道**以对新数据或定期刷新的数据进行评分。

### <a name="ingest-the-data"></a>引入数据
设置该过程，将数据从源位置移动到要运行定型和预测等分析操作的目标位置。 要了解如何使用各种 Azure 数据服务移动数据的技术详细信息和选项，请参阅[将数据加载到存储环境以进行分析](ingest-data.md)。 

### <a name="explore-the-data"></a>浏览数据
在对模型定型之前，需要对数据进行深刻理解。 实际的数据集通常比较杂乱，缺少值或存在大量其他差异。 可使用数据汇总和可视化来审核数据的质量，并提供处理数据所需的信息，然后进行建模。 此过程通常是迭代的。

TDSP 提供名为 [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils) 的自动化实用工具，以帮助实现数据的可视化并准备数据摘要报表。 建议首先从 IDEAR 开始浏览数据，这样有助于以交互方式开始了解数据，而无需进行编码。 然后即可编写用于数据浏览和可视化的自定义代码。 有关清理数据的指南，请参阅[用于准备数据以增强机器学习的任务](prepare-data.md)。  

对清理后数据的质量感到满意后，下一步是更好地理解数据中固有的模式。 这有助于为目标选择并开发合适的预测模型。 查找表明数据与目标的连接情况的证据。 然后确定在进行后续建模步骤时是否有足够的数据可用。 同样，此过程通常是迭代的。 可能需要查找包含更准确或更相关数据的新数据源，以增加在上一阶段中最初标识的数据集。 

### <a name="set-up-a-data-pipeline"></a>设置数据管道
除了初始引入和清理数据以外，作为持续学习过程的一部分，通常还需设置对新数据进行评分或定期刷新数据的过程。 可通过设置数据管道或工作流来实现上述目的。 [使用 Azure 数据工厂将数据从本地 SQL Server 实例移动到 Azure SQL 数据库](move-sql-azure-adf.md)一文提供了如何使用 [Azure 数据工厂](https://azure.microsoft.com/services/data-factory/)来设置管道的示例。 

此阶段会开发数据管道的一个解决方案体系结构。 在数据科学项目的下一阶段，同时开发管道。 根据业务需求以及集成有此解决方案的现有系统的约束，管道可以是以下任一类型： 

   * 基于批处理的管道
   * 流式处理管道或实时管道 
   * 混合管道 

## <a name="artifacts"></a>项目
以下是此阶段中的可交付结果：

   * [数据质量报表](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/DataSummaryReport.md)：此报表包含数据摘要、每个属性和目标之间的关系、变量排名等。 作为 TDSP 一部分提供的 [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils) 工具可在任何表格数据集（例如，CSV 文件或关系表）上快速生成此报表。 
   * **解决方案体系结构**：解决方案体系结构可以是生成模型后数据管道的关系图或说明，该数据管道用于对新数据进行评分或预测。 它还包含要基于新数据重新定型模型的管道。 使用 TDSP 目录结构模板时，将此文档存储到[项目](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Project)目录中。
   * **检查点决策**：开始完整的功能设计和建模前，可以重新评估项目，以确定预期值是否足以继续投入资金。 例如，用户可能已准备好继续进行项目，但需要收集更多数据或由于没有数据可以回答问题而放弃项目。

## <a name="next-steps"></a>后续步骤

以下是 TDSP 生命周期中每个步骤的链接：

   1. [了解业务](lifecycle-business-understanding.md)
   2. [数据采集和理解](lifecycle-data.md)
   3. [建模](lifecycle-modeling.md)
   4. [部署](lifecycle-deployment.md)
   5. [客户验收](lifecycle-acceptance.md)

我们还提供了完整的演练，演示特定方案过程中的所有步骤。 [示例演练](walkthroughs.md)一文提供了包含链接和缩略图描述的方案列表。 该演练演示如何将云、本地工具以及服务结合到一个工作流或管道中，以创建智能应用程序。 

有关如何在使用 Azure 机器学习工作室的 TDSP 中执行步骤的示例，请参阅[通过 Azure 机器学习使用 TDSP](http://aka.ms/datascienceprocess)。
