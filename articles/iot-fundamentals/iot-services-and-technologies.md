---
title: Azure 物联网 (IoT) 技术和解决方案
description: 提供适用于生成 Azure IoT 解决方案的 PaaS/SaaS 技术和解决方案的集合。
author: BryanLa
manager: timlt
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: overview
ms.date: 05/18/2018
ms.author: bryanla
ms.openlocfilehash: e875ec599323b3d037ba704d4cc54a48ff9b1b57
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34631405"
---
# <a name="azure-iot-technologies-and-solutions-paas-and-saas"></a>Azure IoT 技术和解决方案：PaaS 和 SaaS

Microsoft 构建的项目组合支持所有客户的需求，让所有人都能够获得数字化转型带来的好处。 Azure IoT 产品组合概述了可用的 PaaS/SaaS 技术和解决方案。 它提供两种适用于生成你自己的解决方案的途径：
- 平台即服务 (PaaS)：使用 [Azure IoT 解决方案加速器](https://www.azureiotsolutions.com/)开发应用程序，该加速器集合了企业级的预配置解决方案，可以加速自定义 IoT 解决方案的开发。
- 软件即服务 (SaaS)：通过 [Azure IoT Central](https://azure.microsoft.com/services/iot-central/) 这种新的 SaaS 解决方案进行快速入门，以便开发 IoT 应用程序，不需接触复杂的 IoT 解决方案。 如果因组织缺少相关的资源而无法生成你自己的 IoT 解决方案，则可使用 Azure IoT Central 这种无代码的 IoT 解决方案，在数分钟内创建设备模型、仪表板和规则。

![Azure IoT 技术和解决方案][img-paas-saas-technologies-solutions]

## <a name="solutions"></a>解决方案

通过解决方案加速器和 SaaS 产品/服务进行快速入门。 从预配置的解决方案中进行选择，以便通过常见的 IoT 方案（例如远程监视、预测性维护、连接工厂）创建可完全自定义的解决方案。 也可使用 Azure IoT Central 这种可完全托管的端到端解决方案，在不需云解决方案专业知识的情况下打造出强大的 IoT 方案。

### <a name="azure-iot-solution-accelerators-paas"></a>Azure IoT 解决方案加速器 (PaaS)

Azure IoT 解决方案加速器是可自定义 PaaS 解决方案，可以对 IoT 解决方案进行高级控制。 如果你的企业所实现的 IoT 是针对连接操作的，或者所实施的特定自定义要求是针对连接产品的，则可使用 Azure IoT 解决方案加速器，因为它提供你所需要的控制。 

公司（例如设备或设备模型数量多的组织以及寻求连接工厂解决方案的制造商）可以充分利用 IoT 解决方案加速器。 IoT 解决方案加速器创建可以根据复杂需求进行高度自定义的解决方案，并提供： 

- 预生成的解决方案
    - 远程监视
    - 连接工厂
    - 预见性维护
    - 设备模拟
- 快速部署的功能
- 缩短价值实现时间的功能
- 进行终极控制的解决方案 
 
### <a name="azure-iot-central-saas"></a>Azure IoT Central (SaaS)

Azure IoT Central 是完全托管的 SaaS 解决方案，可以让你在没有多少 IoT 经验的情况下快速入门。 如果你的企业更多的是追求速度而不是自定义，则 SaaS 模型可能是最适合你的 IoT 实现需求的。 

设备模型较少、可预测方案较多且 IoT/IT 功能受限的组织现在可以通过 SaaS 方法来利用 IoT。 以前缺少时间、资金和专业技术来开发连接产品的企业现在可以通过 Azure IoT Central 来快速启动产品开发。 Microsoft 是行业先锋，其提供的成熟的 SaaS 解决方案可以满足常见的 IoT 实现需求。 

- 完全托管的 IoT SaaS
- 不需云解决方案开发专业技术
- 可以按需配置
- 适合直截了当的 IoT 需求

### <a name="compare-azure-iot-solution-accelerators-and-azure-iot-central"></a>比较 Azure IoT 解决方案加速器和 Azure IoT Central

若要实现典型的 [IoT 解决方案体系结构](/azure/iot-fundamentals/iot-introduction#iot-solution-architecture)，可使用 Azure IoT 提供的几个选项（[Azure IoT 解决方案加速器](/azure/iot-suite)和 [Azure IoT Central](https://www.microsoft.com/internet-of-things/iot-central-saas-solutions)），每个选项适用于不同组合的客户需求。

[Azure IoT 中心](https://azure.microsoft.com/services/iot-hub/)是核心的 Azure PaaS，可供 Azure IoT Central 和 Azure IoT 解决方案加速器使用。 可以通过 IoT 中心在数百万个 IoT 设备和一个云解决方案之间实现安全可靠的双向通信。 可以借助 IoT 中心克服 IoT 实现难题，例如：

* 大容量设备连接和管理。
* 大容量遥测引入。
* 设备的命令和控制。
* 设备安全措施实施。

选择 Azure IoT 产品是规划 IoT 解决方案的重要一环。 IoT 中心是单项 Azure 服务，其本身不提供端到端 IoT 解决方案。 任何 IoT 解决方案都可以从 IoT 中心入手，不需使用 Azure IoT 解决方案加速器或 Azure IoT Central 即可使用它。 Azure IoT 解决方案加速器和 Azure IoT Central 都使用 IoT 中心以及其他 Azure 服务。 下表汇总了 Azure IoT 解决方案加速器和 Azure IoT Central 之间的主要差异，有助于根据要求进行正确的选择：

|                        | Azure IoT 解决方案加速器 | Azure IoT Central |
| ---------------------- | --------- | ----------- |
| 主要用途 | 加速开发需要最大灵活性的自定义 IoT 解决方案。 | 缩短不需深层服务自定义的直观 IoT 解决方案的面市时间。 |
| 访问基础 PaaS 服务          | 有权访问基础 Azure 服务，根据需要对其进行管理或替换。 | SaaS。 完全托管的解决方案，基础服务不公开。 |
| 灵活性            | 高。 微服务的代码是开源的，可以任意修改。 此外还可以自定义部署基础结构。| 中。 可以根据内置的基于浏览器的用户体验来自定义解决方案模型和 UI 的各个方面。 基础结构不可自定义，因为未公开各种不同的组件。|
| 技能级别                 | 中-高。 需要 Java 或 .NET 技能才能自定义解决方案后端。 需要 JavaScript 技能才能自定义可视化效果。 | 低。 需要建模技能才能自定义解决方案。 不需编码技能。 |
| 入门体验 | 解决方案加速器可实现常见的 IoT 方案。 可以在数分钟内部署。 | 应用程序模板和设备模板提供预建的模型。 可以在数分钟内部署。 |
| 定价                | 可以通过微调服务来控制成本。 | 简单且可预测的定价结构。 |

使用哪种产品来生成 IoT 解决方案最终取决于：

* 业务需求。
* 要生成的解决方案的类型。
* 组织生成解决方案并进行长期维护所需的技能组合。

## <a name="technologies-paas"></a>技术 (PaaS)

跨 Azure 平台的平台即服务 (PaaS) 技术提供最广泛的 IoT 平台服务组合，可以用来轻松地创建、自定义和控制 IoT 解决方案的各个方面。 与数十亿 IoT 设备建立双向通信，并对 IoT 设备进行大规模管理。 然后将 IoT 设备数据与其他平台服务（例如 Azure Cosmos DB 和 Azure 时序见解）集成，增强跨整个解决方案获取见解的功能。 

### <a name="device-support"></a>设备支持

可以利用 [Azure IoT 初学者套件](https://catalog.azureiotsuite.com/kits)或者从[设备目录](http://catalog.azureiotsuite.com/)的数百个 IoT 认证设备中进行选择，充满自信地开始你的 IoT 项目。 所有设备都与平台无关，并通过相关测试，可无缝连接到 IoT 中心。
使用开源[设备 SDK](/azure/iot-hub/iot-hub-devguide-sdks) 将所有设备连接到 Azure IoT。 SDK 支持多种操作系统（例如 Linux、Windows 和实时操作系统）和多种编程语言（例如 [C](https://github.com/Azure/azure-iot-sdk-c)、[Node.js](https://github.com/Azure/azure-iot-sdk-node)、[Java](https://github.com/Azure/azure-iot-sdk-java)、[.NET](https://github.com/Azure/azure-iot-sdk-csharp) 和 [Python](https://github.com/Azure/azure-iot-sdk-python)）。

### <a name="iot"></a>IoT 
[Azure IoT 中心](https://azure.microsoft.com/services/iot-hub/)是一项完全托管的服务，可在数百万个 IoT 设备和一个解决方案后端之间实现安全可靠的双向通信。 Azure IoT 中心设备预配服务是针对 IoT 中心的帮助程序服务，无需人为干预即可零接触恰时预配至合适的 IoT 中心，方便客户采用安全且可缩放的方式预配数百万台设备。

### <a name="edge"></a>Microsoft Edge
[Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) 是一种物联网 (IoT) 服务。 此服务供想要在设备上 （即“边缘设备上”）分析数据的客户使用。 将部分工作负荷移到边缘设备可以降低延迟，并且可以选择使用脱机方案。


### <a name="data-and-analytics"></a>数据和分析
充分利用 IoT 解决方案中的一系列 Azure 数据和分析 PaaS 产品/服务，包括使用 Azure 机器学习将云智能引入边缘设备、使用 Azure Data Lake 以经济高效的方式存储 IoT 设备数据，以及使用 [Azure 时序见解](https://azure.microsoft.com/services/time-series-insights/)可视化 IoT 设备的海量数据。

### <a name="visualization-and-integration"></a>可视化和集成
Microsoft Azure 提供完整的云解决方案，其中结合了持续成长的集成式云服务集合，通过行业领先的承诺确保数据的安全和隐私。 详细了解 [Microsoft Azure](https://azure.microsoft.com/)。





## <a name="next-steps"></a>后续步骤

查看[入门](/azure/iot-hub/iot-hub-get-started)部分，轻松快速地体验 IoT 功能。 或者尝试[教程](/azure/iot-edge/tutorial-simulate-device-windows)之一，进行更深入的实践体验。

[img-paas-saas-technologies-solutions]: media/iot-comparison/paas-saas-technologies-solutions.png

