---
title: "Team Data Science Process 生命周期 | Microsoft Docs"
description: "使用生命周期步骤和组件构建数据科学项目。"
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: b1f677bb-eef5-4acb-9b3b-8a5819fb0e78
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: bradsev;hangzh;gokuma
translationtype: Human Translation
ms.sourcegitcommit: 1796f7a7cd174d7ed6582878d72c59995aac41cb
ms.openlocfilehash: 995ba0dc3ffd2bc78625db7d1176ca0d5e1611a0


---
# <a name="team-data-science-process-lifecycle"></a>Team Data Science Process 生命周期
Team Data Science Process (TDSP) 提供构建数据科学项目开发建议使用的生命周期。 生命周期概述了执行项目时，其从开始到结束所遵循的步骤。 如果使用其他的数据科学生命周期，如 [CRISP-DM](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining)、[KDD](https://wikipedia.org/wiki/Data_mining#Process) 或组织自己的自定义过程，仍可在这些开发生命周期的上下文中使用基于任务的 TDSP。 

此生命周期为数据科学项目而设计，这些项目旨在作为智能应用程序的一部分提供。 这些应用程序部署机器学习或人工智能模型以进行预测分析。 使用此过程也有益于探索数据科学项目和临时或开关分析项目，但在这种情况下，则可能无需执行某些所述步骤。    

以下是 **Team Data Science Process 生命周期**的可视化表示形式 。 

![TDSP-生命周期](./media/data-science-process-overview/tdsp-lifecycle.png) 

TDSP 生命周期由&5; 个以迭代方式执行的主要阶段组成。 其中包括：

* **了解业务**
* **数据采集和理解**
* **建模**
* **部署**
* **客户验收**

我们为每个阶段提供以下信息：

* **目标**：详细指明的特定目标。
* **如何执行**：描述特定任务以及提供完成任务的指南。
* **项目**：可交付结果和用于生成结果的支持。

## <a name="1-business-understanding"></a>1.了解业务
### <a name="goals"></a>目标
* 指定**关键变量**以充当 **模型目标**，并将其相关的指标用于确定项目是否成功。
* 标识的相关**数据源**，需要时业务能够对此类数据源进行访问，或从其他源对其进行访问。

### <a name="how-to-do-it"></a>如何执行
在此阶段中解决了两个主要任务： 

* **定义目标**：与客户和其他利益干系人协同合作，以了解和确定业务问题。 制定定义业务目标并且能够通过数据科学技术得到解决的问题。
* **标识数据源**：查找相关数据，这些数据有助于解决定义项目目标的问题。

#### <a name="11-define-objectives"></a>1.1 定义目标
1. 此步骤的主要目标是标识分析进行预测所需的关键**业务变量**。 这些变量被称为**模型目标**，而与之关联的指标则用于确定项目是否成功。 销售预测或订单涉嫌欺诈的概率是此类目标的示例。
2. 通过提出和完善相关、特定和明确的“尖锐”问题以定义**项目目标**。 数据科学是使用名称和数字来回答此类问题的过程。 “选择问题时，想象你正在接近可以告知你宇宙万物的圣言，只是在那一刻，所有的答案都只是数字或名称。” 有关其他指南，请参阅 [How to do Data Science](https://blogs.technet.microsoft.com/machinelearning/2016/03/28/how-to-do-data-science/)（如何实现数据科学）博客的**提出尖锐问题**一节。   数据科学/机器学习通常用于回答以下五类问题：
   * 多少？ （回归）
   * 哪一类别？ （分类）
   * 哪一组？ （群集）
   * 这是否很奇怪？ （异常情况检测）
   * 应采用哪些选项？ （建议）
3. 通过指定角色及其成员的责任，定义**项目工作组**。 随着更多信息的发现，制定可以循环访问的高级里程碑计划。  
4. **定义成功的指标**。 例如：此 3 个月的项目结束时，实现 X % 的客户流失预测准确率，使我们能够改进以减少客户流失率。 指标必须为 **SMART**： 
   * **S** - 明确 (Specific) 
   * **M** - 可测量 (Measurable)
   * **A** - 可实现 (Achievable) 
   * **R** - 相关 (Relevant) 
   * **T** - 有时限 (Time-bound) 

#### <a name="12-identify-data-sources"></a>1.2 标识数据源
标识包含尖锐问题答案的已知示例的数据源。 查找以下数据：

* 与问题**相关**的数据。 我们是否具有针对目标以及与该目标相关功能的度量值？
* 作为我们的模型目标和感兴趣功能的**准确度量值**的数据。

查找现有系统需要收集和记录的、解决此问题的其他数据，进而解决此问题、实现项目目标，这种情况也很常见。 在这种情况下，可查找外部数据源或更新系统以收集较新的数据。

### <a name="artifacts"></a>项目
以下是此阶段中的可交付结果：

* [**章程文档**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Charter.md)：TDSP 项目结构定义中提供的一个标准模板。 这是一个动态文档，该文档需要在整个项目的进行中随着新的发现以及业务需求的更改进行更新。 关键是在发现过程中有进展时，对本文档进行循环访问，以添加更多详细信息。 让客户和其他利益干系人参与到更改中，并清楚地与他们交流更改的原因。  
* [**数据源**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/Data%20Defintion.md#raw-data-sources)：这是在 TDSP 项目结构中的数据报表的一部分。 它介绍了原始数据的源。 在后续阶段中，你需要填写脚本等其他详细信息，以将数据移到分析环境中。  
* [**数据**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/DataDictionaries)：本文档为用于回答问题的数据提供相关说明和架构（数据类型、验证规则信息（如果有）等）。 如果适用，也会包括实体关系图或说明。

## <a name="2-data-acquisition-and-understanding"></a>2.数据采集和理解
### <a name="goals"></a>目标
* 一个纯净、高质量的数据集，其与目标变量的关系被认为位于分析环境中，准备用于建模。
* 开发了数据管道的一个解决方案体系结构，以对数据定期进行刷新和评分。

### <a name="how-to-do-it"></a>如何执行
在此阶段中解决了三个主要任务：

* **将数据引入**到目标分析环境中。
* **浏览数据**以确定数据质量是否足以回答问题。 
* **设置数据管道**以对新数据或定期刷新的数据进行评分。

#### <a name="21-ingest-the-data"></a>2.1 引入数据
该过程设置为将数据从源位置移动到要执行如定型和预测等分析操作的目标位置。 若要了解如何使用各种 Azure 数据服务执行此操作的技术详细信息和选项，请参阅[将数据加载到存储环境以进行分析](machine-learning-data-science-ingest-data.md)。 

#### <a name="22-explore-the-data"></a>2.2 浏览数据
在对模型定型之前，需要对数据进行深刻理解。 实际的数据集通常比较杂乱、缺少值或存在大量其他差异。 数据摘要和可视化可用于审核数据的质量，并且提供准备进行建模前对数据进行处理所需的信息。 有关清理数据的指南，请参阅[用于准备数据以增强机器学习的任务](machine-learning-data-science-prepare-data.md)。 此过程通常是迭代的。 

TDSP 提供名为 [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils) 的自动化实用工具，以帮助实现数据的可视化并准备数据摘要报表。 我们建议首先从 IDEAR 开始浏览数据，这样有助于以交互方式开始了解数据，而无需进行编码，然后编写用于数据浏览和可视化的自定义代码。 

对清理后的数据质量满意后，下一步是更好地理解数据中的固有模式，这些模式有助于为目标选择并开发适当的预测模型。 查找证据以证明数据与目标的联系程度，以及是否有足够的数据来进行后续的建模步骤。 同样，此过程通常是迭代的。 可能需要查找包含更准确或更相关数据的新数据源，以增加在上一阶段中最初标识的数据集。  

#### <a name="23-set-up-a-data-pipeline"></a>2.3 设置数据管道
除了初始引入和清理数据以外，作为持续学习过程的一部分，通常还需设置对新数据进行评分或定期刷新数据的过程。 可以通过设置数据管道或工作流来实现上述目的。 以下是如何使用 [Azure 数据工厂](https://azure.microsoft.com/services/data-factory/)设置管道的[示例](machine-learning-data-science-move-sql-azure-adf.md)。 在此阶段中开发了数据管道的一个解决方案体系结构。 在进行以下阶段的数据科学项目时，也并行开发了管道。 根据具体的业务需求以及此管理方案集成到其中的现有系统的约束，该管道可能基于批处理、或为流式处理/实时或混合的。 

### <a name="artifacts"></a>项目
以下是此阶段中的可交付结果。

* [**数据质量报表**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/DataSummaryReport.md)：此报表包含数据摘要、每个属性和目标之间的关系、变量排名等。作为 TDSP 一部分提供的 [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils) 工具可在任何表格数据集（例如，CSV 文件或关系表）上生成此报表。 
* **解决方案体系结构**：此解决方案体系结构可以是生成模型后数据管道的关系图或说明，该数据管道用于对新数据进行评分或预测。 它还包含要基于新数据重新定型模型的管道。 使用 TDSP 目录结构模板时，文档存储在此[目录](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Project)中。
* **检查点决策**：在开始完整的功能设计和建模以前，可以重新评估项目以确定是否有足够的值来进行下一阶段。 例如，用户可能已准备好继续进行项目，但需要收集更多数据或由于没有数据可以回答问题而放弃项目。

## <a name="3-modeling"></a>3.建模
### <a name="goals"></a>目标
* 机器学习模型的最佳数据功能。
* 最准确地预测目标的信息性 ML 模型。
* 适用于生产的 ML 模型。

### <a name="how-to-do-it"></a>如何执行
在此阶段中解决了三个主要任务：

* **功能设计**：从原始数据创建数据功能，以实现模型定型。
* **模型定型**：通过比较模型成功的指标，找出最能准确回答问题的模型。
* 确定模型是否**适用于生产**。

#### <a name="31-feature-engineering"></a>3.1 功能设计
功能设计包括对原始变量的涵盖、聚合和转换，以创建分析中使用的功能。 如果要深入了解模型的驱动因素，则需要了解这些功能彼此间的关系，以及使用这些功能的机器学习算法方式。 此步骤需要创造性地组合领域专业知识，并从数据浏览步骤中获取见解。 这是在避免过多不相关的变量同时查找并涵盖信息性变量的一项平衡措施。 信息性变量会改善结果；而不相关的变量对模型引入了不必要的杂乱。 还需要为在评分过程中获取的任何新数据生成一些功能。 因此，可仅根据在评分时可用的数据生成这些功能。 有关使用各种 Azure 数据技术时的功能设计的相关技术指导，请参阅[数据科学过程中的功能设计](machine-learning-data-science-create-features.md)。 

#### <a name="32-model-training"></a>3.2 模型定型
根据所要回答的问题类型，有许多可用的建模算法。 有关选择算法的指南，请参阅[如何选择 Microsoft Azure 机器学习的算法](machine-learning-algorithm-choice.md)。 尽管本文是为 Azure 机器学习而编写的，但所提供的指南也适用于其他 ML 框架。 

模型定型的过程包括以下步骤： 

* 将用于建模的输入数据随机拆分为一个定型数据集和一个测试数据集。
* 使用定型数据集生成模型。
* 评估（定型数据集和测试数据集）一系列包含各种关联优化参数的机器学习算法（称为参数扫描），这些参数旨在回答对当前数据感兴趣的问题。
* 通过比较备用方法之间的成功指标，确定回答问题的“最佳”解决方案。

> [!NOTE]
> **避免泄露**：从定型数据集外涵盖数据可能会导致数据泄露，从而使模型或机器学习算法做出不切实际的良好预测。 泄露是数据科学家获得好到不真实的预测结果时会紧张的常见原因。 很难检测到这些依赖项。 为避免这种情况，通常需要在生成分析数据集、创建模型和计算准确性之间进行循环访问。 
> 
> 

我们提供包含 TDSP 的[自动化建模和报告工具](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/Modeling)，该工具能够运行多个算法和参数扫描以生成基准模型。 它还会生成基准建模报表，该报表汇总每个模型的性能和参数组合，包括变量重要性。 此过程可以进一步促进功能设计，因为它也是可以迭代的。 

### <a name="artifacts"></a>项目
在此阶段中生成的项目包括：

* [**功能集**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/Data%20Defintion.md#feature-sets)：在数据定义报表的功能设置部分中介绍的为建模所开发的功能。 它包括指向代码以生成功能的指针，以及如何生成功能的说明。
* [**建模报表**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Model/Model%201/Model%20Report.md)：对于尝试构建的每个模型，会生成遵循指定 TDSP 模板的标准报表。
* **检查点决策**：评估模型的执行是否足够良好，以便将其部署到生产系统。 要提出的一些关键问题有：
  * 在给定测试数据的情况下，模型是否能充分地回答问题？ 
  * 应尝试的备用方法：收集其他数据、进行更多的功能设计或使用其他算法进行试验？

## <a name="4-deployment"></a>4.部署
### <a name="goal"></a>目标
* 为了最终的用户验收，需要将模型和管道部署到生产或类似生产的环境中。 

### <a name="how-to-do-it"></a>如何执行
在此阶段中解决的主要任务：

* **使模型可操作化**：将模型和管道部署到生产或生产类似的环境中以供应用程序使用。

#### <a name="41-operationalize-a-model"></a>4.1 使模型可操作化
如果拥有一组运行良好的模型，也可以使这些模型可操作化，以供其他应用程序使用。 根据具体的业务需求，实时或基于批处理地作出决策。 若要实现可操作性，这些模型必须使用开放的 API 接口进行公开，此接口可从在线网站、电子表格、仪表板或业务线和后端应用程序等各种应用程序轻松进行访问。 有关使用 Azure 机器学习 Web 服务使模型可操作化的示例，请参阅[部署 Azure 机器学习 Web 服务](machine-learning-publish-a-machine-learning-web-service.md)。 另一个好办法是将遥测和监视构建到所部署的生产模型和数据管道中进行监视，以帮助进行系统状态报告和故障排除。  

### <a name="artifacts"></a>项目
* 系统运行状况和关键指标的状态仪表板。
* 具有部署详细信息的最终建模报表。
* 最终解决方案体系结构文档。

## <a name="5-customer-acceptance"></a>5.客户验收
### <a name="goal"></a>目标
* **完成项目可交付结果**：确认生产环境中的管道、模型及其部署满足客户的目标。

### <a name="how-to-do-it"></a>如何执行
在此阶段中解决了三个主要任务：

* **系统验证**：确认部署的模型和管道满足客户需求。
* **项目提交**：提交到将在生产中运行系统的实体。

客户将验证系统是否满足其业务需求和回答问题的准确性，以将系统部署到生产供其客户端应用程序使用。 最终确定所有文档，并进行评审。 已完成将项目提交到负责操作的实体。 例如，这可能是 IT、客户数据科学团队或负责在生产中运行系统的客户代理。 

### <a name="artifacts"></a>项目
在此最后阶段中生成的主要项目是[**项目最终报表**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Exit%20Report.md)。 这是一份项目技术报表，其中包含项目所有有助于了解和操作系统的详细信息。 TDSP 提供可按原样使用或根据特定客户需求进行自定义的[模板](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Exit%20Report.md)。 

## <a name="summary"></a>摘要
将 [Team Data Science Process 生命周期](http://aka.ms/datascienceprocess)建模为一系列的迭代步骤，这些步骤为使用预期模型所需的任务提供指导。 可以将这些模型部署在生产环境中，以利用它生成智能应用程序。 此过程生命周期的目标是将数据科学项目继续推动到明确的参与终点。 尽管数据科学就是一项研究和发现的练习，但如果能够使用一组定义完善、采用标准化模板的项目清楚地与团队和客户交流，这将有助于避免误解并能增加成功完成复杂的数据科学项目的可能性。

## <a name="next-steps"></a>后续步骤
还提供了完整的、端到端的演练，演示**特定方案**处理过程中的所有步骤。 在 [Team Data Science Process 演练](data-science-process-walkthroughs.md)主题中，列出并链接了相关步骤和缩略图说明。




<!--HONumber=Feb17_HO2-->


