---
title: 对警报执行根本原因分析 - Azure | Microsoft Docs
description: 在本教程中，你将了解如何使用 Azure 时序见解对警报执行根本原因分析。
author: Philmea
ms.author: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/20/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: c3cb940583677d813873c07fbfa679fdcc1dff59
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/23/2020
ms.locfileid: "77565462"
---
# <a name="tutorial-conduct-a-root-cause-analysis-on-an-alert"></a>教程：对警报执行根本原因分析

本教程介绍如何使用远程监视解决方案加速器诊断警报的根本原因。 你看到远程监视解决方案仪表板中触发了一个警报，然后使用 Azure 时序见解资源管理器来调查根本原因。

该教程使用两台模拟的运货卡车设备，它们发送位置、海拔、速度和货物温度遥测数据。 这些卡车由一家称作 Contoso 的组织进行管理，并已连接到远程监视解决方案加速器。 作为 Contoso 操作员，你需要了解为何其中一辆卡车 (delivery-truck-02) 记录了一次低温警报。

本教程介绍以下操作：

>[!div class="checklist"]
> * 在仪表板中筛选设备
> * 查看实时遥测数据
> * 在时序见解资源管理器中探索数据
> * 执行根本原因分析
> * 基于你的了解创建新规则

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

## <a name="choose-the-devices-to-display"></a>选择要显示的设备

若要选择想要在“仪表板”页上显示的联网设备，请使用筛选器。  如果只想显示“卡车”设备，请在筛选器下拉列表中选择内置的“卡车”筛选器：  

[![在仪表板上筛选卡车](./media/iot-accelerators-remote-monitoring-root-cause-analysis/filter-trucks-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/filter-trucks-expanded.png#lightbox)

应用某个筛选器时，只有与筛选条件匹配的设备才会显示在“仪表板”的地图上和遥测数据面板中。  你可以看到有两辆卡车连接到了解决方案加速器，包括 truck-02  ：

## <a name="view-real-time-telemetry"></a>查看实时遥测数据

解决方案加速器会在“仪表板”页上的图表中绘制实时遥测数据。  默认情况下，图表将显示随时间变化的海拔遥测数据。

[![卡车海拔遥测数据绘图](./media/iot-accelerators-remote-monitoring-root-cause-analysis/trucks-moving-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/trucks-moving-expanded.png#lightbox)

若要查看卡车的温度遥测数据，请单击“遥测面板”中的“温度”   。 可以查看两辆卡车的温度在过去 15 分钟内是如何变化的。 还可以在警报窗格中看到为 delivery-truck-02 触发了一次低温警报。

[![含低温警报的 RM 仪表板](./media/iot-accelerators-remote-monitoring-root-cause-analysis/low-temp-alert-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/low-temp-alert-expanded.png#lightbox)

## <a name="explore-the-data"></a>浏览数据

要确定低温警报的原因，请在时序见解资源管理器中打开运货卡车遥测数据。 单击仪表板上的任意“在时序见解中探索”  链接：

[![突出显示 TSI 链接的 RM 仪表板](./media/iot-accelerators-remote-monitoring-root-cause-analysis/explore-tsi-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/explore-tsi-expanded.png#lightbox)

当资源管理器启动时，将看到列出了所有设备：

[![TSI 资源管理器初始视图](./media/iot-accelerators-remote-monitoring-root-cause-analysis/initial-tsi-view-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/initial-tsi-view-expanded.png#lightbox)

在筛选器框中通过键入“delivery-truck”  来筛选设备，然后在左侧面板中选择“温度”  作为“度量值”  ：

[![TSI 资源管理器卡车温度](./media/iot-accelerators-remote-monitoring-root-cause-analysis/filter-tsi-temp-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/filter-tsi-temp-expanded.png#lightbox)

会看到在远程监视仪表板中看到的相同视图。 另外，现在可以放大得更近以查看警报触发的时间范围：

[![TSI 资源管理器缩放](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-zoom-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-zoom-expanded.png#lightbox)

你还可以添加来自卡车的其他遥测数据流。 单击左上角的“添加”  按钮。 此时出现新的窗格：

[![显示新窗格的 TSI 资源管理器](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-pane-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-pane-expanded.png#lightbox)

在新窗格中，将新标签的名称更改为“设备”  以便其匹配先前的名称。 选择“海拔”  作为“度量值”  并选择“iothub-connection-device-id”  作为“拆分依据”  值，以将海拔遥测数据添加到视图中：

[![具有温度和海拔数据的 TSI 资源管理器](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-altitude-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-altitude-expanded.png#lightbox)

## <a name="diagnose-the-alert"></a>诊断警报

在当前视图中查看数据流时，可以看到两个卡车的海拔剖面图并不相同。 此外，delivery-truck-02 的温度降低发生在卡车到达高海拔时  。 你对此发现很震惊，因为两辆卡车计划采取的是相同的路线。

为了确认卡车走了不同的行程路线这一怀疑，使用“添加”  按钮向侧边面板添加另一个窗格。 在新窗格中，将新标签的名称更改为“设备”  以便其匹配先前的名称。 选择“经度”  作为“度量值”  并选择“iothub-connection-device-id”  作为“拆分依据”  值，以将经度遥测数据添加到视图中。 通过查看经度  数据流之间的区别，可以知道卡车确实采取了不同的路线：

[![具有温度、海拔和经度数据的 TSI 资源管理器](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-longitude-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-longitude-expanded.png#lightbox)

## <a name="create-a-new-rule"></a>创建新规则

虽然卡车路线通常都是事先经过优化选取的，但你意识到交通模式、天气和其他不可预测事件可能导致延误，并且卡车司机会根据个人的最佳判断来作出最终的路线决策。 然而由于车辆内资产的温度非常重要，你应该在远程监视解决方案中创建一条额外规则。 此规则是为了确保在 1 分钟间隔内的平均海拔超过 350 英尺时收到警告：

[![远程监视规则选项卡设置海拔规则](./media/iot-accelerators-remote-monitoring-root-cause-analysis/new-rule-altitude-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/new-rule-altitude-expanded.png#lightbox)

若要了解如何创建和编辑规则，请参阅之前教程中的[检测设备问题](iot-accelerators-remote-monitoring-automate.md)。

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>后续步骤

本教程介绍了如何使用时序见解资源管理器与远程监视解决方案加速器来诊断警报的根本原因。 若要了解如何使用解决方案加速器识别和解决联网设备的问题，请继续学习下一篇教程。

> [!div class="nextstepaction"]
> [使用设备警报识别和解决与监视解决方案连接的设备问题](iot-accelerators-remote-monitoring-maintain.md)
