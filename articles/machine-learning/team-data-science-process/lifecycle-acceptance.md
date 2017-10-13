---
title: "团队数据科学过程生命周期的客户验收阶段 — Azure | Microsoft Docs"
description: "数据科学项目的客户验收阶段的目标、任务和可交付结果。"
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
ms.openlocfilehash: 953f090f775da5e48b2f4ed36550a5624ae4596b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="customer-acceptance"></a>客户验收

本主题概述与团队数据科学过程的**客户验收阶段**相关的目标、任务和可交付结果。 此过程提供可用于构建数据科学项目的建议生命周期。 该生命周期概述了项目通常执行的主要阶段（通常以迭代方式进行）：

* **了解业务**
* **数据采集和理解**
* **建模**
* **部署**
* **客户验收**

以下是 **Team Data Science Process 生命周期**的可视化表示形式 。 

![TDSP-Lifecycle2](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>目标
* **完成项目可交付结果**：确认生产环境中的管道、模型及其部署满足客户的目标。

## <a name="how-to-do-it"></a>如何执行
在此阶段中解决了两个主要任务：

* **系统验证**：确认部署的模型和管道满足客户需求。
* **项目提交**：提交到要在生产中运行系统的实体。

客户应验证系统是否满足自己的业务需求，且能否准确回答问题，以便将系统部署到生产环境中，以供客户端应用程序使用。 最终确定所有文档，并进行评审。 已完成将项目提交到负责操作的实体。 例如，此实体可能是 IT、客户数据科学团队或负责在生产中运行系统的客户代理。 

## <a name="artifacts"></a>项目
在此最后阶段中生成的主要项目是“客户项目退出报表”。 这是一份技术报表，其中包含所有有助于了解和操作系统的项目详细信息。 TDSP 提供[退出报表](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Exit%20Report.md)模板，可以按原样使用，也可以根据特定客户端需求进行自定义。 


## <a name="next-steps"></a>后续步骤

以下是 Team Data Science Process 生命周期中每个步骤的链接：

* [1.了解业务](lifecycle-business-understanding.md)
* [2.数据采集和理解](lifecycle-data.md)
* [3.建模](lifecycle-modeling.md)
* [4.部署](lifecycle-deployment.md)
* [5.客户验收](lifecycle-acceptance.md)

还提供了完整的、端到端的演练，演示**特定方案**处理过程中的所有步骤。 在[示例演练](walkthroughs.md)主题中，列出了相关步骤并链接了缩略图说明。 这些演练演示如何将云、本地工具和服务合并到工作流或管道中，以创建智能应用程序。 

有关在团队数据科学过程中执行使用 Azure 机器学习工作室的步骤的示例，请参阅[使用 Azure ML](http://aka.ms/datascienceprocess) 学习路径。