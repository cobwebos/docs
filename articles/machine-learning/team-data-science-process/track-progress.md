---
title: 跟踪 Team Data Science Process 项目的进度
description: 数据科学组管理员、团队主管和项目主管如何跟踪数据科学项目的进度。
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 8cf1e5a4d97b882e7a8d0c81041bbcde709760d1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "75864190"
---
# <a name="track-the-progress-of-data-science-projects"></a>跟踪数据科学项目的进度

数据科学组管理员、团队主管和项目主管可以跟踪其项目的进度。  经理想要了解已完成的工作、工作人员的工作，以及剩余的工作。   管理期望是成功的重要元素。

## <a name="azure-devops-dashboards"></a>Azure DevOps 仪表板

如果使用的是 Azure DevOps，则可以生成仪表板来跟踪与给定敏捷项目关联的活动和工作项。 有关仪表板的详细信息，请参阅[仪表板、报表和小组件](/azure/devops/report/dashboards/)。

有关如何在 Azure DevOps 中创建和自定义仪表板与小组件的说明，请参阅以下快速入门：

- [添加和管理仪表板](/azure/devops/report/dashboards/dashboards)
- [将小组件添加到仪表板](/azure/devops/report/dashboards/add-widget-to-dashboard)

## <a name="example-dashboard"></a>示例仪表板

下面是一个简单的示例仪表板，用于跟踪敏捷数据科学项目的冲刺活动，包括目标为关联的存储库的提交的数目。 

- “倒计时”磁贴显示当前冲刺 (sprint) 中剩余的天数。**** 

- 这两个**代码磁贴**显示过去七天内两个项目存储库中的提交数。 

- **TDSP 客户项目的工作项**显示所有工作项的查询结果及其状态。 

- **累积流关系图** (CFD) 显示已关闭的和活动的工作项的数目。

- **燃尽图**显示仍需在冲刺 (sprint) 的剩余时间内完成的工作。

- **燃耗图表**显示已完成工作与冲刺 (sprint) 中的总工时数的比较。

![仪表板](./media/track-progress/dashboard.png)

## <a name="next-steps"></a>后续步骤

[执行团队数据科学过程的演练](walkthroughs.md)列出了演示所有过程步骤的演练。 这些链接的方案说明了如何将云和本地资源管理到智能应用程序中。 
