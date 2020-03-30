---
title: 在 Azure IoT Central 中连接 Rigado Cascade 500 | Microsoft Docs
description: 了解如何将 Rigado Cascade 500 网关设备连接到 IoT Central 应用程序。
services: iot-central
ms.service: iot-central
ms.topic: how-to
ms.custom:
- iot-storeAnalytics-conditionMonitor
- iot-p0-scenario
ms.author: avneets
author: avneet723
ms.date: 11/27/2019
ms.openlocfilehash: d52366684d772f91b53a1ab385b51ae4f11f0a5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158320"
---
# <a name="connect-a-rigado-cascade-500-gateway-device-to-your-azure-iot-central-application"></a>如何将 Rigado Cascade 500 网关设备连接到 Azure IoT Central 应用程序


本文为解决方案构建人员介绍如何将 Rigado Cascade 500 网关设备连接到 Microsoft Azure IoT Central 应用程序。 

## <a name="what-is-cascade-500"></a>什么是 Cascade 500？

Cascade 500 IoT 网关是 Rigado 提供的一款硬件产品，随附在其 Cascade 边缘即服务解决方案中。 该产品为商用 IoT 项目和产品团队提供灵活的边缘计算能力、稳健的容器化应用程序环境，以及各种无线设备连接选项，包括 Bluetooth 5、LTE 和 Wi-Fi。

Cascade 500 已为 Azure IoT 即插即用（预览）进行了预认证，允许我们的解决方案构建者轻松地将设备送入端到端解决方案。 Cascade 网关允许无线连接到网关设备附近的各种状态监视传感器。 可以通过网关设备将这些传感器加入到 IoT Central 中。

## <a name="prerequisites"></a>先决条件
若要完成本操作指南中的每个步骤，需要准备好以下资源：

* 一台 Rigado Cascade 500 设备。 有关详细信息，请访问 [Rigado](https://www.rigado.com/)。
* Azure IoT Central 应用程序。 有关详细信息，请参阅[创建新应用程序](./quick-deploy-iot-central.md)。

## <a name="add-a-device-template"></a>添加设备模板

若要将 Cascade 500 网关设备加入到 Azure IoT Central 应用程序实例，需要在应用程序中配置相应的设备模板。

若要添加 Cascade 500 设备模板： 

1. 导航到左侧窗格中的 ***"设备模板"*** 选项卡，选择 **"新建**"：![创建新设备模板](./media/howto-connect-rigado-cascade-500/device-template-new.png)
1. 页面中提供了“创建自定义模板”或“使用预配置的设备模板”选项************
1. 从预配置的设备模板列表中选择 C500 设备模板，如下所示：![选择 C500 设备模板](./media/howto-connect-rigado-cascade-500/device-template-preconfigured.png)
1. 选择 ***"下一步"：自定义***以继续下一步。 
1. 在下一个屏幕上，选择“创建”以将 C500 设备模板加入到 IoT Central 应用程序中。******

## <a name="retrieve-application-connection-details"></a>检索应用程序连接详细信息

现在需要检索 Azure IoT Central 应用程序的“范围 ID” 和“主密钥”，以连接 Cascade 500 设备。******** 

1. 在左侧窗格中导航到“管理”，单击“设备连接”。******** 
2. 记下 IoT Central 应用程序的“范围 ID”。****
![应用范围 ID](./media/howto-connect-rigado-cascade-500/app-scope-id.png)
3. 现在单击 **"查看键**"并记下**主键主键**
![](./media/howto-connect-rigado-cascade-500/primary-key-sas.png)  

## <a name="contact-rigado-to-connect-the-gateway"></a>联系 Rigado 来连接网关 

若要将 Cascade 500 设备连接到 IoT Central 应用程序，需要联系 Rigado 并提供上述步骤中获取的应用程序连接详细信息。 

将设备连接到 Internet 后，Rigado 可以通过安全渠道将配置更新推送到 Cascade 500 网关设备。 

此更新将在 Cascade 500 设备上应用 IoT Central 连接详细信息，并显示在设备列表中。 

![主密钥](./media/howto-connect-rigado-cascade-500/devices-list-c500.png)  

现已准备好在 IoT Central 应用程序中使用 C500 设备！

## <a name="next-steps"></a>后续步骤

了解如何将 Rigado Cascade 500 连接到 Azure IoT Central 应用程序后，建议接下来了解如何[创建店内分析应用程序](../retail/tutorial-in-store-analytics-create-app-pnp.md)来构建端到端的解决方案。 