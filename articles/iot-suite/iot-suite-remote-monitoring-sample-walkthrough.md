---
title: 远程监视解决方案的体系结构 - Azure | Microsoft Docs
description: 有关远程监视解决方案加速器的体系结构的演练。
services: iot-suite
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 31fe13af-0482-47be-b4c8-e98e36625855
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/10/2017
ms.author: dobett
ms.openlocfilehash: 71b0405975d88e69d3ecea6090983bc3c779395d
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/18/2018
---
# <a name="remote-monitoring-solution-accelerator-architecture"></a>远程监视解决方案加速器体系结构

远程监视[解决方案加速器](../iot-accelerators/iot-accelerators-what-are-solution-accelerators.md)针对远程位置中的多台计算机实现端到端监视解决方案。 该解决方案结合了关键 Azure 服务来提供业务方案的通用实现。 可将其用作自己实现的起点，并可以根据特定的业务要求[自定义](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md)该解决方案。

本文将逐步讲解远程监视解决方案的一些关键要素，以帮助你了解其工作原理。 这一知识有助于：

* 排查解决方案中的问题。
* 规划如何根据具体要求自定义该解决方案。
* 设计自己的 IoT 解决方案，以使用 Azure 服务。

## <a name="logical-architecture"></a>逻辑体系结构

下图描绘了叠加在 [IoT 体系结构](../iot-accelerators/iot-accelerators-what-is-azure-iot.md)之上的远程监视解决方案加速器的逻辑组件：

![逻辑体系结构](media/iot-suite-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)

## <a name="why-microservices"></a>为何使用微服务？

自 Microsoft 发布第一款解决方案加速器以来，云体系结构已经演变。 [微服务](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/)应运而生，经证实能够在不降低开发速度的情况下实现可伸缩性和灵活性。 有多种 Microsoft 服务在内部使用此体系结构模式，且获得了出色的可靠性和可伸缩性。 更新的解决方案加速器将这些知识付诸实践，使我们也能从中受益。

> [!TIP]
> 若要详细了解微服务体系结构，请参阅 [.NET Application Architecture](https://www.microsoft.com/net/learn/architecture)（.NET 应用程序体系结构）和 [Microservices: An application revolution powered by the cloud](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/)（微服务：由云推动的应用程序革命）。

## <a name="device-connectivity"></a>设备连接

该解决方案在逻辑体系结构的设备连接部分中包含以下组件：

### <a name="simulated-devices"></a>模拟设备

该解决方案包含一个微服务，使用该微服务可以管理用于在解决方案中测试端到端流的模拟设备池。 模拟设备：

* 生成设备到云的遥测数据。
* 响应来自 IoT 中心的云到设备方法调用。

该微服务提供一个 RESTful 终结点用于创建、启动和停止模拟。 每个模拟由一组不同类型的的虚拟设备构成，这些设备可发送遥测数据和响应方法调用。

可以通过解决方案门户中的仪表板预配模拟设备。

### <a name="physical-devices"></a>物理设备

可将物理设备连接到解决方案。 可以使用 Azure IoT 设备 SDK 实现模拟设备的行为。

可以通过解决方案门户中的仪表板预配物理设备。

### <a name="iot-hub-and-the-iot-manager-microservice"></a>IoT 中心和 IoT 管理器微服务

[IoT 中心](../iot-hub/index.yml)引入从设备发送到云中的数据，并将其提供给 `telemetry-agent` 微服务使用。

此外，解决方案中的 IoT 中心还可以：

* 维护一个标识注册表，用于存储允许连接到门户的所有设备的 ID 和身份验证密钥。 可以通过标识注册表启用和禁用设备。
* 代表解决方案门户在设备上调用方法。
* 维护所有已注册设备的设备孪生。 设备孪生存储设备报告的属性值。 设备孪生还存储解决方案门户中设置的所需属性，供设备在后续连接时检索。
* 计划作业，为多个设备设置属性或者在多个设备上调用方法。

该解决方案包含用于处理与 IoT 中心之间的交互的 `iot-manager` 微服务，这些交互包括：

* 创建和管理 IoT 设备。
* 管理设备孪生。
* 在设备上调用方法。
* 管理 IoT 凭据。

此服务还运行 IoT 中心查询，以检索属于用户定义组的设备。

该微服务提供一个 RESTful 终结点用于管理设备和设备孪生、调用方法和运行 IoT 中心查询。

## <a name="data-processing-and-analytics"></a>数据处理和分析

该解决方案在逻辑体系结构的数据处理和分析部分中包含以下组件：

### <a name="device-telemetry"></a>设备遥测

该解决方案包含两个微服务用于处理设备遥测。

[telemetry-agent](https://github.com/Azure/telemetry-agent-dotnet) 微服务：

* 在 Cosmos DB 中存储遥测数据。
* 分析来自设备的遥测数据流。
* 根据定义的规则生成警报。

警报存储在 Cosmos DB 中。

`telemetry-agent` 微服务使解决方案门户能够读取设备发送的遥测数据。 解决方案门户还使用此服务：

* 定义监视规则，例如触发警报的阈值
* 检索以往警报的列表。

使用此微服务提供的 RESTful 终结点来管理遥测、规则和警报。

### <a name="storage"></a>存储

[storage-adapter](https://github.com/Azure/pcs-storage-adapter-dotnet) 微服务是解决方案加速器使用的主存储服务前面的适配器。 它提供简单的集合和键值存储。

解决方案加速器的标准部署使用 Cosmos DB 作为其主存储服务。

Cosmos DB 数据库将数据存储在解决方案加速器中。 **storage-adapter** 微服务充当解决方案中其他微服务的适配器，用于访问存储服务。

## <a name="presentation"></a>呈现

该解决方案在逻辑体系结构的呈现部分中包含以下组件：

[Web 用户界面是一个 React Javascript 应用程序](https://github.com/Azure/pcs-remote-monitoring-webui)。 该应用程序：

* 仅使用 Javascript React，完全在浏览器中运行。
* 采用 CSS 样式。
* 通过 AJAX 调用与面向公众的微服务交互。

用户界面呈现了解决方案加速器的所有功能，并与如下所述的其他服务交互：

* 用于保护用户数据的 [authentication](https://github.com/Azure/pcs-auth-dotnet) 微服务。
* 用于列出和管理 IoT 设备的 [Iothub manager](https://github.com/Azure/iothub-manager-dotnet) 微服务。

使用户界面能够存储和检索配置设置的 [ui-config](https://github.com/Azure/pcs-config-dotnet) 微服务。

## <a name="next-steps"></a>后续步骤

如果想要浏览源代码和开发人员文档，请从以下两个主要 GitHub 存储库中的一个入手：

* [使用 Azure IoT 进行远程监视的解决方案加速器 (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/)。
* [使用 Azure IoT 进行远程监视的解决方案加速器 (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)。
* [用于远程监视的解决方案加速器（体系结构）](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Architecture)。

有关远程监视解决方案加速器的更多概念性信息，请参阅[自定义解决方案加速器](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md)。
