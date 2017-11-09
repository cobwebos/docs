---
title: "Azure Team Data Science Process 生命周期部署阶段 | Microsoft Docs"
description: "你的数据科学项目的部署阶段的目标、任务和可交付结果。"
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
ms.openlocfilehash: b49b3ab696c22928c5f5a4566e059345fd810588
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="deployment"></a>部署

本主题概述了与 Team Data Science Process 的**部署**相关联的目标、任务和可交付结果。 此过程提供了可用于构建数据科学项目的建议生命周期。 该生命周期概述了项目通常执行的主要阶段（通常以迭代方式进行）：

* **了解业务**
* **数据采集和理解**
* **建模**
* **部署**
* **客户验收**

以下是 **Team Data Science Process 生命周期**的可视化表示形式 。 

![TDSP-Lifecycle2](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>目标
* 将包含数据管道的模型部署到生产或类似生产的环境中，以便进行最终用户验收。 

## <a name="how-to-do-it"></a>如何执行
在此阶段中解决的主要任务：

* **操作模型**：将模型和管道部署到生产中或生产类似的环境中供应用程序操作。

### <a name="41-operationalize-a-model"></a>4.1 使模型可操作化
如果拥有一组运行良好的模型，也可以使这些模型可操作化，以供其他应用程序使用。 根据具体的业务需求，实时或基于批处理地作出决策。 通过使用开放 API 接口公开模型以对其进行部署。 此接口允许模型通过联机网站、电子表格、仪表板或业务线和后端应用程序等各种应用程序轻松访问。 有关使用 Azure 机器学习 Web 服务使模型可操作化的示例，请参阅[部署 Azure 机器学习 Web 服务](../studio/publish-a-machine-learning-web-service.md)。 根据最佳做法，还需要将遥测和监视功能集成到部署的生产模型和数据管道中。 此做法有助于后续进行系统状态报告和故障排除。  

## <a name="artifacts"></a>项目
* 系统运行状况和关键指标的状态仪表板。
* 具有部署详细信息的最终建模报表。
* 最终解决方案体系结构文档。


## <a name="next-steps"></a>后续步骤

以下是 Team Data Science Process 生命周期中每个步骤的链接：

* [1.了解业务](lifecycle-business-understanding.md)
* [2.数据采集和理解](lifecycle-data.md)
* [3.建模](lifecycle-modeling.md)
* [4.部署](lifecycle-deployment.md)
* [5.客户验收](lifecycle-acceptance.md)

还提供了完整的、端到端的演练，演示**特定方案**处理过程中的所有步骤。 在[示例演练](walkthroughs.md)主题中，列出了相关步骤并链接了缩略图说明。 这些演练演示如何将云、本地工具和服务合并到工作流或管道中，以创建智能应用程序。 

有关在团队数据科学过程中执行使用 Azure 机器学习工作室的步骤的示例，请参阅[使用 Azure ML](http://aka.ms/datascienceprocess) 学习路径。