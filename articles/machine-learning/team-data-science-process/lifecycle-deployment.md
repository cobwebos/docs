---
title: Team Data Science Process 生命周期部署阶段 - Azure | Microsoft Docs
description: 你的数据科学项目部署阶段的目标、任务和可交付结果
services: machine-learning
documentationcenter: ''
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: bradsev
ms.openlocfilehash: 5cb6361ed674ffaaf776adafd6f3ff87272c73eb
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2018
---
# <a name="deployment"></a>部署

本文概述了与 Team Data Science Process (TDSP) 的部署相关联的目标、任务和可交付结果。 此过程提供可用于构建数据科学项目的建议生命周期。 该生命周期概述了项目通常执行的主要阶段（通常以迭代方式进行）：

   1. **了解业务**
   2. **数据采集和理解**
   3. **建模**
   4. **部署**
   5. **客户验收**

此处直观地展示了 TDSP 生命周期： 

![TDSP 生命周期](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>目标
将包含数据管道的模型部署到生产或类似于生产的环境中，以便进行最终用户验收。 

## <a name="how-to-do-it"></a>如何执行
在此阶段中解决的主要任务：

**操作模型**：将模型和管道部署到生产中或生产类似的环境中供应用程序操作。

### <a name="operationalize-a-model"></a>操作模型
如果拥有一组运行良好的模型，则可操作这些模型，以供其他应用程序使用。 根据具体的业务需求，实时作出预测或基于批处理作出预测。 若要部署模型，请使用开放 API 接口公开模型。 通过该接口，各种应用程序可轻松使用模型，例如：

   * 联机网站
   * 电子表格 
   * 仪表板
   * 业务线应用程序 
   * 后端应用程序 

有关使用 Azure 机器学习 Web 服务使模型可操作化的示例，请参阅[部署 Azure 机器学习 Web 服务](../studio/publish-a-machine-learning-web-service.md)。 将遥测和监视功能构建到部署的生产模型和数据管道中是一种最佳做法。 此做法有助于后续进行系统状态报告和故障排除。  

## <a name="artifacts"></a>项目

* 显示系统运行状况和关键指标的状态仪表板
* 具有部署详细信息的最终建模报表
* 最终解决方案体系结构文档


## <a name="next-steps"></a>后续步骤

以下是 TDSP 生命周期中每个步骤的链接：

   1. [了解业务](lifecycle-business-understanding.md)
   2. [数据采集和理解](lifecycle-data.md)
   3. [建模](lifecycle-modeling.md)
   4. [部署](lifecycle-deployment.md)
   5. [客户验收](lifecycle-acceptance.md)

我们还提供了完整的演练，演示特定方案过程中的所有步骤。 [示例演练](walkthroughs.md)一文提供了包含链接和缩略图描述的方案列表。 该演练演示如何将云、本地工具以及服务结合到一个工作流或管道中，以创建智能应用程序。 

有关如何在使用 Azure 机器学习工作室的 TDSP 中执行步骤的示例，请参阅[通过 Azure 机器学习使用 TDSP](http://aka.ms/datascienceprocess)。
