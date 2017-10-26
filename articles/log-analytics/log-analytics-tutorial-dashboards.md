---
title: "创建和共享 Azure Log Analytics 数据的仪表板 | Microsoft Docs"
description: "本教程可帮助你了解 Log Analytics 仪表板如何直观显示所有已保存的日志搜索，从而提供单个可重用功能区来查看环境。"
services: log-analytics
documentationcenter: log-analytics
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: abb07f6c-b356-4f15-85f5-60e4415d0ba2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/14/2017
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 272945134b534a5ded794379ce5e96b0902a4227
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2017
---
# <a name="create-and-share-dashboards-of-log-analytics-data"></a>创建和共享 Log Analytics 数据的仪表板

Log Analytics 仪表板可直观显示所有已保存的日志搜索，使你能够在组织中查找、关联和共享 IT 操作数据。  本教程介绍如何创建日志搜索，用于支持 IT 操作支持团队将要访问的共享仪表板。  学习如何：

> [!div class="checklist"]
> * 在 Azure 门户中创建共享仪表板
> * 可视化性能日志搜索 
> * 将日志搜索添加到共享仪表板 
> * 在共享仪表板中自定义磁贴

要完成本教程中的示例，必须将现有虚拟机[连接到 Log Analytics 工作区](log-analytics-quick-collect-azurevm.md)。  
 
## <a name="log-in-to-azure-portal"></a>登录到 Azure 门户
登录 Azure 门户 ([https://portal.azure.com](https://portal.azure.com))。 

## <a name="create-a-shared-dashboard"></a>创建共享仪表板

登录 Microsoft Azure 门户后，最先看到的是[仪表板](../azure-portal/azure-portal-dashboards.md)。<br> ![Azure 门户仪表板](media/log-analytics-tutorial-dashboards/log-analytics-portal-dashboard.png)

可将所有 Azure 资源中对 IT 而言最重要的操作数据整合到此处，包括来自 Azure Log Analytics 的遥测数据。  在开始执行可视化日志搜索之前，先创建并共享一个仪表板。  这样就可以避免在进行示例性能日志搜索（呈现为折线图）并将其添加到仪表板之前执行此操作。  

若要创建仪表板，请选择当前仪表板名称旁边的“新建仪表板”按钮。<br> ![在 Azure 门户中新建仪表板](media/log-analytics-tutorial-dashboards/log-analytics-create-dashboard-01.png)

此操作会创建全新的专用空仪表板，并进入自定义模式，可在此模式下命名仪表板以及添加或重新排列磁贴。 编辑仪表板名称，针对此教程指定“示例仪表板”，然后选择“完成自定义”。<br><br> ![保存自定义的 Azure 仪表板](media/log-analytics-tutorial-dashboards/log-analytics-create-dashboard-02.png)

创建仪表板时，默认该仪表板是专用的，这意味着只有你才可以看到它。 若要让其对其他人可见，请使用与其他仪表板命令一起出现的“共享”按钮。<br> ![在 Azure 门户中共享新的仪表板](media/log-analytics-tutorial-dashboards/log-analytics-share-dashboard.png) 

系统会要求选择可将仪表板发布到的订阅和资源组。 为方便起见，门户的发布体验会引导完成将仪表板放在名为**仪表板**的资源组中的模式。  验证所选订阅，然后单击“发布”。  通过 [Azure 基于资源的访问控制](../active-directory/role-based-access-control-configure.md)来控制对仪表板中所显示信息的访问。   

## <a name="visualize-a-log-search"></a>可视化日志搜索

可从 Azure 门户中的“日志搜索”门户创建针对单行的基本查询。 可在不启动外部门户的情况下使用日志搜索门户，并可将其用于执行日志搜索的多种函数，例如创建警报规则、创建计算机组和导出查询结果。 

[高级分析门户](https://docs.loganalytics.io/docs/Learn/Getting-Started/Getting-started-with-the-Analytics-portal)是一个专用门户，提供日志搜索门户中不可用的高级功能。 这些功能包括在多行上编辑查询、选择性地执行代码、上下文相关 Intellisense 和智能分析。 在高级分析门户中，创建图表形式的性能视图，将其保存以供将来搜索，然后将其固定到之前创建的共享仪表板。   

通过日志搜索中的链接可启动高级分析门户。<br> ![启动高级分析门户](media/log-analytics-tutorial-dashboards/log-analytics-advancedportal-01.png)

在分析门户中，输入以下查询，仅返回 Windows 和 Linux 计算机的处理器利用率记录，这些记录按计算机和生成时间分组并显示在可视化图表中：

```
Perf | where CounterName == "% Processor Time" and ObjectName == "Processor" and InstanceName == "_Total" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1m), Computer | render timechart
```

选择右上角的“保存查询”按钮，保存查询。<br> ![从高级分析门户保存查询](media/log-analytics-tutorial-dashboards/log-analytics-advancedportal-02.png)<br><br> 在“保存查询”控制面板中提供一个名称（例如“Azure VM - 处理器利用率”），然后单击“保存”。  这样一来，即可创建一个常见查询库，以便进行搜索或修改，而无需完全重新写入。  最后，从页面右上角中间位置选择“将图表固定到 Azure 仪表板”按钮，将其固定到之前创建的共享仪表板。  

将查询固定到仪表板之后，可发现该查询下方有一个通用标题和注释。<br> ![Azure 仪表板示例](media/log-analytics-tutorial-dashboards/log-analytics-modify-dashboard-01.png)<br><br>  应将其重命名为查看者易于理解的、富有意义的名称。  右键单击该磁贴，然后选择“编辑磁贴”。  完成自定义该磁贴的标题和副标题后，单击“更新”。  随即会出现询问你是发布更改还是放弃的横幅。  单击“发布更改”，然后关闭“编辑磁贴”控制窗格。  

![已完成的示例仪表板配置](media/log-analytics-tutorial-dashboards/log-analytics-modify-dashboard-02.png)

## <a name="next-steps"></a>后续步骤
本教程介绍了如何在 Azure 门户中创建仪表板，并在仪表板中添加日志搜索。  继续学习下一教程，了解可基于日志搜索结果实现的不同响应。  

> [!div class="nextstepaction"]
> [使用 Log Analytics 警报对事件做出响应](log-analytics-tutorial-response.md)