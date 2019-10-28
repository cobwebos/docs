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
ms.date: 10/19/2019
ms.openlocfilehash: 0a93bedb697a0d5a514fddab55c79b969b2bd77b
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2019
ms.locfileid: "72954222"
---
# <a name="connect-a-ruuvitag-sensor-to-your-azure-iot-central-application"></a>将 RuuviTag 传感器连接到 Azure IoT Central 应用程序

本文介绍如何使用解决方案生成器将 RuuviTag 传感器连接到 Microsoft Azure IoT Central 应用程序。

什么是 Ruuvi 标记？

RuuviTag 是一种高级开源传感器信标平台，旨在满足企业客户、开发人员和学生的需求，甚至可以在你的家庭中使用，也可以作为你的个人工作的一部分使用。 将设备设置为在其盒中取出并准备好将其部署到所需的位置时，设备将立即生效。 它是一个蓝牙 LE 信标，其中内置了环境传感器和加速感应器。 

RuuviTag 通过 BLE （蓝牙低能耗）进行通信，因此要求网关设备与 Azure IoT Central 通信。 请确保已有网关设备，如 Rigado Cascade 500，安装程序能够将 RuuviTag 连接到 IoT Central。 

如果要设置 Rigado Cascade 500 网关设备，请按照[此处的说明](./howto-connect-rigado-cascade-500-pnp.md)进行操作。

## <a name="prerequisites"></a>必备组件
若要连接 RuuviTag 传感器，需要以下资源：

* RuuviTag 传感器。 有关详细信息，请访问[RuuviTag](https://ruuvi.com/)。 
* Rigado Cascade 500 设备或其他 BLE 网关。 有关详细信息，请访问[Rigado](https://www.rigado.com/)。
* 从一个预览版应用程序模板创建的 Azure IoT Central 应用程序。 有关详细信息，请参阅[创建新的应用程序](https://docs.microsoft.com/azure/iot-central/quick-deploy-iot-central-pnp?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)。

## <a name="add-a-ruuvitag-device-template"></a>添加 RuuviTag 设备模板

若要将 RuuviTag 传感器载入 Azure IoT Central 应用程序实例，需要在应用程序中配置相应的设备模板。

添加 RuuviTag 设备模板： 

1. 导航到左侧窗格中的 "***设备模板***" 选项卡，选择 " **+ 新建**： ![创建新设备模板"](./media/howto-connect-ruuvi/devicetemplate-new.png) 页面提供***创建自定义模板***或***使用预配置设备模板***的选项
1. 从预配置的设备模板列表中选择 "RuuviTag 设备" 模板，如下所示： ![选择 RuuviTag 设备模板 "](./media/howto-connect-ruuvi/devicetemplate-preconfigured.png)
1. 选择 "***下一步：自定义***" 可继续执行下一步。 
1. 在下一个屏幕上，选择 "***创建***"，将 C500 设备模板载入 IoT Central 应用程序。

## <a name="connect-a-ruuvitag-sensor"></a>连接 RuuviTag 传感器

如上所述，若要将 RuuviTag 与 IoT Central 应用程序进行连接，则需要设置网关设备。 在下面的步骤中，我们假定已设置了 Rigado Cascade 500 网关设备。  

1. 打开 Rigado Cascade 500 设备并将其连接到网络连接（通过以太网或无线连接）
1. 从 RuuviTag 中弹出护盖，并拔出塑料选项卡以保护与电池的连接。 
1. 将 RuuviTag 放在接近已配置 IoT Central 应用程序的 Rigado 级联500网关附近。 
1. 只需几秒钟，你的 RuuviTag 就会出现在 IoT Central 中的设备列表中。  
![RuuviTag 设备列表](./media/howto-connect-ruuvi/ruuvi-devicelist.png) 你现在可以在 IoT Central 应用程序中使用此 RuuviTag。  

## <a name="create-a-simulated-ruuvitag"></a>创建模拟 RuuviTag
如果没有可用的物理 RuuviTag，可以创建模拟的 RuuviTag 传感器，用于在 Azure IoT Central 应用程序中进行测试。

创建模拟 RuuviTag：

1. 选择**RuuviTag 的设备 >** 。 
1. 选择 " **+ 新建**"。 
1. 指定唯一的**设备 ID**和友好**设备名称**。  
1. 启用**模拟**设置。
1. 选择**创建**。  

## <a name="next-steps"></a>后续步骤

现在，你已了解如何将 RuuviTag 连接到 Azure IoT Central 应用程序，接下来需要了解如何[自定义你的 IoT Central 应用程序](../retail/tutorial-in-store-analytics-customize-dashboard-pnp.md)以构建端到端解决方案。 