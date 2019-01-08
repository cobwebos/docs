---
title: include 文件
description: include 文件
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 12/31/2018
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: dd4873017105db190f9a468ec1f1f77f4e8c9c0e
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/02/2019
ms.locfileid: "53977091"
---
Azure IoT Edge 的主要功能之一是能够从云中将模块部署到 IoT Edge 设备。 IoT Edge 模块是以容器形式实现的可执行包。 在本部分中，我们将从 [Azure 市场的“IoT Edge 模块”部分](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)部署一个预建的模块。 此模块将生成 IoT Edge 设备的模拟遥测数据。

1. 在 [Azure 门户](https://portal.azure.com)中，在搜索中输入“模拟的温度传感器”并打开市场结果。

   ![Azure 门户搜索中的模拟的温度传感器](./media/iot-edge-deploy-module/search-for-temperature-sensor.png)

2. 选择要接收该模块的 IoT Edge 设备。 在“IoT Edge 模块的目标设备”中，提供以下信息：

   1. **订阅**：选择包含要使用的 IoT 中心的订阅。

   2. **IoT 中心**：选择要使用的 IoT 中心的名称。

   3. **IoT Edge 设备名**：如果在此快速入门早期已使用推荐的设备名，则输入 myEdgeDevice。 或者，选择“查找设备”以从 IoT 中心的设备列表中进行选择。 
   
   4. 选择“创建”。

3. 现在，你已从 Azure 市场选择了 IoT Edge 模块，并且选择了要接收该模块的 IoT Edge 设备，接下来，你将转到一个具有三个步骤的向导，该向导可帮助准确定义该模块的部署方式。 在向导的“添加模块”步骤中，请注意“SimulatedTemperatureSensor”模块会自动填充。 在教程中，将使用此页向部署添加其他模块。 对于本快速入门，只需部署此模块即可。 选择“下一步”继续执行向导的下一步。

4. 在向导的“指定路由”步骤中，定义消息在模块之间传递以及传递到 IoT 中心的方式。 对于本快速入门，需要来自所有模块的所有消息都转到 IoT 中心 (`$upstream`)。 如果未自动填充，请添加以下代码，然后选择“下一步”：

   ```json
    {
    "routes": {
        "route": "FROM /messages/* INTO $upstream"
        }
    }
   ```

5. 在向导的“查看部署”步骤中，可以预览定义部署到 IoT Edge 设备的所有模块的 JSON 文件。 请注意，包括“SimulatedTemperatureSensor”模块以及名为“edgeAgent”和“edgeHub”的两个其他系统模块。 查看完成后，选择“提交”。

   在将新部署提交到 IoT Edge 设备时，不会向设备推送任何内容。 相反，设备会定期对 IoT 中心进行查询以获取任何新指令。 当设备发现新部署信息时，它将使用该信息从云中拉取模块映像，并开始在本地运行模块。 此过程可能需要几分钟时间。 

6. 提交模块部署详细信息后，向导将引导你返回到 IoT 中心的 IoT Edge 页。 从 IoT Edge 设备列表中选择设备，以查看其详细信息。 

7. 在设备详细信息页上，向下滚动至“模块”部分。 应有三个模块列出：$edgeAgent、$edgeHub 和 SimulatedTemperatureSensor。 如果部署中指定的一个或多个模块被列出，但设备未报告，则意味着 IoT Edge 设备仍在启动它们。 请稍等片刻，然后选择页面顶部的“刷新”。 

   ![查看已部署模块列表中的 SimulatedTemperatureSensor](./media/iot-edge-deploy-module/deployed-modules-marketplace.png)
