---
title: 使用团队数据科学过程模板构建项目 | Microsoft Docs
description: 如何实例化 Azure 机器学习中用于构建项目以实现协作的团队数据科学过程 (TDSP) 模板
services: machine-learning
documentationcenter: ''
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: bradsev
ms.openlocfilehash: 5b53bd3ec479ba6e096b4d00089f968e37f0135c
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2018
ms.locfileid: "34831762"
---
# <a name="structure-projects-with-the-team-data-science-process-template"></a>使用团队数据科学过程模板构建项目

本文档介绍如何在 Azure 机器学习中使用团队数据科学过程 (TDSP) 模板创建数据科学项目。 这些模板有助于构建项目以实现协作和可再现性。 


## <a name="what-is-the-team-data-science-process"></a>什么是团队数据科学过程？
TDSP 是一种执行并交付高级分析解决方案的敏捷、迭代的数据科学过程。 它旨在改进企业组织中数据科学团队的协作和效率。 它通过四个关键组件支持这些目标：

   * 标准[数据科学生命周期](../team-data-science-process/lifecycle.md)定义。
   * 标准化项目结构、[项目文档和报告模板](https://github.com/Azure/Azure-TDSP-ProjectTemplate)。
   * 用于执行项目的基础结构和资源，例如分别为计算和存储基础结构与代码存储库。
   * 用于数据科学项目任务的[工具和实用程序](https://github.com/Azure/Azure-TDSP-Utilities)，例如：
      - 协作式版本控制
      - 代码评审
      - 数据探索和建模
      - 工作计划

有关 TDSP 更完整的讨论，请参阅[团队数据科学过程概述](../team-data-science-process/overview.md)。

## <a name="why-should-you-use-the-tdsp-structure-and-templates"></a>为何应使用 TDSP 结构和模板？
实现数据科学项目结构、生命周期和文档的标准化，对于促进数据科学团队的有效协作很重要。 使用 TDSP 模板创建机器学习项目可以为协调团队协作提供此类框架。

我们此前已发布[适用于 TDSP 项目结构和模板的 GitHub 存储库](https://github.com/Azure/Azure-TDSP-ProjectTemplate)来帮助实现这些目标。 但直到现在才能够在数据科学工具中实例化 TDSP 结构和模板。 现在可以创建实例化 TDSP 结构和文档模板的机器学习项目。 

## <a name="things-to-note-before-creating-a-new-project"></a>新建项目之前要注意的事项
在新建项目之前，请审查以下各项：
* 审查 TDSP 机器学习[模板](https://aka.ms/tdspamlgithubrepo)。
* 内容（“docs”文件夹中已存在的内容除外）大小需要小于 25 MB。 请参阅此列表后面的说明。
* sample\_data 文件夹只能存放用于测试代码或开始早期开发的小型数据文件（小于 5 MB）。
* 存储 Word 和 PowerPoint 等文件会大幅增加“docs”文件夹的大小。 建议查找协作 Wiki、[SharePoint](https://products.office.com/en-us/sharepoint/collaboration) 或其他协作资源来存储此类文件。
* 若要了解如何在机器学习中处理大型文件和输出的信息，请阅读[持久保存更改和处理大型文件](http://aka.ms/aml-largefiles)。

> [!NOTE]
> 在项目执行期间未使用的所有文档相关内容（readme.md 文件除外）必须驻留在名为“docs”（全小写）的文件夹中，这些内容包括文本、Markdown、图像和其他文档文件。 “docs”文件夹是一个被机器学习执行忽略的特殊文件夹，目的是不让此文件夹中的内容不必要地被复制到计算目标中。 此文件夹中的对象也不计入项目大小的 25 MB 上限。 例如，“docs”文件夹是存储文档中所需的大型图像文件的位置。 但 Git 仍然会通过运行历史记录跟踪这些文件。 

## <a name="instantiate-the-tdsp-structure-and-templates-from-the-machine-learning-template-gallery"></a>实例化来自机器学习模板库的 TDSP 结构和模板
若要使用 TDSP 结构和文档模板创建新项目，请完成以下过程。

### <a name="create-a-new-project"></a>创建新项目
若要创建新项目，请打开 Azure 机器学习。 在左上角窗格的“项目”下，选择加号（“+”），然后选择“新建项目”。

![新建项目](./media/how-to-use-tdsp-in-azure-ml/instantiation-1.png)


### <a name="create-a-new-tdsp-structured-project"></a>创建新的 TDSP 结构化项目
   1. 在相关框或列表中指定参数和信息：

      - 项目名称
      - 项目目录
      - 项目说明
      - 空的 Git 存储库路径
      - 工作区名称

   2. 然后在“搜索”框中，输入“TDSP”。 
   3. 出现“使用 TDSP 构建项目”选项时，选择该模板。 
   4. 选择“创建”按钮以创建具有 TDSP 结构的新项目。 如果在创建项目时提供了空的 Git 存储库（在相应的框中），则在创建项目后，系统会为该存储库填充项目结构和内容。

![创建 TDSP 项目](./media/how-to-use-tdsp-in-azure-ml/instantiation-2.png)


## <a name="examine-the-tdsp-project-structure"></a>检查 TDSP 项目结构
创建新项目后，可以检查其结构（请看下图中的左侧面板）。 它包含用于了解业务的标准化文档的所有方面。 这些项包括 TDSP 生命周期阶段、数据位置、定义和此文档模板的体系结构。 

所示的结构派生自 [TDSP project structure, documents, and artifact templates](https://github.com/Azure/Azure-TDSP-ProjectTemplate)（TDSP 项目结构、文档和项目模板）中发布的 TDSP 结构，并经过了一些修改。 例如，多个文档模板合并到一个 Markdown，也就是 [ProjectReport](https://aka.ms/tdspamlgithubrepoprojectreport)。 

### <a name="project-folder-structure"></a>项目文件夹结构
TDSP 项目模板包含以下顶层文件夹：
   - **code**：包含代码。
   - **docs**：包含有关项目的必要文档（例如 Markdown 文件和相关媒体等）。
   - **sample_data**：包含可用于早期开发或测试的 SAMPLE (small) 数据。 通常，这些集的大小不超过几 (5) MB。 此文件夹不适用于完整或大型数据集。

![样本数据](./media/how-to-use-tdsp-in-azure-ml/instantiation-3.png)


## <a name="use-the-tdsp-structure-and-templates"></a>使用 TDSP 结构和模板
需要向结构和模板添加特定于项目的信息。 应使用执行和交付项目所需的代码和信息填充这些结构和模板。 [ProjectReport](https://aka.ms/tdspamlgithubrepoprojectreport) 文件是一个模板，需要使用与项目相关的信息修改该模板。 它附带一组问题，可帮助你填写 [TDSP 生命周期](../team-data-science-process/lifecycle.md)四个阶段中每个阶段的信息。

下图中的左侧面板显示了在执行期间或执行完成后项目结构的外观示例。 此项目是来自[团队数据科学过程示例项目：在 Azure 机器学习中对美国人口普查数据中的收入进行分类](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome)的示例项目。

![项目结构示例](./media/how-to-use-tdsp-in-azure-ml/instantiation-4.png)

## <a name="document-your-project"></a>记录项目
请参阅 [TDSP 文档模板](https://github.com/Azure/Azure-TDSP-ProjectTemplate)，了解如何记录项目的相关信息。 在当前的机器学习 TDSP 文档模板中，建议将所有信息都包含在 [ProjectReport](https://aka.ms/tdspamlgithubrepoprojectreport) 文件中。 此模板应使用特定于项目的信息进行填充。 

我们还提供 [ProjectLearnings](https://aka.ms/tdspamlgithubrepoprojectlearnings) 模板。 可使用此模板来包含主项目文档中不包含但仍对文档有用的所有信息。 

### <a name="example-project-report"></a>项目报告示例
可获取[项目报告示例](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/blob/master/docs/deliverable_docs/ProjectReport.md)。 此[美国收入分类示例项目](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome)的项目报告说明了如何实例化 TDSP 模板并将其用于数据科学项目。

## <a name="next-steps"></a>后续步骤
为便于你了解如何在机器学习项目中使用 TDSP 结构和模板，我们在机器学习的相关文档中提供了多个已完成的项目示例：

- 有关演示如何在机器学习中创建 TDSP 项目的示例，请参阅[团队数据科学过程示例项目：在 Azure 机器学习中对美国人口普查数据中的收入进行分类](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome)。
- 有关在机器学习中的 TDSP 实例化项目的自然语言处理 (NLP) 中使用深度学习的示例，请参阅[搭配使用自然语言处理和深度学习来识别生物医学实体](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction)。

