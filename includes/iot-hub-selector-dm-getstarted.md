---
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 19331f35ea2fa773325ec61e728677e37767ab54
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/23/2019
ms.locfileid: "66156369"
---
> [!div class="op_single_selector"]
> * [设备：Node.js 服务：Node.js](../articles/iot-hub/iot-hub-node-node-device-management-get-started.md)
> * [设备：C#服务：C#](../articles/iot-hub/iot-hub-csharp-csharp-device-management-get-started.md)
> * [设备：Java 服务：Java](../articles/iot-hub/iot-hub-java-java-device-management-getstarted.md)
> * [设备：Python 服务：Python](../articles/iot-hub/iot-hub-python-python-device-management-get-started.md)

后端应用可以使用 Azure IoT 中心基元（例如[设备孪生][lnk-devtwin]和[直接方法][lnk-c2dmethod]）远程启动和监视设备上的设备管理操作。 本教程说明后端应用和设备应用如何协同工作，以便使用 IoT 中心发起远程设备重启操作并对其进行监视。

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

使用直接方法可从云中的后端应用启动设备管理操作（例如重新启动、恢复出厂设置以及固件更新）。 设备负责以下操作：

* 处理从 IoT 中心发送的方法请求。
* 在设备上启动相应的设备特定操作。
* 通过向 IoT 中心 *报告的属性*，提供状态更新。

可以使用云中的后端应用运行设备克隆查询，以报告设备管理操作的进度。

[lnk-devtwin]: ../articles/iot-hub/iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
