---
title: include 文件
description: include 文件
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 06/30/2020
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 76c8eb7acf20d8cf68d3573defd947efbc6c3c43
ms.sourcegitcommit: a989fb89cc5172ddd825556e45359bac15893ab7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2020
ms.locfileid: "85801671"
---
Azure IoT Edge 的主要功能之一是能够从云中将代码部署到 IoT Edge 设备。 *IoT Edge 模块*是以容器形式实现的可执行程序包。 在本部分中，你将直接从 Azure IoT 中心的 [Azure 市场的 IoT Edge 模块部分](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)部署一个预建的模块。

在本部分中部署的模块模拟一个传感器并发送生成的数据。 开始使用 IoT Edge 时，此模块是非常有用的代码段，因为你可以使用模拟的数据进行开发和测试。 如果要确切了解此模块的功能，则可以查看[模拟的温度传感器源代码](https://github.com/Azure/iotedge/blob/027a509549a248647ed41ca7fe1dc508771c8123/edge-modules/SimulatedTemperatureSensor/src/Program.cs)。

若要从 Azure 市场部署你的第一个模块，请使用以下步骤：

1. 登录 [Azure 门户](https://portal.azure.com)，导航到 IoT 中心。

1. 从左窗格菜单中的“自动设备管理”下，选择“IoT Edge” 。

1. 单击设备列表中目标设备的设备 ID。

1. 在上方栏中，选择“设置模块”。

   ![从设备详细信息页选择“设置模块”](./media/iot-edge-deploy-module/select-set-modules.png)

1. 在该页的“IoT Edge 模块”部分中，单击“添加”，并从下拉菜单中选择“市场模块”  。

   ![添加市场模块](./media/iot-edge-deploy-module/add-marketplace-module.png)

1. 在“IoT Edge 模块市场”中，搜索“模拟温度传感器”并选择该模块。

1. 请注意，SimulatedTemperatureSensor 模块将添加到“IoT Edge 模块”部分，并以所需的状态运行。

   在完成时选择“下一步:”路由以继续执行向导的下一步。

   ![列出温度传感器模块后，转到下一步](./media/iot-edge-deploy-module/view-temperature-sensor-next-routes.png)

1. 在向导的“路由”选项卡中，可以定义消息在模块和 IoT 中心之间的传递方式。 路由是使用名称/值对构造而成的。 应在此页上看到两条路由。 称为“路由”的默认路由用于将所有消息发送到 IoT 中心（即 `$upstream`）。 从市场添加模块时，将自动创建名为“SimulatedTemperatureSensorToIoTHub”的第二条路由。 此路由用于将所有消息（尤其是来自模拟温度模块中的消息）发送到 IoT 中心。 可以删除默认路由，因为在这种情况下它是多余的。

   在完成时选择“下一步:查看 + 创建”继续执行向导的下一步。

   ![删除默认路由，然后转到下一步](./media/iot-edge-deploy-module/delete-route-next-review-create.png)

1. 在向导的“查看 + 创建”选项卡中，可以预览定义部署到 IoT Edge 设备的所有模块的 JSON 文件。 请注意，包括“SimulatedTemperatureSensor”模块以及名为“edgeAgent”和“edgeHub”的两个运行时模块  。 完成查看后，选择“创建”。

   在将新部署提交到 IoT Edge 设备时，不会向设备推送任何内容。 相反，设备会定期对 IoT 中心进行查询以获取任何新指令。 如果设备找到了更新的部署清单，则它会使用关于新部署的信息来从云中拉取模块映像，然后开始在本地运行模块。 此过程可能需要几分钟时间。

1. 创建模块部署详细信息后，向导会将你返回到设备详细信息页。 在设备详细信息页上的“模块”选项卡中查看部署状态。应有三个模块列出：$edgeAgent、$edgeHub 和 SimulatedTemperatureSensor。 如果列出了部署中指定的一个或多个模块，但设备未报告这些模块，则意味着 IoT Edge 设备仍在启动它们。 请稍等片刻，然后选择页面顶部的“刷新”。

   ![查看已部署模块列表中的 SimulatedTemperatureSensor](./media/iot-edge-deploy-module/view-deployed-modules.png)
