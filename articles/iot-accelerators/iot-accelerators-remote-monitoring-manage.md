---
title: 在远程监视解决方案中配置设备 - Azure | Microsoft Docs
description: 本教程介绍如何配置与远程监视解决方案加速器连接的设备。
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: e4236952bd41c4955e337813ff6d706263b8ef47
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "73890902"
---
# <a name="tutorial-configure-devices-connected-to-your-monitoring-solution"></a>教程：配置连接到监视解决方案的设备

在本教程中，你将使用远程监视解决方案加速器来配置和管理连接的 IoT 设备。 将新设备添加到解决方案加速器并配置设备。

Contoso 购置了新的机器以扩建其某处生产场地。 在等待交付新机器期间，你想要运行一次模拟，以测试解决方案的行为。 为了运行该模拟，你要将一个新的模拟引擎设备添加到远程监视解决方案加速器，并测试此模拟设备是否能够正确响应配置更新。 尽管本教程使用的是模拟设备，但设备开发人员可以在[连接到远程监视解决方案加速器的实际设备](iot-accelerators-connecting-devices.md)上执行直接方法。

本教程介绍以下操作：

>[!div class="checklist"]
> * 预配模拟设备。
> * 测试模拟设备。
> * 重新配置设备。
> * 组织设备。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

## <a name="add-a-simulated-device"></a>添加模拟设备

在解决方案中导航到“设备资源管理器”页，然后单击“+ 新建设备”：  

[![预配模拟设备](./media/iot-accelerators-remote-monitoring-manage/devicesprovision-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesprovision-expanded.png#lightbox)

在“新建设备”面板中选择“模拟”，将要预配的设备数保留为 **1**，选择“有故障的引擎”设备模型，然后选择“应用”以创建模拟设备：   

[![预配模拟引擎设备](./media/iot-accelerators-remote-monitoring-manage/devicesprovisionengine-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesprovisionengine-expanded.png#lightbox)

## <a name="test-the-simulated-device"></a>测试模拟设备

若要测试模拟引擎设备是否发送遥测数据并报告属性值，请在“设备资源管理器”页上的设备列表中选择该模拟设备。  有关引擎的实时信息显示在“设备详细信息”面板中： 

[![查看新的模拟引擎设备](./media/iot-accelerators-remote-monitoring-manage/devicesviewnew-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesviewnew-expanded.png#lightbox)

在“设备详细信息”中，验证新设备是否发送遥测数据。  若要查看设备发出的振动遥测数据流，请单击“振动”： 

[![选择要查看的遥测数据流](./media/iot-accelerators-remote-monitoring-manage/devicesvibration-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesvibration-expanded.png#lightbox)

“设备详细信息”面板显示有关设备的其他信息，例如标记值、设备支持的方法，以及设备报告的属性。 

若要查看详细的诊断，请在“设备详细信息”  面板中向下滚动以查看“诊断”  部分。

## <a name="reconfigure-a-device"></a>重新配置设备

若要测试是否可以更新引擎的配置属性，请在“设备资源管理器”页上的设备列表中选择该引擎。  接着单击“作业”，然后选择“属性”。   作业面板显示选定设备的可更改属性值：

[![重新配置设备](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigure-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigure-expanded.png#lightbox)

若要更新引擎的位置，请将作业名称设置为 **UpdateEngineLocation**，将经度设置为 **-122.15**，将位置设置为“工厂 2”，将纬度设置为 **47.62**，然后单击“应用”： 

[![更新设备属性值](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigurephysical-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigurephysical-expanded.png#lightbox)

若要跟踪作业的状态，请单击“查看作业状态”： 

[![更新设备属性值](./media/iot-accelerators-remote-monitoring-manage/locationjobstatus-inline.png)](./media/iot-accelerators-remote-monitoring-manage/locationjobstatus-expanded.png#lightbox)

作业完成后，导航到“仪表板”页。  引擎设备将显示在地图上的新位置中：

[![查看引擎位置](./media/iot-accelerators-remote-monitoring-manage/enginelocation-inline.png)](./media/iot-accelerators-remote-monitoring-manage/enginelocation-expanded.png#lightbox)

## <a name="organize-your-devices"></a>组织设备

为了方便操作员组织和管理设备，需要使用团队名称对设备进行标记。 Contoso 设立了两个不同的团队来从事现场服务活动：

* 智能车辆团队管理卡车和原型制作设备。
* 智能建筑团队管理冷却器、升降机和引擎。

若要显示所有设备，请导航到“设备资源管理器”页，选择“所有设备”筛选器：  

[![显示所有设备](./media/iot-accelerators-remote-monitoring-manage/devicesalldevices-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesalldevices-expanded.png#lightbox)

### <a name="add-tags"></a>添加标记

选择所有“卡车”和“原型制作”设备。   然后单击“作业”  。

在“作业”面板中，选择“标记”，将作业名称设置为 **AddConnectedVehicleTag**，添加名为 **FieldService** 且值为 **ConnectedVehicle** 的文本标记。  然后单击“应用”： 

[![将标记添加到原型制作和卡车设备](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag-expanded.png#lightbox)

在设备页上，选择所有“冷却器”、“升降机”和“引擎”设备。    然后单击“作业”  。

在“作业”面板中，选择“标记”，将作业名称设置为 **AddSmartBuildingTag**，添加名为 **FieldService** 且值为 **SmartBuilding** 的文本标记。  然后单击“应用”： 

[![将标记添加到冷却器、升降机和引擎设备](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag2-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag2-expanded.png#lightbox)

### <a name="create-filters"></a>创建筛选器

现在，可以使用标记值创建筛选器。 在“设备资源管理器”页上，单击“管理设备组”：  

[![管理设备组](./media/iot-accelerators-remote-monitoring-manage/devicesmanagefilters-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesmanagefilters-expanded.png#lightbox)

创建在条件中使用标记名称 **FieldService** 和值 **SmartBuilding** 的文本筛选器。 将该筛选器另存为“智能建筑”： 

[![创建智能构建筛选器](./media/iot-accelerators-remote-monitoring-manage/smartbuildingfilter-inline.png)](./media/iot-accelerators-remote-monitoring-manage/smartbuildingfilter-expanded.png#lightbox)

创建在条件中使用标记名称 **FieldService** 和值 **ConnectedVehicle** 的文本筛选器。 将该筛选器另存为“连接的车辆”。 

[![创建连接的车辆筛选器](./media/iot-accelerators-remote-monitoring-manage/connectedvehiclefilter-inline.png)](./media/iot-accelerators-remote-monitoring-manage/connectedvehiclefilter-expanded.png#lightbox)

现在，Contoso 操作员可以基于操作团队查询设备：

[![创建连接的车辆筛选器](./media/iot-accelerators-remote-monitoring-manage/filterinaction-inline.png)](./media/iot-accelerators-remote-monitoring-manage/filterinaction-expanded.png#lightbox)

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>后续步骤

本教程介绍了如何配置和管理与远程监视解决方案加速器连接的设备。 若要了解如何使用解决方案加速器对意外警报执行根本原因分析，请继续学习下一个教程。

> [!div class="nextstepaction"]
> [对警报执行根本原因分析](iot-accelerators-remote-monitoring-root-cause-analysis.md)
