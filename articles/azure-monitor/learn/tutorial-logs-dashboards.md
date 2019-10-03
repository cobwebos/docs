---
title: 创建和共享 Azure Log Analytics 数据的仪表板 | Microsoft Docs
description: 本教程有助于了解 Log Analytics 仪表板如何直观显示所有已保存的日志查询，从而提供单个可重用功能区来查看环境。
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: abb07f6c-b356-4f15-85f5-60e4415d0ba2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: c859fb9b8b3509e8369559a3a9a4d45cb4e34125
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2019
ms.locfileid: "68414173"
---
# <a name="create-and-share-dashboards-of-log-analytics-data"></a>创建和共享 Log Analytics 数据的仪表板

Log Analytics 仪表板可直观显示所有已保存的日志查询，使你能够在组织中查找、关联和共享 IT 操作数据。  本教程介绍如何创建日志查询，以用于支持 IT 操作支持团队要访问的共享仪表板。  学习如何：

> [!div class="checklist"]
> * 在 Azure 门户中创建共享仪表板
> * 直观显示性能日志查询 
> * 将日志查询添加到共享仪表板 
> * 在共享仪表板中自定义磁贴

要完成本教程中的示例，必须将现有虚拟机[连接到 Log Analytics 工作区](quick-collect-azurevm.md)。  
 
## <a name="sign-in-to-azure-portal"></a>登录到 Azure 门户
在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。 

## <a name="create-a-shared-dashboard"></a>创建共享仪表板
选择“仪表板”  可打开默认[仪表板](../../azure-portal/azure-portal-dashboards.md)。 你的仪表板看起来与下面的示例有所不同。

![Azure 门户仪表板](media/tutorial-logs-dashboards/log-analytics-portal-dashboard.png)

可将所有 Azure 资源中对 IT 而言最重要的操作数据整合到此处，包括来自 Azure Log Analytics 的遥测数据。  在开始直观显示日志查询之前，先来创建并共享一个仪表板。  然后，可以专注于将呈现为折线图的示例性能日志查询，并将其添加到该仪表板。  

若要创建仪表板，请选择当前仪表板名称旁边的“新建仪表板”  按钮。

![在 Azure 门户中新建仪表板](media/tutorial-logs-dashboards/log-analytics-create-dashboard-01.png)

此操作会创建全新的专用空仪表板，并进入自定义模式，可在此模式下命名仪表板以及添加或重新排列磁贴。 编辑仪表板的名称，针对本教程指定“示例仪表板”，然后选择“完成自定义”   。<br><br> ![保存自定义的 Azure 仪表板](media/tutorial-logs-dashboards/log-analytics-create-dashboard-02.png)

创建仪表板时，默认该仪表板是专用的，这意味着只有你才可以看到它。 若要让其对其他人可见，请使用与其他仪表板命令一起出现的“共享”  按钮。

![在 Azure 门户中共享新的仪表板](media/tutorial-logs-dashboards/log-analytics-share-dashboard.png) 

系统会要求选择可将仪表板发布到的订阅和资源组。 为方便起见，门户的发布体验会引导完成将仪表板放在名为**仪表板**的资源组中的模式。  验证所选订阅，然后单击“发布”  。  通过 [Azure 基于资源的访问控制](../../role-based-access-control/role-assignments-portal.md)来控制对仪表板中所显示信息的访问。   

## <a name="visualize-a-log-query"></a>直观显示日志查询
[Log Analytics](../log-query/get-started-portal.md) 是一个用于处理日志查询及其结果的专用门户。 这些功能包括在多行上编辑查询、选择性地执行代码、上下文相关 Intellisense 和智能分析。 在本教程中，你将使用 Log Analytics 创建图形形式的性能视图，将其保存以供将来查询，并将其固定到之前创建的共享仪表板。

在“Azure Monitor”菜单中选择“日志”  以打开 Log Analytics。 它将启动并显示一个新的空白查询。

![主页](media/tutorial-logs-dashboards/homepage.png)

输入以下查询来返回 Windows 和 Linux 计算机的处理器利用率记录，这些记录按“计算机”和 TimeGenerated 分组并显示在可视图表中。 单击“运行”  以运行该查询，并查看生成的图表。

```Kusto
Perf 
| where CounterName == "% Processor Time" and ObjectName == "Processor" and InstanceName == "_Total" 
| summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1hr), Computer 
| render timechart
```

选择页面顶部的“保存”按钮，以保存查询  。

![保存查询](media/tutorial-logs-dashboards/save-query.png)

在“保存查询”控制面板中提供名称（例如“Azure VM - 处理器利用率”）和类别（例如“仪表板”），然后单击“保存”     。  这样，便可创建可以使用和修改的常见查询的库。  最后，从页面右上角选择“固定到仪表板”按钮，然后再选择仪表板名称，以将其固定到之前创建的共享仪表板  。

将查询固定到仪表板之后，可发现该查询下方有一个通用标题和注释。

![Azure 仪表板示例](media/tutorial-logs-dashboards/log-analytics-modify-dashboard-01.png)

 应将其重命名为查看者易于理解的、富有意义的名称。  单击“编辑”按钮以自定义磁贴的标题和副标题，然后单击“更新”  。  随即会出现询问你是发布更改还是放弃的横幅。  单击“保存副本”  。  

![已完成的示例仪表板配置](media/tutorial-logs-dashboards/log-analytics-modify-dashboard-02.png)

## <a name="next-steps"></a>后续步骤
本教程介绍了如何在 Azure 门户中创建仪表板并向其中添加日志查询。  请继续学习下一教程以了解可根据日志查询结果实现的不同响应。  

> [!div class="nextstepaction"]
> [使用 Log Analytics 警报对事件做出响应](tutorial-response.md)
