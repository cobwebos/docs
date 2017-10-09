---
title: "Team Data Science Process 生命周期的建模阶段 - Azure | Microsoft Docs"
description: "你的数据科学项目的建模阶段的目标、任务和可交付结果。"
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
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 0c855faa96d7feb9f762ecaed7654669a5a8a5b7
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---
# <a name="modeling"></a>建模

本主题概述了与 Team Data Science Process 的**建模阶段**相关联的目标、任务和可交付结果。 此过程提供了可用于构建数据科学项目的建议生命周期。 该生命周期概述了项目通常执行的主要阶段（通常以迭代方式进行）：

* **了解业务**
* **数据采集和理解**
* **建模**
* **部署**
* **客户验收**

以下是 **Team Data Science Process 生命周期**的可视化表示形式 。 

![TDSP-Lifecycle2](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>目标
* 机器学习模型的最佳数据功能。
* 最准确地预测目标的信息性 ML 模型。
* 适用于生产的 ML 模型。

## <a name="how-to-do-it"></a>如何执行
在此阶段中解决了三个主要任务：

* **功能设计**：从原始数据创建数据功能，以实现模型定型。
* **模型定型**：通过比较模型成功的指标，找出最能准确回答问题的模型。
* 确定模型是否**适用于生产**。

### <a name="31-feature-engineering"></a>3.1 功能设计
功能设计包括对原始变量的涵盖、聚合和转换，以创建分析中使用的功能。 如果要深入了解模型的驱动因素，则需要了解这些功能彼此间的关系，以及使用这些功能的机器学习算法方式。 此步骤需要创造性地组合领域专业知识，并从数据浏览步骤中获取见解。 这是在避免过多不相关的变量同时查找并涵盖信息性变量的一项平衡措施。 信息性变量会改善结果；而不相关的变量对模型引入了不必要的杂乱。 还需要为在评分过程中获取的任何新数据生成一些功能。 因此，可仅根据在评分时可用的数据生成这些功能。 有关使用各种 Azure 数据技术时的功能设计的相关技术指导，请参阅[数据科学过程中的功能设计](create-features.md)。 

### <a name="32-model-training"></a>3.2 模型定型
根据所要回答的问题类型，有许多可用的建模算法。 有关选择算法的指南，请参阅[如何选择 Microsoft Azure 机器学习的算法](../studio/algorithm-choice.md)。 尽管本文是针对 Azure 机器学习编写，但所述指南也适用于其他任何机器学习项目。 

模型定型的过程包括以下步骤： 

* 随机拆分输入数据，以建模成定型数据集和测试数据集。
* 使用定型数据集生成模型。
* 评估（定型数据集和测试数据集）一系列竞争机器学习算法，以及关联的各种优化参数（称为“参数整理”），适用于回答与当前数据相关的问题。
* 比较备用方法的成功指标，确定可回答问题的“最佳”解决方案。

> [!NOTE]
> **避免泄漏**：添加定型数据集外部数据可能会导致数据泄漏，因为此类数据允许模型或机器学习算法做出不切实际的良好预测。 泄露是数据科学家获得好到不真实的预测结果时会紧张的常见原因。 很难检测到这些依赖项。 为避免泄漏，通常需要在生成分析数据集、创建模型和计算准确性之间进行循环。 
> 
> 

我们提供包含 TDSP 的[自动化建模和报告工具](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/Modeling)，该工具能够运行多个算法和参数扫描以生成基准模型。 它还会生成基准建模报表，该报表汇总每个模型的性能和参数组合，包括变量重要性。 此过程可以进一步促进功能设计，因为它也是可以迭代的。 

## <a name="artifacts"></a>项目
在此阶段中生成的项目包括：

* [**功能集**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/Data%20Defintion.md#feature-sets)：“数据定义”报表的“功能集”部分中介绍了为建模开发的功能。 它包括指向代码以生成功能的指针，以及如何生成功能的说明。
* [**模型报表**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Model/Model%201/Model%20Report.md)：对于尝试过的每个模型，都会根据模板生成一个标准报表，用于详细介绍每次试验。
* **检查点决策**：评估模型的执行是否足够良好，以便将其部署到生产系统。 要提出的一些关键问题有：
  * 在给定测试数据的情况下，模型是否能充分地回答问题？ 
  * 应尝试的备用方法：收集其他数据、进行更多的功能设计或使用其他算法进行试验？

## <a name="next-steps"></a>后续步骤

以下是 Team Data Science Process 生命周期中每个步骤的链接：

* [1.了解业务](lifecycle-business-understanding.md)
* [2.数据采集和理解](lifecycle-data.md)
* [3.建模](lifecycle-modeling.md)
* [4.部署](lifecycle-deployment.md)
* [5.客户验收](lifecycle-acceptance.md)

还提供了完整的、端到端的演练，演示**特定方案**处理过程中的所有步骤。 在[示例演练](walkthroughs.md)主题中，列出了相关步骤并链接了缩略图说明。 具体展示了如何将云、本地工具以及服务结合到一个工作流或管道中，以创建智能应用程序。 

有关在 Team Data Science Process 中执行使用 Microsoft Azure 机器学习工作室的步骤的示例，请参阅[结合使用 Azure ML](http://aka.ms/datascienceprocess) 学习路径。 
