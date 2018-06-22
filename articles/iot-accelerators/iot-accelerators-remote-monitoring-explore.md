---
title: 远程监视解决方案入门 - Azure | Microsoft Docs
description: 本教程使用模拟方案来介绍远程监视解决方案加速器。 这些方案是首次部署远程监视解决方案加速器时创建的。
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 05/01/2018
ms.topic: conceptual
ms.openlocfilehash: e354e110df8d168e153c50de6f7aad08dd416464
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34627801"
---
# <a name="explore-the-capabilities-of-the-remote-monitoring-solution-accelerator"></a>探索远程监视解决方案加速器的功能

本教程介绍远程监视解决方案的重要功能。 这了介绍这些功能，本教程将会针对一家名为 Contoso 的公司使用模拟 IoT 应用程序，以此展示常见的客户方案。

本教程可帮助我们了解远程监视解决方案现成提供的典型 IoT 方案。

本教程介绍如何执行下列操作：

>[!div class="checklist"]
> * 在仪表板上可视化和筛选设备
> * 响应警报
> * 更新设备中的固件
> * 组织资产
> * 停止和启动模拟设备

以下视频展示了远程监视解决方案的演练：

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Part-28-An-introduction-to-Azure-IoT-through-the-new-Remote-Monitoring-Preconfigured-Solution/Player]

## <a name="prerequisites"></a>先决条件

若要完成本教程，需在 Azure 订阅中部署远程监视解决方案的实例。

如果尚未部署远程监视解决方案，应完成[部署远程监视解决方案加速器](iot-accelerators-remote-monitoring-deploy.md)教程。

## <a name="the-contoso-sample-iot-deployment"></a>Contoso 示例 IoT 部署

可以使用 Contoso 示例 IoT 部署来了解远程监视解决方案现成提供的基本方案。 这些方案基于真实的 IoT 部署。 我们很可能会选择自定义远程监视解决方案来满足自己的具体要求，但 Contoso 示例可帮助我们了解基础知识。

> [!NOTE]
> 如果使用 CLI 部署了解决方案加速器，则文件 `deployment-{your deployment name}-output.json` 会包含有关部署的信息，例如用于访问已部署示例的 URL。

Contoso 示例将会预配一组要操作的模拟设备和规则。 了解基本方案后，可在[使用远程监视解决方案执行高级设备监视](iot-accelerators-remote-monitoring-monitor.md)中继续探索其他解决方案功能。

Contoso 公司在不同的环境中管理各种资源。 Contoso 打算使用基于云的 IoT 应用程序的强大功能，通过一个中心应用程序远程监视和管理多个资产。 以下部分提供了 Contoso 示例的初始配置摘要：

> [!NOTE]
> Contoso 示例仅演示了预配模拟设备和创建规则的一种方式。 其他预配选项包括创建自己的自定义设备。 若要详细了解如何创建自己的设备和规则，请参阅[管理和配置设备](iot-accelerators-remote-monitoring-manage.md)以及[使用基于阈值的规则检测问题](iot-accelerators-remote-monitoring-automate.md)。

### <a name="contoso-devices"></a>Contoso 设备

Contoso 使用不同类型的智能设备。 这些设备在公司中发挥不同的作用，可发送各种遥测流。 此外，每种设备类型具有不同的设备属性和支持的方法。

下表显示了预配的设备类型的摘要：

| 设备类型        | 遥测                                  | 属性                                  | 标记                    | 方法                                                                                      |
| ------------------ | ------------------------------------------ | ------------------------------------------- | ----------------------- | -------------------------------------------------------------------------------------------- |
| 冷却器            | 温度、湿度、压力            | 类型、固件版本、型号               | 位置、楼层、园区 | 重新启动、固件更新、紧急阀门释放、增大压力                          |
| 原型制作设备 | 温度、压力、地理位置        | 类型、固件版本、型号               | 位置、模式          | 重新启动、固件更新、转移设备、停止设备、温度释放、温度提高 |
| 引擎             | 燃油水平、冷却剂传感器、振动 | 类型、固件版本、型号               | 位置、楼层、园区 | 固件更新、排空油箱、加满油箱                                              |
| 卡车              | 地理位置、速度、货物温度     | 类型、固件版本、型号               | 位置、负载          | 货物温度低、提高货物温度、固件更新                         |
| 升降机           | 楼层、振动、温度              | 类型、固件版本、型号、地理位置 | 位置、园区        | 停止升降机、启动升降机、固件更新                                               |

> [!NOTE]
> Contoso 演示示例为每种类型预配两个设备。 对于每种类型，一个设备可在 Contoso 定义为常态的边界内正常运行，另一个设备存在某种类型的故障。 下一部分介绍 Contoso 针对设备定义的规则。 这些规则定义正确行为的边界。

### <a name="contoso-rules"></a>Contoso 规则

