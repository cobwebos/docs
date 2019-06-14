---
title: 远程监视解决方案加速器概述 - Azure | Microsoft Docs
description: 远程监视解决方案加速器概述。
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: af09ea39f373d518d5600e3fa46adc378fd9236d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "61442524"
---
# <a name="remote-monitoring-solution-accelerator-overview"></a>远程监视解决方案加速器概述

远程监视[解决方案加速器](../iot-accelerators/about-iot-accelerators.md)针对远程位置中的多台计算机实现端到端监视解决方案。 该解决方案结合了关键 Azure 服务来提供业务方案的通用实现。 可将其用作自己实现的起点，并可以根据特定的业务要求[自定义](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md)该解决方案。

本文将逐步讲解远程监视解决方案的一些关键要素，以帮助你了解其工作原理。 这一知识有助于：

* 排查解决方案中的问题。
* 规划如何根据具体要求自定义该解决方案。
* 设计使用 Azure 服务的 IoT 解决方案。

GitHub 上提供了远程监视解决方案加速器代码：

* [.NET](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)

## <a name="logical-architecture"></a>逻辑体系结构

下图描绘了叠加在 [IoT 体系结构](../iot-fundamentals/iot-introduction.md)之上的远程监视解决方案加速器的逻辑组件：

![逻辑体系结构](./media/iot-accelerators-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)

## <a name="why-microservices"></a>为何使用微服务？

自 Microsoft 发布第一款解决方案加速器以来，云体系结构已有所演变。 [微服务](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/)应运而生，经证实能够在不降低开发速度的情况下实现可伸缩性和灵活性。 有多种 Microsoft 服务在内部使用此体系结构模式，且获得了出色的可靠性和可伸缩性。 更新的解决方案加速器将这些知识付诸实践，使我们也能从中受益。

