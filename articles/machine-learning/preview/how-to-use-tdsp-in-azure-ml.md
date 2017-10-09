---
title: "使用团队数据科学过程模板构建项目 | Microsoft Docs"
description: "如何实例化 Azure ML 中用于构建项目的团队数据科学过程 (TDSP) 模板，以实现协作。"
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
ms.date: 09/17/2017
ms.author: bradsev
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 2e03b6bcb1bc5a617234c7801f22f8207a9f5a4e
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---

# <a name="structure-projects-with-team-data-science-process-template"></a>使用团队数据科学过程模板构建项目

本文档说明如何使用用于构建项目的团队数据科学过程 (TDSP) 模板，在 Azure 机器学习中创建数据科学项目，以实现协作和可再现性。 


## <a name="what-is-team-data-science-process"></a>什么是团队数据科学过程？
团队数据科学过程是一种执行并交付高级分析解决方案的敏捷、迭代的数据科学过程。 它旨在改进企业组织中的协作和数据科学团队效率。 它通过四个关键组件支持这些目标：

1. 标准[数据科学生命周期](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md)定义。
2. 标准化项目结构[项目文档和报告模板](https://github.com/Azure/Azure-TDSP-ProjectTemplate)
3. 用于执行项目的基础结构和资源，比如计算和存储基础结构以及代码存储库。
4. 用于执行数据科学项目任务的[工具和实用程序](https://github.com/Azure/Azure-TDSP-Utilities)，比如协作版本控制和代码评审、数据浏览和建模以及工作计划。

有关 TDSP 更完整的讨论，请参阅[团队数据科学过程概述](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/README.md)。

## <a name="why-should-you-use-tdsp-structure-and-templates"></a>为何使用 TDSP 结构和模板？
实现数据科学项目结构、生命周期和文档的标准化，对于促进数据科学团队的有效协作很重要。 使用 TDSP 模板创建 Azure 机器学习项目可以为协调团队协作提供一个框架。

我们此前已发布[适用于 TDSP 项目结构和模板的 GitHub 存储库](https://github.com/Azure/Azure-TDSP-ProjectTemplate)来帮助实现这些目标， 但直到现在才能够在数据科学工具中实例化 TDSP 结构和模板。 现在可以创建 Azure 机器学习项目来实例化 TDSP 结构和文档模板。 

## <a name="things-to-note-before-creating-a-new-project"></a>新建项目*之前*要注意的事项
新建项目*之前*应注意或复查以下事项：
* TDSP Azure 机器学习[模板](https://aka.ms/tdspamlgithubrepo)。
* 内容（“docs”文件夹中的内容除外）大小需小于 25 Mb。 请参阅下面的**注意**。
* sample\_data 文件夹只能存放用于测试代码或进行早期开发的小型数据文件（小于 5 Mb）。
* 存储 Office Word、PowerPoint 等文件会大幅增加“docs”文件夹的大小。 建议查找协作 Wiki、[SharePoint](https://products.office.com/en-us/sharepoint/collaboration) 或其他协作资源来存储此类文件。
* 有关如何在 Azure 机器学习中处理大型文件和输出的信息，请阅读[此文](http://aka.ms/aml-largefiles)。

> [!NOTE]
> 确保在项目执行期间未使用的所有文档相关内容（readme.md 文件除外）均驻留在名为“docs”（全小写）的文件夹中，这种内容包括文本、Markdown、图像和其他文档文件。 这是一个被 Azure 机器学习执行忽略的特殊文件夹，目的是不让此文件夹中的内容不必要地复制到计算目标中。 另外，此文件夹中的对象不计入 25 MB 的项目大小上限，因此可以存储文档中所需的大型图像文件或其他文件。 但 Git 仍然会通过运行历史记录跟踪它们。 

## <a name="instantiating-tdsp-structure-and-templates-from-the-azure-machine-learning-template-gallery"></a>实例化来自 Azure 机.器学习模板库的 TDSP 结构和模板
若要使用团队数据科学过程结构和文档模板创建新项目，请完成以下过程： 

### <a name="click-on-new-project"></a>单击“新建项目”
打开 Azure 机器学习。 在左上角的“项目”下，单击 **+** 并选择“新建项目”创建新项目。

![新建项目](./media/how-to-use-tdsp-in-azure-ml/instantiation-1.png)


### <a name="creating-a-new-tdsp-structured-project"></a>创建新的 TDSP 结构化项目
在相关框中指定参数和信息：

- 项目名称
- 项目目录
- 项目说明
- 空的 Git 存储库路径
- 工作区名称

然后在“搜索”框中，键入 *TDSP*。 显示“使用 TDSP 构建项目”时，单击选择该模板。 然后单击“创建”按钮，使用 TDSP 结构创建新项目。 如果在创建项目的过程中（在相应的框中）提供了空的 Git 存储库，则在创建项目以后，系统会为该存储库填充项目结构和内容。

![创建 TDSP 项目](./media/how-to-use-tdsp-in-azure-ml/instantiation-2.png)


## <a name="examine-the-tdsp-project-structure"></a>检查 TDSP 项目结构
创建新项目后，可以检查其结构（下图的左侧面板）。 本文档模板包含标准化文档的方方面面（便于了解业务）、TDSP 生命周期的各个阶段、数据位置、定义和体系结构。 此结构派生自[此处](https://github.com/Azure/Azure-TDSP-ProjectTemplate)发布的 TDSP 结构，但进行了部分修改。 例如，多个文档模板合并到一个 Markdown，也就是 [ProjectReport](https://aka.ms/tdspamlgithubrepoprojectreport)。 

### <a name="project-folder-structure"></a>项目文件夹结构
TDSP 项目模板包含以下顶层文件夹：
1. **code**：包含代码
2. **docs**：包含有关项目的必要文档（例如 Markdown 文件、相关媒体等）
3. **sample_data**：包含可用于早期开发或测试的 **SAMPLE (small)** 数据。 通常不超过数 (5) Mb。 不适用于完整或大型数据集。

![样本数据](./media/how-to-use-tdsp-in-azure-ml/instantiation-3.png)


## <a name="using-the-tdsp-structure-and-templates"></a>使用 TDSP 结构和模板
这些结构和模板需要用特定于项目的信息填充。 用户应当用执行和交付项目所需的代码和信息填充这些结构和模板。 [ProjectReport](https://aka.ms/tdspamlgithubrepoprojectreport) 文件是一个模板，应使用与项目相关的信息直接修改该模板。 它附带一组问题，可帮助用户填写[团队数据科学过程生命周期](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md)四个阶段中每个阶段的信息。

下面提供了项目结构在执行期间或完成之后的外观示例（下图的左侧面板）。 该示例来自 [Team Data Science Process Sample Project: Classify incomes from US Census data in Azure Machine Learning](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome)（团队数据科学过程示例项目：在 Azure 机器学习中对美国人口普查数据中的收入进行分类）。

![项目结构示例](./media/how-to-use-tdsp-in-azure-ml/instantiation-4.png)

## <a name="documenting-your-project"></a>记录项目
请参考 [TDSP 文档模板](https://github.com/Azure/Azure-TDSP-ProjectTemplate)以记录项目。 在当前的 Azure 机器学习 TDSP 文档模板中，建议将所有信息包含在 [ProjectReport](https://aka.ms/tdspamlgithubrepoprojectreport) 文件中。 此模板应使用特定于项目的信息进行填充。 

我们还提供另一个 [ProjectLearnings](https://aka.ms/tdspamlgithubrepoprojectlearnings) 模板，用于包含主项目文档中不包含但仍对文档有用的所有信息。 

### <a name="example-project-report"></a>项目报告示例
[此处](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/blob/master/docs/deliveralbe_docs/ProjectReport.md)提供一个项目报告示例。 这是 [US Income Classification sample project](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome)（美国收入分类示例项目）的项目报告，其中说明了如何实例化 TDSP 模板并将其用于数据科学项目。

## <a name="next-steps"></a>后续步骤
为便于用户了解如何在 Azure 机器学习项目中使用 TDSP 结构和模板，我们在 Azure 机器学习相关文档中提供了多个已编制的项目示例。

- 有关演示如何在 Azure 机器学习中创建 TDSP 项目的示例，请参阅 [Team Data Science Process Sample Project: Classify incomes from US Census data in Azure Machine Learning](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome)（团队数据科学过程示例项目：在 Azure 机器学习中对美国人口普查数据中的收入进行分类） 
- 有关在 Azure 机器学习的 TDSP 实例化项目的 NLP 中使用深度学习的示例，请参阅 [Bio-medical entity recognition using Natural Language Processing with Deep Learning](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction)（搭配使用自然语言处理和深度学习来识别生物医学实体）