Contoso 的操作员知道哪些阈值确定了设备是否正常工作。 例如，如果报告的压力大于 250 PSI，则表示冷却器的工作不正常。 下表显示了 Contoso 针对每种设备类型定义的基于阈值的规则：

| 规则名称 | 说明 | 阈值 | Severity | 受影响的设备 |
| --------- | ----------- | --------- | -------- | ---------------- |
| 冷却器压力过高 | 如果冷却器超过正常压力级别，则发出警报   |P>250 psi       | 严重 | 冷却器            |
| 原型制作设备温度过高  | 如果原型制作设备超过正常温度级别，则发出警报  |T>80&deg; F |严重 | 原型制作设备 |
| 引擎油箱已空  | 如果引擎油箱已空，则发出警报                     | F<5 加仑 | 信息     | 引擎             |
| 超过正常货物温度 | 如果卡车的货物温度超过正常值，则发出警报                 | T<45&deg; F |警告  | 卡车              |
| 升降机振动已停止      | 如果升降机已完全停止（根据振动级别确定），则发出警报                     | V<0.1 mm |警告  | 升降机           |

### <a name="operate-the-contoso-sample-deployment"></a>操作 Contoso 示例部署

现已了解 Contoso 示例中的初始设置。 以下部分介绍 Contoso 示例中的三个方案，演示操作员可以如何使用解决方案加速器。

## <a name="respond-to-a-pressure-alert"></a>响应压力警报

此方案演示如何识别冷却器设备触发的警报并对其做出响应。 冷却器位于 Redmond 的 43 栋 2 楼。

操作员在仪表板中看到一条与冷却器压力相关的警报。 可以平移和缩放地图来查看更多详细信息。

1. 在“仪表板”页上的“警报”网格中，可以看到“冷却器压力过高”警报。 地图上也突出显示了该冷却器：

    ![仪表板在地图中显示压力警报和设备](./media/iot-accelerators-remote-monitoring-explore/dashboardalarm.png)

1. 若要导航到“维护”页，请在导航菜单上选择“维护”。 在“维护”页上，可以查看触发冷却器压力警报的规则的详细信息。

1. 警报列表显示警报的触发次数、警报确认，以及已打开和关闭的警报：

    ![“维护”页显示已触发的警报列表](./media/iot-accelerators-remote-monitoring-explore/maintenancealarmlist.png)

1. 列表中的最后一条警报是最近发出的警报。 单击“冷却器压力过高”警报可查看关联的设备和遥测数据。 遥测数据显示了冷却器的压力峰值：

    ![“维护”页显示所选警报的遥测数据](./media/iot-accelerators-remote-monitoring-explore/maintenancetelemetry.png)

现已确定触发警报的问题和关联的设备。 操作员要执行的后续步骤是确认警报并解决问题。

1. 若要指出你目前正在处理警报，请将“警报状态”更改为“已确认”：

    ![选择并确认警报](./media/iot-accelerators-remote-monitoring-explore/maintenanceacknowledge.png)

1. 若要处理冷却器，请将它选中，然后选择“作业”。 选择“运行方法”，然后选择“EmergencyValveRelease”，添加名为 **ChillerPressureRelease** 的作业，再选择“应用”。 这些设置会创建一个可立即执行的作业：

    ![选择设备并计划操作](./media/iot-accelerators-remote-monitoring-explore/maintenanceschedule.png)

1. 若要查看作业状态，请返回“维护”页，并在“作业”视图中查看作业列表。 可以看到，该作业已运行，以释放冷却器的阀门压力：

    ![“作业”视图中的作业状态](./media/iot-accelerators-remote-monitoring-explore/maintenancerunningjob.png)

最后，确认来自冷却器的遥测值已恢复正常。

1. 若要查看警报网格，请导航到“仪表板”页。

1. 若要查看设备遥测数据，请在地图中选择原始警报所对应的设备，并确认该设备已恢复正常。

1. 若要关闭事件，请导航到“维护”页，选择警报，将状态设置为“已关闭”：

    ![选择并关闭警报](./media/iot-accelerators-remote-monitoring-explore/maintenanceclose.png)

## <a name="update-device-firmware"></a>更新设备固件

Contoso 正在现场测试某个新型设备。 在测试周期中，需确保可正常进行设备固件更新。 以下步骤说明如何使用远程监视解决方案更新多个设备上的固件。

若要执行所需的设备管理任务，请使用“设备”页。 首先，筛选所有原型制作设备：

1. 导航到“设备”页。 在“筛选器”下拉列表中选择“原型制作设备”筛选器：

    ![在“设备”页上使用筛选器](./media/iot-accelerators-remote-monitoring-explore/devicesprototypingfilter.png)

    > [!TIP]
    > 单击“管理”可以管理可用的筛选器。

1. 选择一个原型制作设备：

    ![在“设备”页上选择设备](./media/iot-accelerators-remote-monitoring-explore/devicesselect.png)

