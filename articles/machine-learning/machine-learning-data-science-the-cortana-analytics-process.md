---
title: "什么是团队数据科学过程？  | Microsoft 文档"
description: "团队数据科学过程是用于构建利用高级分析功能的智能应用程序的系统化方法。"
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: a098aa2e-fd79-4543-8e15-9aae9d8b3ee6
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/16/2016
ms.author: bradsev
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 49bb8201a93e622774e197801b566caa03ed28a6


---
# <a name="what-is-the-team-data-science-process-tdsp"></a>什么是 Team Data Science Process (TDSP)？
[团队数据科学过程 (Team Data Science Process, TDSP)](data-science-process-overview.md) 提供一种系统化的方法来生成智能应用程序，可让数据科学家团队在将这些应用程序变为产品所需活动的整个生命周期内更高效地协作。 TDSP 概述了一系列步骤，这些步骤提供关于以下操作的**指导**：定义问题，设置所需的工具和环境，分析相关数据，构建和评估预测模型，以及在企业应用程序中部署那些模型。 

以下是**团队数据科学过程**中的步骤：  

![CAP 工作流](./media/machine-learning-data-science-the-cortana-analytics-process/CAP-workflow.png)

此过程是**迭代过程**：理解模型中涉及的新数据和现有数据或引用，需要重新执行之前在序列中已完成的步骤。 **轻松地调整**现有组织开发和项目规划流程，以便处理 TDSP 定义的一系列步骤。 

对于过程中的步骤，会在 [TDSP 学习途径](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/)中进行图解、添加链接，并在下文进行描述。  

## <a name="preparation-steps"></a>准备步骤
## <a name="p1-plan-the-analytics-project"></a>P1。 规划分析项目
首先通过定义业务目标和问题来分析项目。 在“业务需求”中指定了业务目标和问题。 此步骤中的中心目标识别键业务变量（例如，销售预测或可能具有欺骗性的订单），分析需要该变量来进行预测以满足要求。 若要了解从分析角度解决项目对象所需的**数据源**，其他规划通常很有必要。 查找现有系统需要收集和记录的、解决此问题的其他数据，进而解决此问题、实现项目目标，这种情况也很常见。 相关指南，请参阅[规划 Data Science Process 的环境](machine-learning-data-science-plan-your-environment.md)和 [Azure 机器学习中的高级分析方案](machine-learning-data-science-plan-sample-scenarios.md)。  

## <a name="p2-setup-analytics-environment"></a>P2。 设置分析环境
团队数据科学过程分析环境涉及到几个组件： 

* **数据工作区**，其中数据分阶段实现分析和建模， 
* 预处理、探索以及数据建模的**处理基础结构**
* **运行时基础结构**分析模型可操作，运行使用这些模型的应用程序的智能客户端。  

需要设置的分析基础结构通常是独立于核心操作系统环境的一部分。 但它通常利用企业内多个系统的数据以及公司外部资源中的数据。 分析基础结构可以完全基于云或本地安装，或者基于两者。 有关选项，请参阅[设置在团队数据科学过程中使用的数据科学环境](machine-learning-data-science-environment-setup.md)。

## <a name="analytics-steps"></a>分析步骤：
## <a name="1-ingest-data-into-the-analytical-environment"></a>1.将数据引入到分析环境中
第一步是从各种源引入相关数据，从企业内部或外部引入可处理数据的分析环境。 源上的数据**格式**可能不同于目标所需的格式。 因此可能需要通过引入工具，来处理一些数据转换。 有关选项，请参阅[将数据加载到分析存储环境](machine-learning-data-science-ingest-data.md)

除了最初引入数据，作为持续学习过程的一部分，很多智能应用程序需定期刷新数据。 可通过设置“数据管道”或工作流来实现上述目的。 它构进程的迭代部分，其中包括重建和重新评估智能应用程序部署解决方案所使用的分析模型。 例如，请参阅[使用 Azure 数据工厂将数据从本地 SQL server 移到 SQL Azure](machine-learning-data-science-move-sql-azure-adf.md)。

