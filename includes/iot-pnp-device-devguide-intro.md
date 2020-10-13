---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 09/24/2020
ms.openlocfilehash: 706e936590f5f47ee2989bf77b3caad67a8f28fc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91579582"
---
IoT 即插即用允许构建智能设备，将其功能公布到 Azure IoT 应用程序。 当客户将其连接到 IoT 即插即用启用的应用程序时，IoT 即插即用设备不需要手动配置。

智能设备可以直接实现、使用 [模块](../articles/iot-hub/iot-hub-devguide-module-twins.md)或使用 [IoT Edge 模块](../articles/iot-edge/about-iot-edge.md)。

本指南介绍了按照 [IoT 即插即用约定](../articles/iot-pnp/concepts-convention.md)创建设备、模块或 IoT Edge 模块所需的基本步骤。

若要构建 IoT 即插即用设备、模块或 IoT Edge 模块，请执行以下步骤：

1. 确保你的设备使用 MQTT 或 MQTT over Websocket 协议连接到 Azure IoT 中心。
1. 创建 [数字孪生定义语言 (DTDL) ](https://github.com/Azure/opendigitaltwins-dtdl) 模型来描述你的设备。 若要了解详细信息，请参阅 [了解 IoT 即插即用模型中的组件](../articles/iot-pnp/concepts-components.md)。
1. 更新设备或模块，以便在 `model-id` 设备连接过程中通告。
1. 使用[IoT 即插即用约定](../articles/iot-pnp/concepts-convention.md)实现遥测、属性和命令

设备或模块实现准备就绪后，请使用 [Azure iot 浏览器](../articles/iot-pnp/howto-use-iot-explorer.md) 验证设备是否遵循 IoT 即插即用约定。
