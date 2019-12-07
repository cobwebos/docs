---
title: 连接 Azure IoT Central 中的 RuuviTag |Microsoft Docs
description: 了解如何将 RuuviTag 环境传感器连接到 IoT Central 应用程序。
services: iot-central
ms.service: iot-central
ms.topic: conceptual
ms.custom:
- iot-storeAnalytics-conditionMonitor
- iot-p0-scenario
ms.author: avneets
author: avneet723
ms.date: 11/27/2019
ms.openlocfilehash: 9be8fbad8811d758f9ac4205c0d1e60e0d82e07f
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2019
ms.locfileid: "74895491"
---
# <a name="connect-a-ruuvitag-sensor-to-your-azure-iot-central-application"></a>将 RuuviTag 传感器连接到 Azure IoT Central 应用程序

本文介绍如何使用解决方案生成器将 RuuviTag 传感器连接到 Microsoft Azure IoT Central 应用程序。

什么是 Ruuvi 标记？

RuuviTag 是一种高级开源传感器信标平台，旨在满足企业客户、开发人员、学员和爱好者的需求。 一旦将设备移出其盒，就会将其设置为正常工作，并随时准备好将其部署到需要的位置。 它是一个蓝牙 LE 信标，其中内置了环境传感器和加速感应器。

RuuviTag 通过 BLE （蓝牙低能耗）进行通信，并要求网关设备与 Azure IoT Central 通信。 请确保已有网关设备，如 Rigado Cascade 500，设置为启用 RuuviTag 连接到 IoT Central。

如果要设置 Rigado Cascade 500 网关设备，请按照[此处的说明](./howto-connect-rigado-cascade-500.md)进行操作。

## <a name="prerequisites"></a>必备组件

若要连接 RuuviTag 传感器，需要以下资源：

* RuuviTag 传感器。 有关详细信息，请访问[RuuviTag](https://ruuvi.com/)。
* Rigado Cascade 500 设备或其他 BLE 网关。 有关详细信息，请访问[Rigado](https://www.rigado.com/)。
* 从一个预览版应用程序模板创建的 Azure IoT Central 应用程序。 有关详细信息，请参阅[创建新的应用程序](./quick-deploy-iot-central.md)。

## <a name="add-a-ruuvitag-device-template"></a>添加 RuuviTag 设备模板

若要将 RuuviTag 传感器载入 Azure IoT Central 应用程序实例，需要在应用程序中配置相应的设备模板。

添加 RuuviTag 设备模板：

1. 导航到左侧窗格中的 "***设备模板***" 选项卡，选择 " **+ 新建**： ![创建新设备模板"](./media/howto-connect-ruuvi/devicetemplate-new.png) 页面提供***创建自定义模板***或***使用预配置设备模板***的选项
1. 从预配置的设备模板列表中选择 "RuuviTag 设备" 模板，如下所示： ![选择 RuuviTag 设备模板 "](./media/howto-connect-ruuvi/devicetemplate-preconfigured.png)
1. 选择 "***下一步：自定义***" 可继续执行下一步。
1. 在下一个屏幕上，选择 "***创建***"，将 C500 设备模板载入 IoT Central 应用程序。

## <a name="connect-a-ruuvitag-sensor"></a>连接 RuuviTag 传感器

如前文所述，若要使用 IoT Central 应用程序连接 RuuviTag，需要设置网关设备。 以下步骤假定你已设置 Rigado Cascade 500 网关设备。  

1. 打开 Rigado Cascade 500 设备并将其连接到网络连接（通过以太网或无线连接）
1. 从 RuuviTag 中弹出护盖，并拔出塑料选项卡以保护与电池的连接。
1. 将 RuuviTag 靠近已在 IoT Central 应用程序中配置的 Rigado 级联500网关。
1. 只需几秒钟，你的 RuuviTag 就会出现在 IoT Central 中的设备列表中。  
    ![RuuviTag 设备列表](./media/howto-connect-ruuvi/ruuvi-devicelist.png)

你现在可以在 IoT Central 应用程序中使用此 RuuviTag。  

## <a name="create-a-simulated-ruuvitag"></a>创建模拟 RuuviTag

如果没有物理 RuuviTag 设备，可以创建模拟的 RuuviTag 传感器，用于在 Azure IoT Central 应用程序中进行测试。

创建模拟 RuuviTag：

1. 选择**RuuviTag 的设备 >** 。
1. 选择“+ 新建”。
1. 指定唯一的**设备 ID**和友好**设备名称**。  
1. 启用**模拟**设置。
1. 选择**创建**。  

## <a name="next-steps"></a>后续步骤

现在，你已了解如何将 RuuviTag 连接到 Azure IoT Central 应用程序，接下来需要了解如何[自定义你的 IoT Central 应用程序](../retail/tutorial-in-store-analytics-customize-dashboard-pnp.md)以构建端到端解决方案。
