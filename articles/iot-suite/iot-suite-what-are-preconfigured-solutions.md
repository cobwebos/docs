---
title: "Azure IoT 套件预配置解决方案概述 | Microsoft Azure"
description: "描述 Azure IoT 套件预配置解决方案和体系结构，以及指向其他资源的链接。"
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 59009f37-9ba0-4e17-a189-7ea354a858a2
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/10/2017
ms.author: dobett
ms.openlocfilehash: 5c5fa4927073ff52418a940fce59ca1f6b57daa6
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2017
---
# <a name="what-is-azure-iot-suite"></a>Azure IoT 套件是什么？

Azure IoT 套件是一套具有下述特点的预配置解决方案：

* 快速部署
* 快速入门
* 按需自定义

IoT 套件预配置解决方案在设计时遵循相同的原则和目标。

## <a name="preconfigured-solutions-overview"></a>预配置解决方案概述

预配置解决方案是可以使用订阅部署到 Azure 的常见 IoT 解决方案模式的开源实现。 每个预配置解决方案都通过将自定义代码和 Azure 服务相结合来实现特定的 IoT 方案。 可以根据特定的要求自定义任何方案。 这些方案包括：

* 在功能丰富的仪表板上实现数据的可视化，以获取深度见解和解决方案状态。
* 通过实时 IoT 设备遥测配置规则和警报。
* 计划设备管理作业，例如软件和配置的更新。
* 预配自己的自定义物理或模拟设备。
* 在 IoT 设备组内排查和修复问题。

每个预配置解决方案都是可以使用模拟或物理设备生成遥测数据的完整端到端实现。 可以使用预配置解决方案作为解决方案加速器，以便：

* 为自己的 IoT 解决方案提供一个起点。
* 了解 IoT 解决方案设计和开发中的常见模式。

目前有三个预配置解决方案可用：

* [远程监视](iot-suite-remote-monitoring-explore.md)
* [前瞻性维护](iot-suite-predictive-overview.md)
* [连接工厂](iot-suite-connected-factory-overview.md)

下表显示了如何将解决方案映射到特定的 IoT 功能：

| 解决方案 | 数据引入 | 设备标识 | 设备管理 | 边缘处理 | 命令和控制 | 规则和操作 | 预测分析 |
| ------------------------------------------------------------ | -- | -- | -- | -- | -- | -- | -- |
| [远程监视](iot-suite-remote-monitoring-explore.md)  |是 |是 |是 |-   |是 |是 |-   |
| [前瞻性维护](iot-suite-predictive-overview.md)   |是 |是 |-   |-   |是 |是 |是 |
| [连接工厂](iot-suite-connected-factory-overview.md) |是 |是 |是 |是 |是 |是 |-   |

* *数据提取*：向云中大规模输入数据。
* *设备标识*：管理唯一设备标识，并控制对解决方案的设备访问权限。
* *设备管理*管理设备元数据，并执行设备重新启动和固件升级等操作。
* *命令和控制*：从云中向设备发送消息，使设备采取操作。
* *规则和操作*：解决方案后端使用规则作用于特定的设备到云的数据。
* *预测分析*：解决方案后端对设备到云数据进行分析，由此预测应何时采取特定操作。 例如，分析飞机发动机遥测数据来确定发动机维护何时到期。

