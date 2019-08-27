---
title: 将真实的设备添加到 Azure IoT Central 应用程序 | Microsoft Docs
description: 以操作员的身份将真实设备添加到 Azure IoT Central 应用程序。
author: sarahhubbard
ms.author: sahubbar
ms.date: 08/06/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 7191c98cfc522068dbea576a8f81776ae4301da8
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2019
ms.locfileid: "69878858"
---
# <a name="tutorial-add-a-simulated-device-to-your-azure-iot-central-application-preview-features"></a>教程：将模拟设备添加到 Azure IoT Central 应用程序（预览功能）

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

本教程介绍如何在 Microsoft Azure IoT Central 应用程序中添加和配置模拟设备。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 添加新的模拟设备
> * 在构建体验中使用模拟设备

## <a name="prerequisites"></a>先决条件

在开始之前，构建者应完成第一篇构建者教程以创建 Azure IoT Central 应用程序：

* [定义新设备类型](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)（必需）

## <a name="add-a-simulated-device"></a>添加模拟设备

若要将真实设备添加到应用程序，请使用[定义新设备类型](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)教程中创建的“环境传感器”设备模板。 

1. 若要以操作员身份添加新设备，请在左侧导航菜单中选择“设备”  。 “设备”  选项卡显示“所有设备”  和“环境传感器”  设备模板。

1. 若要添加模拟环境传感器设备，请选择“+新建”  。 使用建议的**设备 ID** 或输入自己的小写**设备 ID**。 也可输入新设备的名称。 将“模拟”  开关切换为“开”  ，然后选择“创建”  。

    ![模拟设备](./media/tutorial-add-device-pnp/simulated-device.png)

现在，你可以使用模拟数据与构建者为设备模板创建的视图进行交互。

## <a name="use-a-simulated-device-to-improve-views"></a>使用模拟设备改进视图

创建新的模拟设备后，构建者可以使用此设备继续改进设备模板的视图并基于其构建。

1. 在设备视图中，复制所创建的模拟设备的**设备 ID**。

1. 在左侧导航菜单中选择“设备模板”  选项卡，然后选择“环境传感器”  模板。

1. 选择要编辑的任何视图，或创建新视图。 单击“配置预览设备”  。 你可以在此处选择“无预览设备”、“使用可配置用于测试的真实设备”，或者“从已添加到 IoT Central 的现有设备”。

1. 选择“从正在运行的设备中选择”  并输入已复制的模拟设备的**设备 ID**。

1. 选择“应用”。  现在，你可以在设备模板视图构建体验中看到相同的模拟设备。 此视图对于图表和其他可视化效果特别有用。

    ![配置预览设备](./media/tutorial-add-device-pnp/configure-preview.png)

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

* 添加新的模拟设备
* 在构建体验中使用模拟设备

现在，你已将模拟设备连接到 Azure IoT Central 应用程序，下面是一些建议的后续步骤。

以操作员身份了解如何：

> [!div class="nextstepaction"]
> [配置规则](tutorial-configure-rules.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)

以设备开发人员身份了解如何：

> [!div class="nextstepaction"]
> [将 MXChip IoT DevKit 设备连接到 Azure IoT Central 应用程序](howto-connect-devkit-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)



