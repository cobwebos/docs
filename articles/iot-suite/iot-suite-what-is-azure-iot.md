---
title: "面向物联网的 Azure 解决方案（IoT 套件）| Microsoft 文档"
description: "Azure 的 IoT 概述包括预配置的解决方案、一个示例解决方案体系结构以及它如何与 Azure IoT 套件关联。"
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 437d2655-896f-4a9e-a4a8-b864790d3ef8
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: b0c27ca561567ff002bbb864846b7a3ea95d7fa3
ms.openlocfilehash: 1c8703d64ff45e589a7ce93f1f2176681e1bf331
ms.contentlocale: zh-cn
ms.lasthandoff: 04/25/2017

---
[!INCLUDE [iot-azure-and-iot](../../includes/iot-azure-and-iot.md)]

## <a name="azure-iot-suite"></a>Azure IoT 套件
Microsoft Azure IoT 套件是企业级解决方案，让用户能够通过一组可扩展的预配置解决方案快速入门。 这些解决方案用于处理常见 IoT 场景，例如[远程监视][lnk-preconfigured-solutions]、[预见性维护][lnk-predictive-maintenance]和[连接工厂][lnk-connected-factory]。 这些解决方案是本文所述的 IoT 解决方案体系结构的实现。

预配置的解决方案是可以正常工作的完整端到端解决方案，其中包括：

- 可帮助你入门的模拟设备。
- 预配置的 Azure 服务，例如 [Azure IoT 中心][Azure IoT Hub]、[Azure 事件中心][Azure Event Hubs]、[Azure 流分析][Azure Stream Analytics]、[Azure 机器学习][Azure Machine Learning]和 [Azure 存储][Azure storage]。
- 解决方案特定的管理控制台。

预配置的解决方案包含经过验证的可用于生产的代码，你可以自定义和扩展该代码以实现你自己的特定 IoT 场景。

你还可能对许多预配置的解决方案所使用的 [Azure IoT 中心][Azure IoT Hub]服务感兴趣。 [Azure IoT 中心][Azure IoT Hub]在预配置解决方案体系结构所使用的设备和云之间提供安全、可靠的双向通信。

## <a name="next-steps"></a>后续步骤
浏览以下资源以继续了解 IoT 套件和预配置解决方案：

* [Azure IoT 套件是什么？][lnk-whatissuite]
* [Azure IoT 套件预配置解决方案是什么？][lnk-whatarepreconfigured]

[lnk-whatissuite]: iot-suite-overview.md
[lnk-whatarepreconfigured]: iot-suite-what-are-preconfigured-solutions.md

[lnk-preconfigured-solutions]: iot-suite-getstarted-preconfigured-solutions.md
[Azure IoT Hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[Azure Event Hubs]: https://azure.microsoft.com/documentation/services/event-hubs/
[Azure Stream Analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[Azure Machine Learning]: https://azure.microsoft.com/documentation/services/machine-learning/
[Azure storage]: https://azure.microsoft.com/documentation/services/storage/
[lnk-predictive-maintenance]: iot-suite-predictive-overview.md
[lnk-connected-factory]: iot-suite-connected-factory-overview.md