1. 单击“作业”按钮，选择“运行方法”，然后选择“固件更新”。 输入“作业名称”、“固件版本”和“固件 URI”的值。 选择“应用”，将作业计划为立即运行：

    ![在设备上计划固件更新](./media/iot-accelerators-remote-monitoring-explore/devicesschedulefirmware.png)

    > [!NOTE]
    > 在模拟设备中，可以使用所需的任何 URL 作为“固件 URI”值，使用所需的任何值作为“固件版本”。 模拟设备不会访问该 URL。

1. 记下该作业影响的设备数，并选择“应用”：

    ![提交固件更新作业](./media/iot-accelerators-remote-monitoring-explore/devicessubmitupdate.png)

可以使用“维护”页来跟踪正在运行的作业。

1. 若要查看作业列表，请导航到“维护”页，单击“作业”。

1. 找到与所创建的作业相关的事件。 验证固件更新过程是否已正常启动。

<!-- 05/01 broken 
You can create a filter to verify the firmware version updated correctly.

1. To create a filter, navigate to the **Devices** page and select **Manage device groups**:

    ![Manage device groups](./media/iot-accelerators-remote-monitoring-explore/devicesmanagefilters.png)

1. Create a filter that includes only devices with the new firmware version:

    ![Create device filter](./media/iot-accelerators-remote-monitoring-explore/devicescreatefilter.png)

1. Return to the **Devices** page and verify that the device has the new firmware version. -->

## <a name="organize-your-assets"></a>组织资产

Contoso 设立了两个不同的团队来从事现场服务活动：

* 智能建筑团队管理冷却器、升降机和引擎。
* 智能车辆团队管理卡车和原型制作设备。

为了方便操作员组织和管理设备，需要使用相应的团队名称对设备进行标记。

可以创建要在设备上使用的标记名称。

1. 若要显示所有设备，请导航到“设备”页，选择“所有设备”筛选器：

    ![显示所有设备](./media/iot-accelerators-remote-monitoring-explore/devicesalldevices.png)

1. 选择“卡车”和“原型制作”设备。 然后选择“作业”：

    ![选择原型制作和卡车设备](./media/iot-accelerators-remote-monitoring-explore/devicesmultiselect.png)

1. 选择“标记”，创建名为 **FieldService**、值为 **ConnectedVehicle** 的新文本标记。 选择作业的名称。 然后单击“应用”：

    ![将标记添加到原型制作和卡车设备](./media/iot-accelerators-remote-monitoring-explore/devicesaddtag.png)

1. 选择“冷却器”、“升降机”和“引擎”设备。 然后选择“作业”：

    ![选择冷却器、引擎和升降机设备](./media/iot-accelerators-remote-monitoring-explore/devicesmultiselect2.png)

1. 选择“标记”，创建名为 **FieldService**、值为 **SmartBuilding** 的新文本标记。 选择作业的名称。 然后单击“应用”：

    ![将标记添加到冷却器、引擎和升降机设备](./media/iot-accelerators-remote-monitoring-explore/devicesaddtag2.png)

可以使用标记值创建筛选器。

1. 在“设备”页上，选择“管理设备组”：

    ![管理设备组](./media/iot-accelerators-remote-monitoring-explore/devicesmanagefilters.png)

1. 创建使用标记名称 **FieldService** 和值 **SmartBuilding** 的新筛选器。 将该筛选器另存为“智能建筑”。

1. 创建使用标记名称 **FieldService** 和值 **ConnectedVehicle** 的新筛选器。 将该筛选器另存为“连接的车辆”。

现在，Contoso 操作员可以根据操作团队查询设备，而无需在设备上更改任何设置。

## <a name="stop-simulated-devices"></a>停止模拟设备

可以使用设置菜单停止模拟设备。 这有助于减少测试和浏览解决方案的开销。 若要启动或停止模拟设备，请执行以下操作：

1. 选择“设置”图标。

1. 然后将“流动”切换到打开或关闭：

    ![“设置”菜单](./media/iot-accelerators-remote-monitoring-explore/settings.png)

## <a name="customize-the-ui"></a>自定义 UI

在设置菜单中，可以将基本的自定义应用到远程监视解决方案加速器。 可以：

- 在明暗主题之间进行切换。
- 更改解决方案的名称。
- 上传自定义徽标。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

>[!div class="checklist"]
> * 在仪表板上可视化和筛选设备
> * 响应警报
> * 更新设备中的固件
> * 组织资产
> * 停止和启动模拟设备

了解远程监视解决方案后，我们建议接下来继续了解远程监视解决方案的高级功能：

* [监视设备](./iot-accelerators-remote-monitoring-monitor.md)。
* [管理设备](./iot-accelerators-remote-monitoring-manage.md)。
* [使用规则自动化解决方案](./iot-accelerators-remote-monitoring-automate.md)。
* [维护解决方案](iot-accelerators-remote-monitoring-maintain.md)。