> [!TIP]
> 若要详细了解微服务体系结构，请参阅 [.NET 应用程序体系结构](https://www.microsoft.com/net/learn/architecture)和[微服务：由云驱动的应用程序革命](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/)。

## <a name="device-connectivity"></a>设备连接

该解决方案在逻辑体系结构的设备连接部分中包含以下组件：

### <a name="real-devices"></a>实际设备

可将实际设备连接到解决方案。 可以使用 Azure IoT 设备 SDK 实现模拟设备的行为。

可以通过解决方案门户中的仪表板预配实际设备。

### <a name="device-simulation-microservice"></a>设备模拟微服务

该解决方案包含[设备模拟微服务](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/device-simulation)，使用该微服务可以从解决方案门户模拟设备池以在解决方案中测试端到端流。 模拟设备：

* 生成设备到云的遥测数据。
* 响应来自 IoT 中心的云到设备方法调用。

该微服务提供一个 RESTful 终结点用于创建、启动和停止模拟。 每个模拟由一组不同类型的的虚拟设备构成，这些设备可发送遥测数据和响应方法调用。

可以通过解决方案门户中的仪表板预配模拟设备。

### <a name="iot-hub"></a>IoT 中心

[IoT 中心](../iot-hub/index.yml)将从实际和模拟设备发送的遥测数据引入到云中。 IoT 中心使遥测数据可供 IoT 解决方案后端中的服务进行处理。

此外，解决方案中的 IoT 中心还可以：

* 维护一个标识注册表，用于存储允许连接到门户的所有设备的 ID 和身份验证密钥。
* 代表解决方案加速器在设备上调用方法。
* 维护所有已注册设备的设备孪生。 设备孪生存储设备报告的属性值。 设备孪生还存储解决方案门户中设置的所需属性，供设备在后续连接时检索。
* 计划作业，为多个设备设置属性或者在多个设备上调用方法。

## <a name="data-processing-and-analytics"></a>数据处理和分析

该解决方案在逻辑体系结构的数据处理和分析部分中包含以下组件：

### <a name="iot-hub-manager-microservice"></a>IoT 中心管理器微服务

解决方案包含用于处理与 IoT 中心之间的交互的 [IoT 中心管理器微服务](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/iothub-manager)，这些交互包括：

* 创建和管理 IoT 设备。
* 管理设备孪生。
* 在设备上调用方法。
* 管理 IoT 凭据。

此服务还运行 IoT 中心查询，以检索属于用户定义组的设备。

该微服务提供一个 RESTful 终结点用于管理设备和设备孪生、调用方法和运行 IoT 中心查询。

### <a name="device-telemetry-microservice"></a>设备遥测数据微服务

[设备遥测数据微服务](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/device-telemetry)提供 RESTful 终结点，以便对存储在时序见解中的设备遥测数据进行读取访问。 通过 RESTful 终结点还可以针对存储中的警报定义，对规则和读/写访问实现 CRUD 操作。

### <a name="storage-adapter-microservice"></a>存储适配器微服务

[存储适配器微服务](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/storage-adapter)管理键值对，从而抽象化存储服务语义，并提供了一个简单接口来使用 Azure Cosmos DB 存储任何格式的数据。

值在集合中进行组织。 可以处理单个值，也可以提取整个集合。 复杂数据结构由客户端进行序列化，并作为简单文本有效负载进行管理。

该服务提供一个 RESTful 终结点，用于对键值对实现 CRUD 操作。 值

### <a name="azure-cosmos-db"></a>Azure Cosmos DB

解决方案加速器部署使用 [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/) 存储规则、警报、配置设置和所有其他冷存储。

### <a name="azure-stream-analytics-manager-microservice"></a>Azure 流分析管理器微服务

[Azure 流分析管理器微服务](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/asa-manager)管理 Azure 流分析 (ASA) 作业，包括设置其配置、启动和停止它们以及监视其状态。

ASA 作业由两个引用数据集进行支持。 一个数据集定义规则，另一个定义设备组。 规则引用数据从设备遥测数据微服务管理的信息生成。 Azure 流分析管理器微服务将遥测数据规则转换为流处理逻辑。

设备组引用数据用于标识应用于传入遥测数据消息的规则组。 设备组由配置微服务进行管理，并使用 Azure IoT 中心设备孪生查询。

ASA 作业将来自已连接设备的遥测数据提供给时序见解以便进行存储和分析。

### <a name="azure-stream-analytics"></a>Azure 流分析

[Azure 流分析](https://docs.microsoft.com/azure/stream-analytics/)是一个事件处理引擎，用于检查从设备流式传输的大量数据。

### <a name="azure-time-series-insights"></a>Azure Time Series Insights

[Azure 时序见解](https://docs.microsoft.com/azure/time-series-insights/)存储来自连接到解决方案加速器的设备的遥测数据。 通过它还可在解决方案 Web UI 中显示和查询设备遥测数据。

> [!NOTE]
> 时序见解目前在 Azure 中国云中不可用。 Azure 中国云中的新远程监视解决方案加速器部署使用 Cosmos DB 进行所有存储。

### <a name="configuration-microservice"></a>配置微服务

[配置微服务](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/config)提供一个 RESTful 终结点，用于对解决方案加速器中的设备组、解决方案设置和用户设置实现 CRUD 操作。 它与存储适配器微服务一起用于保持配置数据。

### <a name="authentication-and-authorization-microservice"></a>身份验证和授权微服务

[身份验证和授权微服务](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/auth)管理被授权访问解决方案加速器的用户。 可以使用任何支持 [OpenId Connect](https://openid.net/connect/) 的标识服务提供程序进行用户管理。

### <a name="azure-active-directory"></a>Azure Active Directory

解决方案加速器部署使用 [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) 作为 OpenID Connect 提供程序。 Azure Active Directory 存储用户信息，并提供证书来验证 JWT 令牌签名。

## <a name="presentation"></a>呈现

该解决方案在逻辑体系结构的呈现部分中包含以下组件：

[Web 用户界面是一个 React Javascript 应用程序](https://github.com/Azure/pcs-remote-monitoring-webui)。 该应用程序：

* 仅使用 Javascript React，完全在浏览器中运行。
* 采用 CSS 样式。
* 通过 AJAX 调用与面向公众的微服务交互。

用户界面呈现了解决方案加速器的所有功能，并与如下所述的其他微服务交互：

* 用于保护用户数据的身份验证和授权微服务。
* 用于列出和管理 IoT 设备的 IoT 中心管理器微服务。

用户界面集成 Azure 时序见解资源管理器以实现设备遥测数据的查询和分析。

配置微服务使用户界面可以存储和检索配置设置。

## <a name="next-steps"></a>后续步骤

如果想要浏览源代码和开发人员文档，请从以下两个 GitHub 存储库中的一个入手：

* [使用 Azure IoT 进行远程监视的解决方案加速器 (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)。
* [使用 Azure IoT 进行远程监视的解决方案加速器 (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)。

详细的解决方案体系结构示意图：
* [用于远程监视体系结构的解决方案加速器](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Architecture)。

有关远程监视解决方案加速器的更多概念性信息，请参阅[自定义解决方案加速器](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md)。
