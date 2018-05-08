---
title: Team Data Science Process 生命周期的建模阶段 - Azure | Microsoft Docs
description: 你的数据科学项目建模阶段的目标、任务和可交付结果
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
ms.openlocfilehash: 22f54e94293bae9f308b75f93e2dbec75b1f97c8
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2018
---
# <a name="modeling"></a>建模

本文概述了与 Team Data Science Process (TDSP) 的建模阶段相关联的目标、任务和可交付结果。 此过程提供可用于构建数据科学项目的建议生命周期。 该生命周期概述了项目通常执行的主要阶段（通常以迭代方式进行）：

   1. **了解业务**
   2. **数据采集和理解**
   3. **建模**
   4. **部署**
   5. **客户验收**

此处直观地展示了 TDSP 生命周期：

![TDSP 生命周期](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>目标
* 确定机器学习模型的最佳数据功能。
* 创建可精准预测目标的信息性机器学习模型。
* 创建适用于生产的机器学习模型。

## <a name="how-to-do-it"></a>如何执行
在此阶段中解决了三个主要任务：

  * **功能设计**：从原始数据创建数据功能，以实现模型定型。
  * **模型定型**：通过比较模型的成功指标，找出最能准确回答问题的模型。
  * 确定模型是否适用于生产。

### <a name="feature-engineering"></a>特性工程
功能设计包括对原始变量的涵盖、聚合和转换，以创建分析中使用的功能。 若要深入了解模型的驱动因素，则需要了解这些功能彼此间的关系，以及使用这些功能的机器学习算法方式。 

此步骤需要创造性地组合域专业知识，并从数据浏览的步骤中获取见解。 功能工程可平衡信息性变量的查找与添加，同时避免产生过多不相关的变量。 信息性变量会改善结果；而不相关的变量会将不必要的干扰引入模型。 还需要为在评分过程中获取的任何新数据生成一些功能。 因此，可仅根据在评分时可用的数据生成这些功能。 

有关使用各种 Azure 数据技术时的功能设计的相关技术指导，请参阅[数据科学过程中的功能设计](create-features.md)。 

### <a name="model-training"></a>模型定型
根据所要回答的问题类型，会有许多可用的建模算法。 有关选择算法的指南，请参阅[如何选择 Microsoft Azure 机器学习的算法](../studio/algorithm-choice.md)。 尽管本文使用的是 Azure 机器学习，但所述指南也适用于其他任何机器学习项目。 

模型定型的过程包括以下步骤： 

   * 随机拆分输入数据，以建模成定型数据集和测试数据集。
   * 使用定型数据集生成模型。
   * 评估定型数据集和测试数据集。 使用一系列竞争机器学习算法，以及关联的各种优化参数（称为“参数整理”），这些参数适用于回答与当前数据相关的问题。
   * 比较备用方法的成功指标，确定可回答问题的“最佳”解决方案。

> [!NOTE]
> **避免泄漏**：若添加定型数据集外部数据，则会导致数据泄漏，因为此类数据允许模型或机器学习算法做出不切实际的良好预测。 泄露是数据科学家获得好到不真实的预测结果时会紧张的常见原因。 很难检测到这些依赖项。 为避免泄漏，通常需要在生成分析数据集、创建模型和评估结果准确性之间进行循环。 
> 
> 

我们提供包含 TDSP 的[自动化建模和报告工具](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/Modeling)，该工具能够运行多个算法和参数扫描以生成基准模型。 它还会生成基准建模报表，该报表汇总每个模型的性能和参数组合，包括变量重要性。 此过程可以进一步促进功能设计，因为它也是可以迭代的。 

## <a name="artifacts"></a>项目
在此阶段中生成的项目包括：

   * [功能集](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/Data%20Defintion.md#feature-sets)：“数据定义”报表的“功能集”部分介绍了为建模开发的功能。 它包括指向代码以生成功能的指针，以及说明如何生成功能的描述。
   * [模型报表](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Model/Model%201/Model%20Report.md)：对于尝试过的每个模型，都会根据模板生成一个标准报表，用于详细介绍每次试验。
   * **检查点决策**：评估模型的执行是否足够良好，以便将其部署到生产系统。 要提出的一些关键问题有：
     * 在给定测试数据的情况下，模型是否能充分地回答问题？ 
     * 是否应尝试备用方法？ 是否应收集其他数据、进行更多的功能设计或使用其他算法进行试验？

## <a name="next-steps"></a>后续步骤

以下是 TDSP 生命周期中每个步骤的链接：

   1. [了解业务](lifecycle-business-understanding.md)
   2. [数据采集和理解](lifecycle-data.md)
   3. [建模](lifecycle-modeling.md)
   4. [部署](lifecycle-deployment.md)
   5. [客户验收](lifecycle-acceptance.md)

我们还提供了完整的演练，演示特定方案过程中的所有步骤。 [示例演练](walkthroughs.md)一文提供了包含链接和缩略图描述的方案列表。 该演练演示如何将云、本地工具以及服务结合到一个工作流或管道中，以创建智能应用程序。 

有关如何在使用 Azure 机器学习工作室的 TDSP 中执行步骤的示例，请参阅[通过 Azure 机器学习使用 TDSP](http://aka.ms/datascienceprocess)。 
