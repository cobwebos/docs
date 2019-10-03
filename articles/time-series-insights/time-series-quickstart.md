---
title: 快速入门：Azure 时序见解资源管理器 | Microsoft Docs
description: 此快速入门说明如何在 Web 浏览器中开始使用 Azure 时序见解资源管理器，使大量 IoT 数据可视化。 在演示环境中浏览关键功能。
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 09/23/2019
ms.openlocfilehash: b5fb0c4de82d5ff5f5290127ec529787cabad64a
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2019
ms.locfileid: "71258356"
---
# <a name="quickstart-explore-azure-time-series-insights"></a>快速入门：探索 Azure 时序见解

此 Azure 时序见解资源管理器快速入门帮助你在免费的演示环境中完成时序见解的入门。 本快速入门介绍如何使用 Web 浏览器将大量 IoT 数据可视化，并浏览当前正式发布的重要功能。

Azure 时序见解是一种完全托管的分析、存储和可视化效果服务，可简化如何同时浏览和分析数十亿个 IoT 事件的过程。 它提供数据的全局视图，从而可以快速验证 IoT 解决方案并避免任务关键型设备出现代价高昂的故障时间。 通过 Azure 时序见解，可以近乎实时地发现隐藏的趋势、发现异常情况并对其进行根本原因分析。

若要更加灵活地使用 Azure 时序见解，可以通过其功能强大的 [REST API](./time-series-insights-update-tsq.md) 和[客户端 SDK](./tutorial-create-tsi-sample-spa.md) 将其添加到预先存在的应用程序中。 通过 API，可以在所选择的客户端应用程序中存储、查询和使用时序数据。 还可使用客户端 SDK 将 UI 组件添加到现有应用程序中。

此时序见解资源管理器快速入门以导览的形式介绍正式版中的功能。

> [!IMPORTANT]
> 如果还没有创建 Azure 帐户，可以创建[免费 Azure 帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

## <a name="prepare-the-demo-environment"></a>准备演示环境

1. 在浏览器中，转到[正式发布演示](https://insights.timeseries.azure.com/demo)。

1. 根据系统的提示使用 Azure 帐户凭据登录到时序见解资源管理器。

1. 此时会显示时序见解快速教程页面。 选择“下一步”  开始快速教程。

   [![选择“下一步”](media/quickstart/quickstart1.png)](media/quickstart/quickstart1.png#lightbox)

## <a name="explore-the-demo-environment"></a>浏览演示环境

1. 显示“时间选择面板”  。 使用该面板选择期限，进行可视化。

   [![时间选项面板](media/quickstart/quickstart2.png)](media/quickstart/quickstart2.png#lightbox)

1. 选择一个期限，将其拖至区域中。 然后选择“搜索”。 

   [![选择期限](media/quickstart/quickstart3.png)](media/quickstart/quickstart3.png#lightbox)

   时序见解显示指定期限的图表可视化效果。 可以在折线图中执行各种操作。 例如，可以筛选、固定、排序和堆叠。

   要返回到“时间选择面板”，请选择如下所示的向下键  ：

   [![图表](media/quickstart/quickstart4.png)](media/quickstart/quickstart4.png#lightbox)

1. 在“术语面板”中，选择“添加”，添加新的搜索词   。

   [![添加项](media/quickstart/quickstart5.png)](media/quickstart/quickstart5.png#lightbox)

1. 在此图表中，可以选择一个区域，右键单击该区域，然后选择“浏览事件”  。

   [![浏览事件](media/quickstart/quickstart6.png)](media/quickstart/quickstart6.png#lightbox)

   正在探索的区域将显示原始数据的网格。

   [![网格视图](media/quickstart/quickstart7.png)](media/quickstart/quickstart7.png#lightbox)

## <a name="select-and-filter-data"></a>选择和筛选数据

1. 编辑术语以更改图表中的值。 添加另一个术语来交叉关联不同类型的值。

   [![添加术语](media/quickstart/quickstart8.png)](media/quickstart/quickstart8.png#lightbox)

1. 在“筛选器系列”对话框中输入筛选器术语，进行临时系列筛选  。 对于本快速入门，输入“Station5”交叉关联此站的温度和压力  。

   [![筛选器系列](media/quickstart/quickstart9.png)](media/quickstart/quickstart9.png#lightbox)

完成快速入门后，可尝试用示例数据集创建不同的可视化效果。

## <a name="clean-up-resources"></a>清理资源

现在你已完成本教程，请清除已创建的资源：

1. 从 [Azure 门户](https://portal.azure.com)的左侧菜单中，选择“所有资源”  ，找到“Azure 时序见解”资源组。
1. 通过选择“删除”  来删除整个资源组（以及其中包含的所有资源），或者单独删除每个资源。

## <a name="next-steps"></a>后续步骤

已准备好创建自己的时序见解环境：
> [!div class="nextstepaction"]
> [计划时序见解环境](time-series-insights-environment-planning.md)
