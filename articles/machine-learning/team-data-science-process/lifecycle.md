---
title: "团队数据科学过程生命周期 — Azure | Microsoft Docs"
description: "执行数据科学项目所需的步骤。"
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: b1f677bb-eef5-4acb-9b3b-8a5819fb0e78
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/02/2017
ms.author: bradsev;
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 264386b527626f3241966bfdb2fb061781121be1
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---
# <a name="team-data-science-process-lifecycle"></a>Team Data Science Process 生命周期

Team Data Science Process (TDSP) 提供可用于构建数据科学项目的建议生命周期。 生命周期概述了执行项目时，其从开始到结束所遵循的步骤。 如果要使用其他数据科学生命周期，如 [CRISP-DM](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining)、[KDD](https://wikipedia.org/wiki/Data_mining#Process) 或组织自己的自定义过程，仍可在这些开发生命周期中使用基于任务的 TDSP。 

此生命周期为数据科学项目而设计，这些项目旨在作为智能应用程序的一部分提供。 这些应用程序部署机器学习或人工智能模型以进行预测分析。 探索数据科学项目和即席分析项目也可从使用此过程获益。 但在这种情况下可能不需要某些所述的步骤。    

TDSP 生命周期由 5 个以迭代方式执行的主要阶段组成。 其中包括：

* [1.了解业务](lifecycle-business-understanding.md)
* [2.数据采集和理解](lifecycle-data.md)
* [3.建模](lifecycle-modeling.md)
* [4.部署](lifecycle-deployment.md)
* [5.客户验收](lifecycle-acceptance.md)

以下是 **Team Data Science Process 生命周期**的可视化表示形式 。 

![TDSP-生命周期](./media/lifecycle/tdsp-lifecycle2.png) 


将团队数据科学过程生命周期建模为一系列迭代步骤，这些步骤为使用预测模型所需的任务提供指导。 可以将这些模型部署在生产环境中，以利用它生成智能应用程序。 此过程生命周期的目标是将数据科学项目继续推动到明确的参与终点。 尽管数据科学就是一项研究和发现的练习，但如果能够使用一组定义完善、采用标准化模板的项目清楚地将这些任务传达给团队和客户，这会有助于避免误解并能增加成功完成复杂的数据科学项目的可能性。

我们为每个阶段提供以下信息：

* **目标**：具体目标。
* **如何执行**：描述特定任务以及提供完成任务的指南。
* **项目**：可交付结果和用于生成结果的支持。

## <a name="next-steps"></a>后续步骤
还提供了完整的、端到端的演练，演示**特定方案**处理过程中的所有步骤。 [示例演练](walkthroughs.md)主题中列出了这些演练，并提供了缩略图说明链接。 这些演练演示如何将云、本地工具和服务合并到工作流或管道中，以创建智能应用程序。 

有关在团队数据科学过程中执行使用 Azure 机器学习工作室的步骤的示例，请参阅[使用 Azure ML](http://aka.ms/datascienceprocess) 学习路径。


