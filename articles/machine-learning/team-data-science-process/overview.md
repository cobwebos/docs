---
title: 什么是团队数据科学过程？ | Microsoft Docs
description: 提供数据科学方法来交付预测分析解决方案和智能应用程序。
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: b1f677bb-eef5-4acb-9b3b-8a5819fb0e78
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: deguhath
ms.openlocfilehash: 4a73b988863a27f872c695cf209d2c46c6bb5f89
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2018
---
# <a name="what-is-the-team-data-science-process"></a>什么是团队数据科学过程？

Team Data Science Process (TDSP) 是一种敏捷的迭代式数据科学方法，可有效交付预测分析解决方案和智能应用程序。 TDSP 有助于改进团队协作和学习。 它包含来自 Microsoft 及本行业中其他企业的最佳做法与结构精华，能够促进数据科学方案的成功实现。 其目标是帮助公司完全实现其分析程序的优势。

本文提供 TDSP 及其主要组件的概述。 我们将提供可以通过各种工具实现的过程的一般说明。 其他链接主题中提供了过程生命周期所涉及的项目任务和角色的更详细说明。 此外，还提供了有关如何使用一套具体 Microsoft 工具和基础结构实现 TDSP 的指导（我们的团队就是使用这些工具和基础结构来实现 TDSP）。

## <a name="key-components-of-the-tdsp"></a>TDSP 的关键组件

TDSP 包括以下关键组件：

- **数据科学生命周期**定义
- **标准化项目结构**
- 数据科学项目的**基础结构和资源**
- 用于项目执行的**工具和实用程序**


## <a name="data-science-lifecycle"></a>数据科学生命周期

Team Data Science Process (TDSP) 提供用于构建数据科学项目开发的生命周期。 生命周期概述了执行项目时，其从开始到结束所遵循的步骤。

如果使用其他的数据科学生命周期，如 [CRISP-DM](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining)、[KDD](https://wikipedia.org/wiki/Data_mining#Process) 或组织自己的自定义过程，仍可在这些开发生命周期的上下文中使用基于任务的 TDSP。 从较高层面讲，这些不同的方法具有很多共性。 

此生命周期面向作为智能应用程序一部分交付的数据科学项目。 这些应用程序部署机器学习或人工智能模型以进行预测分析。 探索数据科学项目或即席分析项目也可从使用此过程获益。 但在这种情况下可能不需要某些所述的步骤。    

该生命周期概述了项目通常执行的主要阶段（通常以迭代方式进行）：

* **了解业务**
* **数据采集和理解**
* **建模**
* **部署**
* **客户验收**

以下是 **Team Data Science Process 生命周期**的可视化表示形式 。 

![TDSP-Lifecycle2](./media/overview/tdsp-lifecycle2.png) 

[Team Data Science Process 生命周期](lifecycle.md)主题中介绍了 TDSP 中每个生命周期阶段的目标、任务和文档项目。 这些任务和项目与项目角色相关联：

- 解决方案架构师
- 项目经理
- 数据科学家
- 项目主管 

下图提供了与这些角色（纵轴）的每个生命周期阶段（横轴）相关联的任务（蓝色）和项目（绿色）的网格视图。 

![TDSP-roles-and-tasks](./media/overview/tdsp-tasks-by-roles.png)

## <a name="standardized-project-structure"></a>标准化项目结构

让所有项目共享一个目录结构并对项目文档使用模板可以方便团队成员查找有关其项目的信息。 所有代码和文档存储在 Git、TFS 或 Subversion 等版本控制系统 (VCS) 中，以实现团队协作。 在 Jira、Rally、Visual Studio Team Services 等敏捷项目跟踪系统中跟踪任务和功能可以更密切地跟踪各项功能的代码。 此类跟踪还可让团队获得更准确的成本估算。 TDSP 建议在 VCS 中为每个项目创建一个独立的存储库，以实现版本控制、信息安全和协作。 为所有项目建立标准化结构有助于在整个组织中积累系统性的认知。

我们在标准位置为文件夹结构和所需的文档提供模板。 此文件夹结构可以组织包含用于数据探索和特征提取以及记录模型迭代的代码的文件。 这些模板可让团队成员更轻松地了解其他人完成的工作，以及将新成员添加到团队。 可以使用标记格式轻松查看和更新文档模板。 使用模板提供包含每个项目关键问题的检查列表，以确保完善定义问题，以及交付件满足预期的质量。 示例包括：

- 用于陈述业务问题和项目范围的项目纲领
- 用于陈述原始数据的结构和统计信息的数据报告
- 用于陈述派生特征的模型报告
- 模型性能指标，例如 ROC 曲线或 MSE


![TDSP-directories](./media/overview/tdsp-dir-structure.png)

可以从 [Github](https://github.com/Azure/Azure-TDSP-ProjectTemplate) 克隆目录结构。

## <a name="infrastructure-and-resources-for-data-science-projects"></a>数据科学项目的基础结构和资源  

TDSP 提供有关管理共享分析和存储基础结构的建议，例如：

- 用于存储数据集的云文件系统 
- 数据库
- 大数据（Hadoop 或 Spark）群集 
- 机器学习服务 

分析和存储基础结构可以在云中或本地。 这是原始数据集和已处理数据集的存储位置。 此基础结构实现重现的分析。 它还可以避免重复，防止产生不一致情况和不必要的基础结构成本。 TDSP 提供了工具用于预配和跟踪共享资源，并让每位团队成员安全连接到这些资源。 让项目成员创建一致的计算环境也是一种良好的做法。 然后，不同的团队成员可以复制和验证试验。

下面是团队处理多个项目和共享各个云分析基础结构组件的示例。

![TDSP-infrastructure](./media/overview/tdsp-analytics-infra.png)


## <a name="tools-and-utilities-for-project-execution"></a>用于项目执行的工具和实用程序

在大多数组织中引入过程很有难度。 TDSP 提供了工具用于实现数据科学过程和生命周期，帮助削减屏障，提高客户的采用一致性。 TDSP 提供了一套初始工具和脚本，帮助在团队中快速采用 TDSP。 另外，它还帮助自动完成数据科学生命周期中的某些常见任务，例如数据探索和基线建模。 它为个人提供一个完善定义的结构，以便在团队的共享代码存储库中贡献共享的工具和实用程序。 然后，这些资源可由团队或组织中的其他项目利用。 TDSP 还计划为向整个社区贡献工具和实用程序提供支持。 可从 [Github](https://github.com/Azure/Azure-TDSP-Utilities) 克隆 TDSP 实用程序。


## <a name="next-steps"></a>后续步骤

[Team Data Science Process：角色和任务](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/roles-tasks.md)概述了致力于标准化此过程的数据科学团队的关键人员角色及其相关任务。 