> [!NOTE]
> 若要部署预配置解决方案并详细了解如何自定义该解决方案，请访问 [Microsoft Azure IoT 套件](https://www.azureiotsuite.com/)。

## <a name="azure-services"></a>Azure 服务

部署预配置解决方案时，预配过程可配置许多 Azure 服务。 下表显示了预配置解决方案中使用的服务：

|                      | 远程监视  | 预见性维护 | 连接工厂 |
| -------------------- | ------------------ | ---------------------- | ----------------- |
| IoT 中心              | 是                |                        | 是               |
| 事件中心           |                    | 是                    |                   |
| 时序见解 |                    |                        | 是               |
| 容器服务   | 是                |                        | 是               |
| 流分析     |                    | 是                    |                   |
| Web 应用             | 是                | 是                    | 是               |
| Cosmos DB            | 是                | 是                    | 是               |
| Azure 表         |                    | 是                    | 是               |

> [!NOTE]
> 若要详细了解在远程监视预配置解决方案中部署的资源，请参阅 GitHub 上的[此文](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/blob/master/README.md#basic-vs-standard-deployments)。

* [Azure IoT 中心](../iot-hub/index.md)。 该服务提供设备到云和云到设备的消息传送功能，并充当云和其他主要 IoT 套件服务的网关。 该服务允许从设备大量接收消息，并将命令发送给设备。 使用该服务，还能够[管理自己的设备](../iot-hub/iot-hub-device-management-overview.md)。 例如，可以配置、重启一个或多个连接到该中心的设备，或对其执行恢复出厂设置。
* [Azure 事件中心](../event-hubs/index.md)。 可以通过此服务将大量事件引入到云。 请参阅 [Azure IoT 中心与 Azure 事件中心的比较](../iot-hub/iot-hub-compare-event-hubs.md)。
* [Azure 时序见解](../time-series-insights/index.yml)。 预配置解决方案使用此服务分析和显示设备的遥测数据。
* [Azure 容器服务](../container-service/index.yml)。 此服务托管和管理预配置解决方案中的微服务。
* 用于数据存储的 [Azure Cosmos DB](../cosmos-db/index.yml) 和 [Azure 存储](../storage/index.yml)。
* [Azure 流分析](../stream-analytics/index.md)。 预测性维护预配置解决方案使用该服务来处理传入遥测、执行聚合以及检测事件。 此预配置解决方案也会使用流分析来处理包含数据（例如元数据或来自设备的命令响应）的信息消息。
* 用于在预配置解决方案中托管自定义应用程序代码的 [Azure Web 应用](../app-service/index.yml)。

有关典型 IoT 解决方案体系结构的概述，请参阅 [Microsoft Azure 和物联网 (IoT)](iot-suite-what-is-azure-iot.md)。

## <a name="whats-new-in-preconfigured-solutions"></a>预配置解决方案中的新功能

Microsoft 正将预配置解决方案更新为新的基于微服务的体系结构。 下表显示了预配置解决方案的当前状态：

| 预配置解决方案 | 体系结构  | 语言     |
| ---------------------- | ------------- | ------------- |
| 远程监视      | 微服务 | Java 和 .NET |
| 预见性维护 | MVC           | .NET          |
| 连接工厂      | MVC           | .NET          |

以下部分介绍基于微服务的预配置解决方案的新功能：

### <a name="microservices"></a>微服务

新版远程监视预配置解决方案使用微服务体系结构。 此预配置解决方案由多个微服务（例如 IoT 中心管理器和存储管理器）组成。 每个微服务的 Java 和 .NET 版及其相关的开发人员文档均可供下载。 有关微服务的详细信息，请参阅[远程监视体系结构](iot-suite-remote-monitoring-sample-walkthrough.md)。

此微服务体系结构是经验证的适用于云解决方案的模式，具有以下特点：

* 可缩放。
* 启用扩展性。
* 易于理解。
* 允许将单个服务替换出来。

> [!TIP]
> 若要详细了解微服务体系结构，请参阅 [.NET Application Architecture](https://www.microsoft.com/net/learn/architecture)（.NET 应用程序体系结构）和 [Microservices: An application revolution powered by the cloud](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/)（微服务：由云推动的应用程序革命）。

在部署新版远程监视时，必须选择以下部署选项之一：

* **基本：**降低成本版，用于演示或部署测试。 所有微服务都部署到一个 Azure 虚拟机。
* **标准：**扩展的基础结构部署，适用于开发生产型部署。 Azure 容器服务将微服务部署到多个 Azure 虚拟机。 Kubernetes 会协调托管单个微服务的 Docker 容器。

### <a name="language-choices-java-and-net"></a>语言选择：Java 和 .NET

Java 和 .NET 中提供了每个微服务的实现。 与 .NET 代码一样，Java 源代码是开源的，可以根据具体要求进行自定义：

* [远程监视 .NET GitHub 存储库](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [远程监视 Java GitHub 存储库](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)

若要查看其他语言实现，请在 [Azure IoT 用户之声](https://feedback.azure.com/forums/321918-azure-iot)中提交请求。

### <a name="react-user-interface-framework"></a>React 用户界面框架

UI 是使用 [React](https://facebook.github.io/react/) javascript 库生成的。 源代码是开源的，可供下载和自定义。

## <a name="next-steps"></a>后续步骤

大致了解 IoT 套件预配置解决方案以后，即可针对每个预配置解决方案执行建议的后续步骤：

* [浏览 Azure IoT 套件远程监视解决方案资源管理器部署模型](iot-suite-remote-monitoring-explore.md)。
* [预测性维护预配置解决方案概述](iot-suite-predictive-overview.md)。
* [连接工厂预配置解决方案入门](iot-suite-connected-factory-overview.md)。

有关 IoT 解决方案体系结构的详细信息，请参阅 [Microsoft Azure IoT 服务：参考体系结构](http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf)。
