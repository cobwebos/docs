---
title: 使用 Dynamics 365 Field Service 连接 Azure IoT Central 应用程序 | Microsoft Docs
description: 了解如何使用 Azure IoT Central 和 Dynamics 365 Field Service 构建端到端解决方案
author: miriambrus
ms.author: miriamb
ms.date: 10/23/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.openlocfilehash: ae266495db1d6b94a43aa962a3e9b63a8115c526
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2020
ms.locfileid: "77017661"
---
# <a name="build-end-to-end-solution-with-azure-iot-central-and-dynamics-365-field-service"></a>使用 Azure IoT Central 和 Dynamics 365 Field Service 构建端到端解决方案 



构建者可以将 Azure IoT Central 应用程序集成到其他业务系统。 


例如，在连接的废弃物管理解决方案中，可以优化垃圾收集车的调度。 可以基于连接的垃圾桶中的 IoT 传感器数据进行优化。在 [IoT Central 连接的废弃物管理应用程序](./tutorial-connected-waste-management.md)中，可以配置规则和操作，并将其设置为触发在 Dynamics Field Service 中创建警报。 此方案是通过使用 Microsoft Flow 完成的，Microsoft Flow 将直接在 IoT Central 中进行配置，以便跨应用程序和服务自动执行工作流。 此外，基于现场服务中的服务活动，可以将信息发送回 Azure IoT Central。 

## <a name="how-to-connect-your-azure-iot-central-application-with-dynamics-365-field-services"></a>如何使用 Dynamics 365 Field Service 连接 Azure IoT Central 应用程序 

以下集成过程可以基于纯粹的配置体验轻松实现：
* Azure IoT Central 可以将有关设备异常的信息发送到连接的现场服务（作为 IoT 警报）进行诊断。
* 连接的现场服务可以创建从设备异常触发的案例或工作订单。
* 连接的现场服务可以安排技术人员进行检查，以防止停机事件发生。
* 可以使用相关服务和计划信息更新 Azure IoT Central 设备仪表板。


## <a name="next-steps"></a>后续步骤
* 详细了解[政府用 IoT Central 模板](./overview-iot-central-government.md)
* 详细了解 [IoT 中心](https://docs.microsoft.com/azure/iot-central/core/overview-iot-central)
* 详细了解 [Dynamics 365 Field Service](https://docs.microsoft.com/dynamics365/field-service/cfs-iot-overview)
