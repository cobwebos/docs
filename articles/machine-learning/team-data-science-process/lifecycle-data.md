---
title: "Team Data Science Process 生命周期的数据采集和理解阶段 - Azure | Microsoft Docs"
description: "你的数据科学项目的数据采集和理解阶段的目标、任务和可交付结果。"
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/02/2017
ms.author: bradsev;
ms.openlocfilehash: eea3c357ebf6ad920c0ddebdb979aa07aece4794
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="data-acquisition-and-understanding"></a>数据获取和理解

本主题概述了与 Team Data Science Process 的**数据采集和理解阶段**相关联的目标、任务和可交付结果。 此过程提供了可用于构建数据科学项目的建议生命周期。 该生命周期概述了项目通常执行的主要阶段（通常以迭代方式进行）：

* **了解业务**
* **数据采集和理解**
* **建模**
* **部署**
* **客户验收**

以下是 **Team Data Science Process 生命周期**的可视化表示形式 。 

![TDSP-Lifecycle2](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>目标
* 干净优质的数据集，其与目标变量的关系被认为位于适当的分析环境中，可供建模。
* 开发了数据管道的一个解决方案体系结构，以对数据定期进行刷新和评分。

## <a name="how-to-do-it"></a>如何执行
在此阶段中解决了三个主要任务：

* **将数据引入**到目标分析环境中。
* **浏览数据**以确定数据质量是否足以回答问题。 
* **设置数据管道**以对新数据或定期刷新的数据进行评分。

### <a name="21-ingest-the-data"></a>2.1 引入数据
该过程设置为将数据从源位置移动到要执行如定型和预测等分析操作的目标位置。 要了解如何使用各种 Azure 数据服务执行此操作的技术详细信息和选项，请参阅[将数据加载到存储环境以进行分析](ingest-data.md)。 

### <a name="22-explore-the-data"></a>2.2 浏览数据
在对模型定型之前，需要对数据进行深刻理解。 实际的数据集通常比较杂乱、缺少值或存在大量其他差异。 数据摘要和可视化可用于审核数据的质量，并且提供准备进行建模前对数据进行处理所需的信息。 此过程通常是迭代的。

TDSP 提供名为 [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils) 的自动化实用工具，以帮助实现数据的可视化并准备数据摘要报表。 我们建议首先从 IDEAR 开始浏览数据，这样有助于以交互方式开始了解数据，而无需进行编码，然后编写用于数据浏览和可视化的自定义代码。 有关清理数据的指南，请参阅[用于准备数据以增强机器学习的任务](prepare-data.md)。  

对清理后的数据质量满意后，下一步是更好地理解数据中的固有模式，这些模式有助于为目标选择并开发适当的预测模型。 查找证据以证明数据与目标的联系程度，以及是否有足够的数据来进行后续的建模步骤。 同样，此过程通常是迭代的。 可能需要查找包含更准确或更相关数据的新数据源，以增加在上一阶段中最初标识的数据集。  

### <a name="23-set-up-a-data-pipeline"></a>2.3 设置数据管道
除了初始引入和清理数据以外，作为持续学习过程的一部分，通常还需设置对新数据进行评分或定期刷新数据的过程。 可以通过设置数据管道或工作流来实现上述目的。 以下是如何使用 [Azure 数据工厂](https://azure.microsoft.com/services/data-factory/)设置管道的[示例](move-sql-azure-adf.md)。 

在此阶段中开发了数据管道的一个解决方案体系结构。 在进行以下阶段的数据科学项目时，也并行开发了管道。 数据管道可以是基于批处理，也可以是流式处理/实时管道或混合管道，具体视业务需求以及此解决方案集成到其中的现有系统的约束而定。 

## <a name="artifacts"></a>项目
以下是此阶段中的可交付结果。

* [**数据质量报表**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/DataSummaryReport.md)：此报表包含数据摘要、每个属性和目标之间的关系、变量排名等。作为 TDSP 一部分提供的 [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils) 工具可在任何表格数据集（例如，CSV 文件或关系表）上生成此报表。 
* **解决方案体系结构**：此解决方案体系结构可以是生成模型后数据管道的关系图或说明，该数据管道用于对新数据进行评分或预测。 它还包含要基于新数据重新定型模型的管道。 使用 TDSP 目录结构模板时，此文档存储在[项目](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Project)目录中。
* **检查点决策**：在开始完整的功能设计和建模前，可以重新评估项目，以确定预期值是否足以继续投入资金。 例如，用户可能已准备好继续进行项目，但需要收集更多数据或由于没有数据可以回答问题而放弃项目。

## <a name="next-steps"></a>后续步骤

以下是 Team Data Science Process 生命周期中每个步骤的链接：

* [1.了解业务](lifecycle-business-understanding.md)
* [2.数据采集和理解](lifecycle-data.md)
* [3.建模](lifecycle-modeling.md)
* [4.部署](lifecycle-deployment.md)
* [5.客户验收](lifecycle-acceptance.md)

还提供了完整的、端到端的演练，演示**特定方案**处理过程中的所有步骤。 在[示例演练](walkthroughs.md)主题中，列出了相关步骤并链接了缩略图说明。 这些演练演示如何将云、本地工具和服务合并到工作流或管道中，以创建智能应用程序。  

有关在团队数据科学过程中执行使用 Azure 机器学习工作室的步骤的示例，请参阅[使用 Azure ML](http://aka.ms/datascienceprocess) 学习路径。