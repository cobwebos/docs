---
title: 在 Azure IoT Central 中连接 Rigado 级联 500 |Microsoft Docs
description: 了解如何将 Rigado Cascade 500 网关设备连接到 IoT Central 应用程序。
services: iot-central
ms.service: iot-central
ms.topic: conceptual
ms.custom:
- iot-storeAnalytics-conditionMonitor
- iot-p0-scenario
ms.author: avneets
author: avneet723
ms.date: 11/27/2019
ms.openlocfilehash: 164fc4eef245a2b9ea610179c3cf2e78b7b1b389
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2019
ms.locfileid: "74895690"
---
# <a name="connect-a-rigado-cascade-500-gateway-device-to-your-azure-iot-central-application"></a>将 Rigado Cascade 500 网关设备连接到 Azure IoT Central 应用程序


本文介绍如何使用解决方案生成器将 Rigado Cascade 500 网关设备连接到 Microsoft Azure IoT Central 应用程序。 

## <a name="what-is-cascade-500"></a>什么是级联500？

Cascade 500 IoT 网关是 Rigado 中的一项硬件服务，包含在其级联边缘即服务解决方案中。 它通过灵活的边缘计算能力、强健的容器化应用程序环境和各种无线设备连接选项（包括蓝牙5、LTE & Wi-fi）提供商业 IoT 项目和产品团队。

Cascade 500 预认证适用于 Azure IoT 即插即用（PnP），使我们的解决方案构建者可以轻松地将设备集成到端到端解决方案。 使用级联网关可以通过无线方式连接到接近网关设备的各种状况监视传感器。 可以通过网关设备将这些传感器载入到 IoT Central。

## <a name="prerequisites"></a>必备组件
若要逐步执行本操作方法指南，需要以下资源：

* Rigado Cascade 500 设备。 有关详细信息，请访问[Rigado](https://www.rigado.com/)。
* 从一个预览版应用程序模板创建的 Azure IoT Central 应用程序。 有关详细信息，请参阅[创建新的应用程序](./quick-deploy-iot-central.md)。

## <a name="add-a-device-template"></a>添加设备模板

若要将级联500网关设备载入 Azure IoT Central 应用程序实例，需要在应用程序中配置相应的设备模板。

添加 Cascade 500 设备模板： 

1. 导航到左侧窗格中的 "***设备模板***" 选项卡，选择 " **+ 新建**： ![创建新设备模板"](./media/howto-connect-rigado-cascade500/device-template-new.png)
1. 页面提供了***创建自定义模板***或***使用预配置设备模板***的选项
1. 从预配置的设备模板列表中选择 "C500 设备" 模板，如下所示： ![选择 C500 设备模板 "](./media/howto-connect-rigado-cascade500/device-template-preconfigured.png)
1. 选择 "***下一步：自定义***" 可继续执行下一步。 
1. 在下一个屏幕上，选择 "***创建***"，将 C500 设备模板载入 IoT Central 应用程序。

## <a name="retrieve-application-connection-details"></a>检索应用程序连接详细信息

现在，你将需要检索 Azure IoT Central 应用程序的**作用域 ID**和**主密钥**，以便连接 Cascade 500 设备。 

1. 在左窗格中导航到 "**管理**"，然后单击 "**设备连接**"。 
2. 记下 IoT Central 应用程序的**作用域 ID** 。
![应用范围 ID](./media/howto-connect-rigado-cascade500/app-scope-id.png)
3. 现在，请单击 "**查看密钥**" 并记下 "**主密钥"
** ![主键](./media/howto-connect-rigado-cascade500/primary-key-sas.png)  

## <a name="contact-rigado-to-connect-the-gateway"></a>联系 Rigado 连接网关 

若要将 Cascade 500 设备连接到 IoT Central 应用程序，需要联系 Rigado，并向他们提供上述步骤中的应用程序连接详细信息。 

设备连接到 internet 后，Rigado 将能够通过安全通道向下推送到 Cascade 500 网关设备的配置更新。 

此更新将在 Cascade 500 设备上应用 IoT Central 连接详细信息，并将其显示在 "设备" 列表中。 

![主密钥](./media/howto-connect-rigado-cascade500/devices-list-c500.png)  

你现在已准备好在 IoT Central 应用程序中使用 C500 设备！

## <a name="next-steps"></a>后续步骤

现在，你已了解如何将 Rigado Cascade 500 连接到 Azure IoT Central 应用程序，接下来我们将了解如何[创建应用商店内分析应用程序](../retail/tutorial-in-store-analytics-create-app-pnp.md)来构建端到端解决方案。 