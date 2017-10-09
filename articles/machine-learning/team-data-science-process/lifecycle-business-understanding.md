---
title: "Azure Team Data Science Process 生命周期的业务理解阶段 | Microsoft Docs"
description: "你的数据科学项目的业务理解阶段的目标、任务和可交付结果。"
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
ms.openlocfilehash: 2adce61f8185bd86a6a870bb5752fe936701b0af
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---
# <a name="business-understanding"></a>了解业务

本主题概述了与 Team Data Science Process 的**业务理解阶段**相关联的目标、任务和可交付结果。 此过程提供了可用于构建数据科学项目的建议生命周期。 该生命周期概述了项目通常执行的主要阶段（通常以迭代方式进行）：

* **了解业务**
* **数据采集和理解**
* **建模**
* **部署**
* **客户验收**

以下是 **Team Data Science Process 生命周期**的可视化表示形式 。 

![TDSP-Lifecycle2](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>目标
* 指定**关键变量**以充当 **模型目标**，并将其相关的指标用于确定项目是否成功。
* 将相关数据源标识为业务有权访问或需要获取。

## <a name="how-to-do-it"></a>如何执行
在此阶段中解决了两个主要任务： 

* **定义目标**：与客户和其他利益干系人协同合作，以了解和确定业务问题。 制定定义业务目标并且能够通过数据科学技术得到解决的问题。
* **标识数据源**：查找相关数据，这些数据有助于解决定义项目目标的问题。

### <a name="11-define-objectives"></a>1.1 定义目标

1. 此步骤的主要目标是标识分析进行预测所需的关键**业务变量**。 这些变量被称为**模型目标**，而与之关联的指标则用于确定项目是否成功。 此类目标的两个示例是销售预测或订单涉嫌欺诈的概率。

2. 通过提出和完善相关、特定和明确的“尖锐”问题以定义**项目目标**。 数据科学是使用名称和数字来回答此类问题的过程。 有关如何提出尖锐问题的详细信息，请参阅[如何执行数据科学](https://blogs.technet.microsoft.com/machinelearning/2016/03/28/how-to-do-data-science/)博文。 数据科学/机器学习通常用于回答以下五类问题：
 
   * 多少？ （回归）
   * 哪一类别？ （分类）
   * 哪一组？ （群集）
   * 这是否很奇怪？ （异常情况检测）
   * 应采用哪些选项？ （建议）

    确定要提出上述哪些问题，以及如何回答才能实现业务目标。

3. 通过指定角色及其成员的责任，定义**项目工作组**。 随着更多信息的发现，制定可以循环访问的高级里程碑计划。  

4. **定义成功的指标**。 例如：此 3 个月的项目结束时，实现 X % 的客户流失预测准确率，使我们能够改进以减少客户流失率。 指标必须为 **SMART**： 
   * **S** - 明确 (Specific) 
   * **M** - 可测量 (Measurable)
   * **A** - 可实现 (Achievable) 
   * **R** - 相关 (Relevant) 
   * **T** - 有时限 (Time-bound) 

### <a name="12-identify-data-sources"></a>1.2 确定数据源
标识包含尖锐问题答案的已知示例的数据源。 查找以下数据：

* 与问题**相关**的数据。 我们是否具有针对目标以及与该目标相关功能的度量值？
* 作为我们的模型目标和感兴趣功能的**准确度量值**的数据。

查找现有系统需要收集和记录的、解决此问题的其他数据，进而解决此问题、实现项目目标，这种情况也很常见。 在这种情况下，建议查找外部数据源或将系统更新为收集新数据。

## <a name="artifacts"></a>项目
以下是此阶段中的可交付结果：

* [**章程文档**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Charter.md)：TDSP 项目结构定义中提供的一个标准模板。 这是一个动态文档，该文档需要在整个项目的进行中随着新的发现以及业务需求的更改进行更新。 关键是在发现过程中有进展时，对本文档进行循环访问，以添加更多详细信息。 让客户和其他利益干系人参与到更改中，并清楚地与他们交流更改的原因。  
* [**数据源**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/Data%20Defintion.md#raw-data-sources)：这是 TDSP 项目“数据报表”文件夹中“数据定义”报表的“原始数据源”部分。 它指定原始数据的原始和目标位置。 在后续阶段中，需要填写脚本等其他详细信息，以将数据移到分析环境中。  
* [**数据字典**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/DataDictionaries)：本文档包含客户端提供的数据说明。 这些说明介绍了架构（数据类型、验证规则的相关信息（若有））和实体关系图（若有）。

## <a name="next-steps"></a>后续步骤

以下是 Team Data Science Process 生命周期中每个步骤的链接：

* [1.了解业务](lifecycle-business-understanding.md)
* [2.数据采集和理解](lifecycle-data.md)
* [3.建模](lifecycle-modeling.md)
* [4.部署](lifecycle-deployment.md)
* [5.客户验收](lifecycle-acceptance.md)

还提供了完整的、端到端的演练，演示**特定方案**处理过程中的所有步骤。 在[示例演练](walkthroughs.md)主题中，列出了相关步骤并链接了缩略图说明。 具体展示了如何将云、本地工具以及服务结合到一个工作流或管道中，以创建智能应用程序。 

有关在 Team Data Science Process 中执行使用 Microsoft Azure 机器学习工作室的步骤的示例，请参阅[结合使用 Azure ML](http://aka.ms/datascienceprocess) 学习路径。
