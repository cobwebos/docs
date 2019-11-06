---
title: include 文件
description: include 文件
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 01/04/2019
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 12661c77c6a950b482187b09e4465c964e6d6652
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494078"
---
Azure IoT Edge 的主要功能之一是能够从云中将代码部署到 IoT Edge 设备。 **IoT Edge 模块**是以容器形式实现的可执行程序包。 在本部分中，你将从 [Azure 市场的“IoT Edge 模块”部分](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)部署一个预建的模块。 

在本部分中部署的模块模拟一个传感器并发送生成的数据。 开始使用 IoT Edge 时，此模块是非常有用的代码段，因为你可以使用模拟的数据进行开发和测试。 如果要确切了解此模块的功能，则可以查看[模拟的温度传感器源代码](https://github.com/Azure/iotedge/blob/027a509549a248647ed41ca7fe1dc508771c8123/edge-modules/SimulatedTemperatureSensor/src/Program.cs)。 

若要从 Azure 市场部署你的第一个模块，请使用以下步骤：

1. 在 [Azure 门户](https://portal.azure.com)中，在搜索中输入“模拟的温度传感器”并打开市场结果  。

   ![Azure 门户搜索中的模拟的温度传感器](./media/iot-edge-deploy-module/search-for-temperature-sensor.png)

2. 选择要接收该模块的 IoT Edge 设备。 在“IoT Edge 模块的目标设备”  页面上，提供以下信息：

   1. **订阅**：选择包含要使用的 IoT 中心的订阅。

   2. **IoT 中心**：选择要使用的 IoT 中心的名称。

   3. **IoT Edge 设备名**：如果在此快速入门早期已使用推荐的设备名，则输入 myEdgeDevice  。 或者，选择“查找设备”以从 IoT 中心的 IoT Edge 设备列表中进行选择  。 
   
   4. 选择“创建”  。

3. 现在，你已从 Azure 市场选择了 IoT Edge 模块，并且选择了要接收该模块的 IoT Edge 设备，接下来，你将转到一个具有三个步骤的向导，该向导可帮助你确切定义如何部署该模块。 在向导的“添加模块”步骤中，请注意“SimulatedTemperatureSensor”模块会自动填充   。 在教程中，将使用此页向部署添加其他模块。 对于本快速入门，只需要部署这一个模块。 选择“下一步”继续执行向导的下一步  。

4. 在向导的“指定路由”步骤中，定义消息在模块之间传递以及传递到 IoT 中心的方式  。 对于本快速入门，需要来自所有模块的所有消息都转到 IoT 中心 (`$upstream`)。 如果未自动填充，请添加以下代码：

   ```json
    {
    "routes": {
        "route": "FROM /messages/* INTO $upstream"
        }
    }
   ```
   然后，选择“下一步”  。

5. 在向导的“查看部署”步骤中，可以预览定义部署到 IoT Edge 设备的所有模块的 JSON 文件  。 请注意，包括 **SimulatedTemperatureSensor** 模块以及名为 **edgeAgent** 和 **edgeHub** 的两个其他系统模块。 查看完成后，选择“提交”  。

   在将新部署提交到 IoT Edge 设备时，不会向设备推送任何内容。 相反，设备会定期对 IoT 中心进行查询以获取任何新指令。 如果设备找到了更新的部署清单，则它会使用关于新部署的信息来从云中拉取模块映像，然后开始在本地运行模块。 此过程可能需要几分钟时间。 

6. 提交模块部署详细信息后，向导将引导你返回到 IoT 中心的 IoT Edge 页  。 从 IoT Edge 设备列表中选择设备，以查看其详细信息。 

7. 在设备详细信息页上，向下滚动至“模块”部分  。 应有三个模块列出：$edgeAgent、$edgeHub 和 SimulatedTemperatureSensor。 如果列出了部署中指定的一个或多个模块，但设备未报告这些模块，则意味着 IoT Edge 设备仍在启动它们。 请稍等片刻，然后选择页面顶部的“刷新”  。 

   ![查看已部署模块列表中的 SimulatedTemperatureSensor](./media/iot-edge-deploy-module/deployed-modules-marketplace.png)
