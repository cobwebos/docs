---
title: "面向物联网的 Azure 解决方案（IoT 套件）| Microsoft 文档"
description: "示例 IoT 解决方案体系结构的概述，以及它如何与设备、Azure IoT 中心服务、Azure IoT 设备 SDK、Azure IoT 服务 SDK 和其他 Azure 服务相关。"
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: a859e379-dca7-42fa-bdf6-1125c86ad140
ms.service: iot-hub
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/15/2017
ms.author: dobett
ms.openlocfilehash: 417ca4b6ecc39cbdafd8e12b5360b370d0ce79fa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
[!INCLUDE [iot-azure-and-iot](../../includes/iot-azure-and-iot.md)]

## <a name="next-steps"></a>后续步骤

Azure IoT 中心是一项 Azure 服务，可在一个解决方案后端和数百万个设备之间实现安全可靠的双向通信。 它使解决方案后端可以：

* 从设备大规模接收遥测。
* 将数据从设备路由到流事件处理器。
* 从设备接收文件上传。
* 将云到设备的消息发送到特定设备。

可以使用 IoT 中心来实现自己的解决方案后端。 此外，IoT 中心还包含标识注册表，可用来预配设备、其安全凭据及其连接到 IoT 中心的权限。 若要详细了解 IoT 中心，请参阅 [IoT 中心是什么][lnk-iot-hub]。

若要了解 Azure IoT 中心如何实现标准的设备管理，以便远程管理设备，请参阅 [IoT 中心设备管理概述][lnk-device-management]。

可以使用 Azure IoT 设备 SDK 在各种设备硬件平台和操作系统上实现客户端应用程序。 设备 SDK 包含库，可协助将遥测数据发送到 IoT 中心，并接收云到设备的消息。 使用设备 SDK 时，要与 IoT 中心通信，有多个网络协议可选。 若要了解详细信息，请参阅[设备 SDK 的相关信息][lnk-device-sdks]。

若要开始编写一些代码并运行一些示例，请参阅 [IoT 中心入门][lnk-getstarted]教程。

也可能会对 [Azure IoT 套件][lnk-iot-suite]有兴趣，这是一套预配置的解决方案。 IoT 套件可让你快速入门和扩展 IoT 项目，以应对常见的 IoT 案例，例如远程监视、资产管理和预测性维护。

[lnk-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks
[lnk-iot-hub]: iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-iotdev]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: iot-hub-device-management-overview.md
