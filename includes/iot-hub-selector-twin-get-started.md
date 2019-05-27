---
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 72ccad94301e053d8103ca949d41202e58d9f5bb
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/23/2019
ms.locfileid: "66146547"
---
> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-twin-getstarted.md)
> * [C#](../articles/iot-hub/iot-hub-csharp-csharp-twin-getstarted.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-twin-getstarted.md)
> * [Python](../articles/iot-hub/iot-hub-python-twin-getstarted.md)

设备孪生是存储设备状态信息（元数据、配置和条件）的 JSON 文档。 IoT 中心为连接到它的每台设备保留一个设备孪生。

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

使用设备克隆可以：

* 存储来自解决方案后端的设备元数据。

* 通过设备应用报告当前状态信息，例如可用功能和条件（例如，使用的连接方法）。

* 同步设备应用和后端应用之间的长时间运行工作流的状态（例如固件和配置更新）。

* 查询设备的元数据、配置或状态。

设备孪生旨在执行同步以及查询设备的配置和条件。 [了解设备孪生](../articles/iot-hub/iot-hub-devguide-device-twins.md)中提供了有关何时使用设备孪生的详细信息。

设备孪生存储在 IoT 中心内，其中包含：

* *标记*，仅可由解决方案后端访问的设备元数据；

* *所需属性*，可以由解决方案后端修改以及由设备应用观察的 JSON 对象；以及

* *报告属性*，可由设备应用修改以及由解决方案后端读取的 JSON 对象。 标记和属性不能包含数组，但可以嵌套对象。

![显示功能的设备孪生图像](./media/iot-hub-selector-twin-get-started/twin.png)

此外，解决方案后端可以根据上述所有数据查询设备孪生。
有关设备孪生的详细信息，请参阅[了解设备孪生](../articles/iot-hub/iot-hub-devguide-device-twins.md)，有关查询的信息，请参阅 [IoT 中心查询语言](../articles/iot-hub/iot-hub-devguide-query-language.md)参考。


本教程演示如何：

* 创建将*标记*添加到设备孪生的后端应用，以及将其连接通道作为设备孪生上的*报告属性*进行报告的模拟设备应用。

* 使用标记上的筛选器和之前创建的属性通过后端应用查询设备。