## <a name="2-explore-and-pre-process-data"></a>2.浏览和预处理数据
下一步是通过调查数据的**汇总统计信息**、关系以及使用**可视化**等技术来获取更深入的了解。 在其中会处理**数据质量**和完整性问题（如缺少值、数据类型不匹配和数据关系不一致）。 在进一步分析和建模前，使用预处理转换清理原始数据。 有关说明，请参阅[准备任务数据以增强机器学习](machine-learning-data-science-prepare-data.md)。

## <a name="3-develop-features"></a>3.开发功能
数据科学家与领域专家协作，必须标识捕获数据集突出属性的功能，这些功能最适合用于预测规划过程中标识的关键业务变量。 可从现有数据获取这些新功能，或可能需要收集其他数据。 此过程被称为**功能工程**，是构建有效的预测分析功能系统的关键步骤之一。 此步骤需要创造性地组合域专业知识，并从数据浏览的步骤中获取见解。 相关指南，请参阅[团队数据科学过程中的特征构造工程](machine-learning-data-science-create-features.md)。

## <a name="4-create-predictive-models"></a>4.创建预测模型
使用已清理的数据和定义数据，数据科学家生成分析模型，以便预测业务要求（规划步骤中所定义的）所标识的关键变量。 机器学习系统支持多个适用于各种情况的**建模算法**。 相关指南，请参阅[如何选择团队 Azure 机器学习的算法](machine-learning-algorithm-choice.md)。

数据科学家必须选择最适合其预测任务的模型，可能需要组合来自多个模型的结果以获取最佳结果。 建模的输入数据通常随机分为三个部分：

* 训练数据集、 
* 验证数据集和 
* 测试数据集 

使用“训练数据集”生成模型。 通过运行模型并测量**验证数据集**预测错误，选择带参数调整模型的最佳组合。 最后，使用**测试数据集**评估独立数据上选定模型的性能，这些独立数据未用于培训或验证模型。  有关过程，请参阅[如何评估 Azure 机器学习中的模型性能](machine-learning-evaluate-model-performance.md)。

## <a name="5-deploy-and-consume-models"></a>5.部署和使用模型
如果拥有一组运行良好的模型，其他应用程序也可以进行**操作**。 根据业务需求作出预测，无论是**实时**业务或是基于**批处理**的业务。 若要实现可操作性，这些模型必须使用**API 接口**进行公开，可从在线网站、电子表格、仪表板或业务及后端应用程序的行等各种应用程序轻松使用该接口。 请参阅[部署 Azure 机器学习 Web 服务](machine-learning-publish-a-machine-learning-web-service.md)。

## <a name="summary-and-next-steps"></a>总结和后续步骤
将[](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/)建模为一系列的迭代步骤，这些步骤为所需任务**提供指导**，以便使用高级分析功能来构建智能应用程序。 每个步骤还提供有关如何使用各种 Microsoft 技术来完成所述任务的详细信息。 

虽然 TDSP 不指定“文档”项目的特定类型，但对于记录数据浏览结果、建模和评估结果以及节省相关代码，以便可以在需要时进行迭代分析等方面，它无疑是最佳选择。 在涉及类似数据和预测任务的其他应用程序上工作时，也允许重复使用分析工作。

还提供了完整的、端到端的演练，演示**特定方案**处理过程中的所有步骤。 有关示例，请参阅：

* [运行中的团队数据科学过程：使用 SQL Server](machine-learning-data-science-process-sql-walkthrough.md)
* [运行中的团队数据科学过程：使用 HDInsight Hadoop 群集](machine-learning-data-science-process-hive-walkthrough.md)。
* [在 Azure HD.mdnsight 上使用 Spark 的数据科学](machine-learning-data-science-spark-overview.md)
* [在 Azure Data Lake 中可扩展的数据科学︰端到端演练](machine-learning-data-science-process-data-lake-walkthrough.md)




<!--HONumber=Nov16_HO3-->


