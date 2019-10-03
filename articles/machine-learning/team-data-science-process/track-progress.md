---
title: 跟踪数据科学项目的进度 - Team Data Science Process
description: 数据科学组管理员、团队主管和项目主管如何跟踪数据科学项目的进度。
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/28/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 7745a53084f4477f7b736ea9d130ffd3eed771f4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60252572"
---
# <a name="tracking-the-progress-of-data-science-projects"></a>跟踪数据科学项目的进度

数据科学组管理员、团队主管和项目主管需要跟踪项目的进度、已完成了哪些工作及是由谁完成的、待办事项列表上还剩下哪些工作。 

## <a name="azure-devops-dashboards"></a>Azure DevOps 仪表板
如果使用的是 Azure DevOps，则可以生成仪表板来跟踪与给定敏捷项目关联的活动和工作项。 

有关如何在 Azure DevOps 中创建和自定义仪表板与小组件的详细信息，请参阅以下几组说明：

- [添加和管理仪表板](https://docs.microsoft.com/azure/devops/report/dashboards/dashboards)
- [将小组件添加到仪表板](https://docs.microsoft.com/azure/devops/report/dashboards/add-widget-to-dashboard)。

## <a name="example-dashboard"></a>示例仪表板

下面是一个简单的示例仪表板，用于跟踪对关联存储库的敏捷数据科学项目，以及提交数的冲刺活动。 **左上角**面板显示：

- 当前冲刺的倒计时， 
- 在过去 7 天内的每个存储库提交数
- 特定用户的工作项。 

剩余面板显示项目的累积流图 (CFD)、燃尽和燃起图：

- **左下角**：处于给定状态的工作的数量 CFD，灰色表示“已批准”，蓝色表示“已提交”，绿色表示“已完成”。
- **右上角**：燃尽图有待与剩余时间完成的工作。
- **右下角**：燃起图的工作已完成工作的总量。

![仪表板](./media/track-progress/dashboard.png)

有关如何生成这些图表的说明，请参阅[仪表板](https://docs.microsoft.com/azure/devops/report/dashboards/)中的快速入门和教程。
 
## <a name="next-steps"></a>后续步骤

我们还提供了相应的演练，用于演示**具体方案**的操作过程的所有步骤。 [示例演练](walkthroughs.md)一文列出了相关步骤并以缩略图说明的形式提供了链接。 这些演练演示如何将云、本地工具和服务合并到工作流或管道中，以创建智能应用程序。 
