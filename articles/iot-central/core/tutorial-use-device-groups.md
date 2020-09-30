---
title: 教程 - 在 Azure IoT Central 应用程序中使用设备组 | Microsoft Docs
description: 教程 - 了解如何以操作员身份在 Azure IoT Central 应用程序中使用设备组分析设备的遥测数据。
author: dominicbetts
ms.author: dobett
ms.date: 02/12/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: 3192a9f121d4380a3e681747596fc91997662bf0
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90967937"
---
# <a name="tutorial-use-device-groups-to-analyze-device-telemetry"></a>教程：使用设备组分析设备遥测数据

本文介绍操作员如何在 Azure IoT Central 应用程序中使用设备组分析设备遥测数据。

设备组是由于与某个指定的条件匹配，而分组到一起的一系列设备。 设备组通过将设备分组到较小的逻辑组，来帮助大规模管理、可视化和分析设备。 例如，可以针对西雅图的所有空调设备创建一个设备组，使技术人员能够找到他们所要负责的设备。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建设备组
> * 使用设备组分析设备遥测数据

## <a name="prerequisites"></a>先决条件

在开始之前，应完成[创建 Azure IoT Central 应用程序](./quick-deploy-iot-central.md)和[将模拟设备添加到 IoT Central 应用程序](./quick-create-simulated-device.md)快速入门，以创建要使用的 MXChip IoT DevKit 设备模板  。

## <a name="create-simulated-devices"></a>创建模拟设备

创建设备组之前，请从“MXChip IoT DevKit”设备模板中添加至少五个模拟设备，以便在本教程中使用： 

![五个模拟传感器设备](./media/tutorial-use-device-groups/simulated-devices.png)

对于其中四个模拟传感器设备，请使用“管理设备”  视图将客户名称设为“Contoso”  ：

![将客户名称设置为 Contoso](./media/tutorial-use-device-groups/customer-name.png)

## <a name="create-a-device-group"></a>创建设备组

若要创建设备组：

1. 在左侧窗格中选择“资源组”。 

1. 选择“+”  ：

    ![新建设备组](media/tutorial-use-device-groups/image1.png)

1. 将设备组命名为“Contoso 设备”。  还可以添加说明。 设备组只能包含单个设备模板中的设备。 选择要用于此组的“MXChip IoT DevKit”设备模板。 

1. 若要自定义设备组以仅包括属于“Contoso”  的设备，请选择“+ 筛选器”  。 选择“客户名称”  属性、“等于”  比较运算符以及“Contoso”  作为值。 你可以添加多个筛选器，并且满足所有  筛选条件的设备将放置在设备组中。 有权访问该应用程序的任何人都可以访问你创建的设备组，因此，任何人都可以查看、修改或删除该设备组：

    ![设备组查询](media/tutorial-use-device-groups/image2.png)

    > [!TIP]
    > 设备组是动态查询。 每当查看设备列表时，列表中都可能显示不同的设备。 列表内容取决于哪些设备当前满足查询的条件。

1. 选择“保存”  。

> [!NOTE]
> 对于 Azure IoT Edge 设备，请选择用于创建设备组的 Azure IoT Edge 模板。

## <a name="analytics"></a>分析

可以结合使用 **Analytics** 和设备组来分析该组中的设备发来的遥测数据。 例如，可以绘制所有 Contoso 环境传感器报告的平均温度的图形。

若要分析设备组的遥测数据：

1. 在左侧窗格中选择“Analytics”。 

1. 选择创建的“Contoso 设备”设备组。  然后，添加“温度”和“湿度”遥测类型：  

    ![创建分析](./media/tutorial-use-device-groups/create-analysis.png)

    使用遥测类型旁边的齿轮图标选择聚合类型。 默认类型为“平均”。  使用“拆分依据”更改聚合数据的显示方式。  例如，如果按设备 ID 进行拆分，则选择“分析”时，将看到每个设备的绘图。 

1. 选择“分析”以查看平均遥测值： 

    ![查看分析](./media/tutorial-use-device-groups/view-analysis.png)

    可以自定义视图，更改显示的时间段，并导出数据。

## <a name="next-steps"></a>后续步骤

了解如何在 Azure IoT Central 应用程序中使用设备组后，建议接下来学习：

> [!div class="nextstepaction"]
> [如何创建遥测规则](tutorial-create-telemetry-rules.md)
