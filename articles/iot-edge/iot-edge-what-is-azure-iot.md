---
title: 面向物联网的 Azure 解决方案 (IoT Edge) | Microsoft Docs
description: 示例 IoT 解决方案体系结构的概述，以及它如何与设备、Azure IoT 中心服务、Azure IoT 设备 SDK、Azure IoT 服务 SDK 和其他 Azure 服务相关。
author: dominicbetts
manager: timlt
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 09/15/2017
ms.author: dobett
ms.openlocfilehash: 56b7bfe02ddb0f2c909b2f363c37308527ec8db1
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37029079"
---
[!INCLUDE [iot-azure-and-iot](../../includes/iot-azure-and-iot.md)]

## <a name="next-steps"></a>后续步骤

Azure IoT Edge 是一项 Azure 服务，用于在边缘进行分析和数据处理。 有了 IoT Edge，就可以使用基于容器的代码来增强设备的功能，此类代码包括的逻辑适用于直接从已使用的 Azure 服务拉取数据；也可以使用你自己的特定于解决方案的代码。 它允许设备：

* 充当网关设备，聚合和处理多个叶设备的数据。
* 执行异常情况检测，对环境中的变化进行响应，不需等待来自云的指令。
* 对数据进行预处理，然后发送结果，尽量减少带宽和存储费用。 

IoT Edge 还包括一个云界面，用于对设备进行远程管理。 可以在不实际接触设备的情况下部署代码、监视状态并进行更新。 可以远程管理单个设备，也可以通过创建部署来影响大量已定义的设备。 有关详细信息，请参阅[了解单设备 IoT Edge 部署或大规模 IoT Edge 部署][lnk-deployment]。

若要了解启用 IoT Edge 所需的组件，请参阅 [Azure IoT Edge 工作原理][lnk-overview]。

如果以前没有使用过 Azure IoT 中心，则可能需要先阅读入门文章：[Azure IoT 中心服务概述][lnk-iot-hub]。

[lnk-deployment]: module-deployment-monitoring.md
[lnk-overview]: about-iot-edge.md
[lnk-iot-hub]: ../iot-hub/iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-iotdev]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: ../iot-hub/iot-hub-device-management-overview.md
