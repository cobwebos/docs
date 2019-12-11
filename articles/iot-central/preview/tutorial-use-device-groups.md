---
title: 使用 Azure IoT Central 应用程序中的设备组 |Microsoft Docs
description: 作为操作员，了解如何使用设备组分析 Azure IoT Central 应用程序中的设备的遥测数据。
author: dominicbetts
ms.author: dobett
ms.date: 12/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: 981046f9417876a68c215c6d01a63c7f0a634464
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976207"
---
# <a name="tutorial-use-device-groups-to-analyze-device-telemetry-preview-features"></a>教程：使用设备组分析设备遥测（预览功能）

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

本文介绍如何使用设备组来分析 Azure IoT Central 应用程序中的设备遥测，作为操作员。

设备组是组合在一起的设备列表，因为它们与某些指定的条件相匹配。 设备组通过将设备分组为较小的逻辑组，帮助你按比例管理、可视化和分析设备。 例如，你可以创建一个设备组来列出西雅图的所有 air 空调设备，以使技术人员能够查找其所负责的设备。

本教程介绍如何执行以下操作：

> [!div class="checklist"]
> * 创建设备组
> * 使用设备组来分析设备遥测数据

## <a name="prerequisites"></a>必备组件

在开始之前，应完成[创建 Azure IoT Central 应用程序](./quick-deploy-iot-central.md)和[将模拟设备添加到 IoT Central 应用程序](./quick-create-pnp-device.md)快速入门，以创建要使用的环境传感器设备模板。

## <a name="create-simulated-devices"></a>创建模拟设备

创建设备组之前，请从**环境传感器**设备模板添加至少五个模拟设备，以便在本教程中使用：

![五个模拟环境传感器设备](./media/tutorial-use-device-groups/simulated-devices.png)

对于四个环境传感器设备，使用 "**环境传感器属性**" 视图将客户名称设置为**Contoso**：

![将客户名称设置为 Contoso](./media/tutorial-use-device-groups/customer-name.png)

## <a name="create-a-device-group"></a>创建设备组

若要创建设备组：

1. 选择左窗格中的 "**设备组**"。

1. 选择“+ 新建”。

    ![新设备组](media/tutorial-use-device-groups/image1.png)

1. 为设备组指定一个名称，如**Contoso 设备**。 还可以添加说明。 设备组只能包含来自单个设备模板的设备。 选择要用于此组的**环境传感器**设备模板。

1. 通过选择 "**客户名称**" 属性、"**等于**比较运算符" 和 " **contoso** " 作为值，创建查询以确定属于该设备组的**Contoso**的设备。 你可以添加多个查询，并将满足**所有**条件的设备放入设备组中。 有权访问该应用程序的任何人都可以访问您创建的设备组，以便任何人都可以查看、修改或删除该设备组。

    ![设备组查询](media/tutorial-use-device-groups/image2.png)

    > [!NOTE]
    > 设备组是一个动态查询。 每当查看设备列表时，列表中都可能显示不同的设备。 列表内容取决于哪些设备当前满足查询的条件。

1. 选择“保存”。

> [!NOTE]
> 对于 Azure IoT Edge 设备，请选择 Azure IoT Edge 模板来创建设备组。

## <a name="analytics"></a>分析

可以通过设备组使用**分析**来分析来自组中的设备的遥测数据。 例如，您可以绘制所有 Contoso 环境传感器报告的平均温度。

若要分析设备组的遥测数据：

1. 选择左窗格中的 "**分析**"。

1. 选择创建的**Contoso 设备**组。 然后添加**温度**和**湿度**遥测类型：

    ![创建分析](./media/tutorial-use-device-groups/create-analysis.png)

    使用遥测类型旁边的齿轮图标选择聚合类型。 默认值为**Average**。 使用**Split by**更改聚合数据的显示方式。 例如，如果按设备 ID 拆分，则选择 "**分析**" 时，会看到每个设备的绘图。

1. 选择 "**分析**" 以查看平均遥测值：

    ![查看分析](./media/tutorial-use-device-groups/view-analysis.png)

    您可以自定义视图，更改显示的时间段，然后导出数据。

## <a name="next-steps"></a>后续步骤

现在，你已了解如何使用 Azure IoT Central 应用程序中的设备组，以下是建议的下一个步骤：

> [!div class="nextstepaction"]
> [如何创建遥测规则](tutorial-create-telemetry-rules.md)
