---
title: Team Data Science Process 生命周期的业务理解阶段 - Azure | Microsoft Docs
description: 你的数据科学项目业务理解阶段的目标、任务和可交付结果
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: deguhath
ms.openlocfilehash: 6d8eedbbf4a682443e73ecb9cf9496f3cdd1cd9d
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2018
ms.locfileid: "34837195"
---
# <a name="business-understanding"></a>了解业务

本文概述了与 Team Data Science Process (TDSP) 的业务理解阶段相关联的目标、任务和可交付结果。 此过程提供可用于构建数据科学项目的建议生命周期。 该生命周期概述了项目通常执行的主要阶段（通常以迭代方式进行）：

   1. **了解业务**
   2. **数据采集和理解**
   3. **建模**
   4. **部署**
   5. **客户验收**

此处直观地展示了 TDSP 生命周期： 

![TDSP 生命周期](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>目标
* 指定关键变量以充当 模型目标，并使用其相关指标来确定项目是否成功。
* 确定业务有权访问或需要获取的相关数据源。

## <a name="how-to-do-it"></a>如何执行
在此阶段中解决了两个主要任务： 

   * **定义目标**：与客户和其他利益干系人协同合作，以了解和确定业务问题。 制定定义业务目标且能够通过数据科学技术得到解决的问题。
   * **标识数据源**：查找相关数据，这些数据有助于解决定义项目目标的问题。

### <a name="define-objectives"></a>定义目标
1. 此步骤的主要目标是确定分析需要预测的关键业务变量。 这些变量被称为模型目标，而与之关联的指标则用于确定项目是否成功。 此类目标的两个示例是销售预测或订单涉嫌欺诈的概率。

2. 通过提出和完善相关、特定和明确的“尖锐”问题以定义项目目标。 数据科学是使用名称和数字来回答此类问题的过程。 有关如何提出尖锐问题的详细信息，请参阅[如何执行数据科学](https://blogs.technet.microsoft.com/machinelearning/2016/03/28/how-to-do-data-science/)博文。 数据科学或机器学习通常用于回答以下五类问题：
 
   * 多少？ （回归）
   * 哪一类别？ （分类）
   * 哪一组？ （群集）
   * 这是否很奇怪？ （异常情况检测）
   * 应采用哪些选项？ （建议）

   确定要提出上述哪些问题，以及如何回答才能实现业务目标。

3. 通过指定角色及其成员的责任，定义项目团队。 随着发现的信息不断增多，制定可以循环访问的高级里程碑计划。 

4. 定义成功指标。 例如，你可能想要预测客户流失。 在为期三个月的项目结束时，准确率需要达到百分之“x”。 有了这些数据，你就可以进行客户促销，以减少客户流失。 指标必须为 **SMART**： 

   * **S** - 明确 (Specific) 
   * **M** - 可测量 (Measurable)
   * **A** - 可实现 (Achievable) 
   * **R** - 相关 (Relevant) 
   * **T** - 有时限 (Time-bound) 

### <a name="identify-data-sources"></a>确定数据源
标识包含尖锐问题答案的已知示例的数据源。 查找以下数据：

* 与问题相关的数据。 是否具有针对目标以及与该目标相关的功能的度量值？
* 作为模型目标和感兴趣功能的准确度量值的数据。

例如，你可能会发现现有系统需要收集和记录其他数据，进而解决此问题、实现项目目标。 在这种情况下，你需要查找外部数据源或更新系统以收集新数据。

## <a name="artifacts"></a>项目
以下是此阶段中的可交付结果：

   * [章程文档](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Charter.md)：TDSP 项目结构定义中提供的一个标准模板。 章程文档是一个动态文档。 在发现新内容时，业务需求发生变化时，请更新整个项目中的模板。 关键是在发现过程中有进展时，对本文档进行循环访问，以添加更多详细信息。 让客户和其他利益干系人参与到更改中，并清楚地与他们交流更改的原因。  
   * [数据源](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/Data%20Defintion.md#raw-data-sources)：TDSP 项目“数据报表”文件夹中的“数据定义”报表的“原始数据源”部分包含数据源。 此部分指定原始数据的原始位置和目标位置。 在后续阶段中，需要填写脚本等其他详细信息，以将数据移到分析环境中。  
   * [数据字典](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Data_Dictionaries)：本文档包含客户端提供的数据说明。 这些说明介绍了架构（数据类型、验证规则的相关信息（若有））和实体关系图（若有）。

## <a name="next-steps"></a>后续步骤

以下是 TDSP 生命周期中每个步骤的链接：

   1. [了解业务](lifecycle-business-understanding.md)
   2. [数据采集和理解](lifecycle-data.md)
   3. [建模](lifecycle-modeling.md)
   4. [部署](lifecycle-deployment.md)
   5. [客户验收](lifecycle-acceptance.md)

我们还提供了完整的演练，演示特定方案过程中的所有步骤。 [示例演练](walkthroughs.md)一文提供了包含链接和缩略图描述的方案列表。 该演练演示如何将云、本地工具以及服务结合到一个工作流或管道中，以创建智能应用程序。 

有关如何在使用 Azure 机器学习工作室的 TDSP 中执行步骤的示例，请参阅[通过 Azure 机器学习使用 TDSP](http://aka.ms/datascienceprocess)。
