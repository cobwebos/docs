---
title: 远程监视解决方案体系结构选择 - Azure | Microsoft Docs
description: 本文介绍在远程监视中所做的体系结构和技术选择
services: iot-suite
suite: iot-suite
author: timlaverty
manager: camerons
ms.author: timlav
ms.service: iot-suite
ms.date: 04/30/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 192a763c01e60d816ae2046587176627fc9d8736
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
ms.locfileid: "33781546"
---
# <a name="remote-monitoring-architectural-choices"></a>远程监视体系结构选择

Azure IoT 远程监视 (RM) 是一种经 MIT 许可的开源解决方案加速器，其引入的常用 IoT 方案（例如设备连接、设备管理、流处理）有助于客户加速开发过程。  RM 遵循建议的 Azure IoT 参考体系结构（发布在[此处](https://azure.microsoft.com/updates/microsoft-azure-iot-reference-architecture-available/)）。  

本文介绍了在 RM 的每个子系统中所做的体系结构和技术选择，并对考虑采取的替代选择进行了讨论。  必须指出，在 RM 中所做的技术选择不是实现远程监视 IoT 解决方案的唯一方法。  技术实现是开发成功应用程序的基线，应根据客户解决方案实现的技能、体验和垂直应用程序需求进行修改。

## <a name="architectural-choices"></a>体系结构选择

### <a name="microservices-serverless-and-cloud-native"></a>微服务、无服务器和云原生

建议用于 IoT 应用程序的体系结构的基础是云原生、微服务和无服务器。  IoT 应用程序的不同子系统应作为不同的服务来生成，这些服务可以独立部署，还可以独立缩放。  有了这些属性，就可以提高规模，在更新单个子系统时更加灵活，并可根据子系统灵活地选择适当的技术。  微服务可以通过多种技术来实现。 例如，可以将容器技术（如 Docker）与无服务器技术（如 Azure Functions）配合使用，也可以在 PaaS 服务（如 Azure 应用服务）中托管微服务。

## <a name="core-subsystem-technology-choices"></a>核心子系统技术选择

此部分详述在 RM 中针对每个核心子系统所做的技术选择。

![核心关系图](media/iot-suite-remote-monitoring-architectural-choices/subsystem.png) 

### <a name="cloud-gateway"></a>云网关
Azure IoT 中心用作 RM 云网关。  IoT 中心提供与设备进行的安全、双向通信。 可以在[此处](https://azure.microsoft.com/services/iot-hub/)详细了解 IoT 中心。 进行 IoT 设备连接时，使用 .NET Core 和 Java IoT 中心 SDK。  这些 SDK 提供 IoT 中心 REST API 的包装器，并处理包括重试在内的各种方案。 

### <a name="stream-processing"></a>流处理
对于流处理，RM 使用 Azure 流分析进行复杂的规则处理。  对于希望规则更简单的客户，我们也提供了可以处理简单规则的自定义微服务，虽然此设置不包含在现成的部署中。 参考体系结构建议使用 Azure Functions 进行简单的规则处理，使用 Azure 流分析 (ASA) 进行复杂的规则处理。  

### <a name="storage"></a>存储
至于存储，可以使用 Cosmos DB 来应对所有存储需求：冷存储、暖存储、规则存储、警报。 我们目前正按照参考体系结构的建议转移到 Azure Blob 存储。  Cosmos DB 是建议用于 IoT 应用程序的常规用途暖存储解决方案，虽然 Azure 时序见解和 Azure Data Lake 等解决方案也适用于许多情况。

### <a name="business-integration"></a>业务集成
RM 中的业务集成仅限放置在暖存储中的警报的生成。 将解决方案与 Azure 逻辑应用集成以后，即可执行更多的业务集成。

### <a name="user-interface"></a>用户界面
此 Web UI 是使用 JavaScript React 生成的。  React 提供常用的行业 Web UI 框架，类似于其他常用的框架（例如 Angular）。  

### <a name="runtime-and-orchestration"></a>运行时和业务流程
在 RM 中针对子系统实现选择的应用程序运行时为 Docker 容器，其使用的 Kubernetes (K8s) 充当进行水平缩放所需的业务流程协调程序。  此体系结构允许按子系统进行单个的缩放定义，但从安全角度来看，这样做需不断更新 VM 和容器，因此会产生 DevOps 成本。  Docker 和 K8s 的替代方法包括在 PaaS 服务（例如 Azure 应用服务）中托管微服务，或者使用 Service Fabric, DCOS、Swarm 等作为业务流程协调程序。

## <a name="next-steps"></a>后续步骤
* 部署[此处](https://www.azureiotsuite.com/)的 RM 解决方案。
* 浏览 [C#](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) 和 [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java/) 格式的 GitHub 代码。  
* 在[此处](https://azure.microsoft.com/updates/microsoft-azure-iot-reference-architecture-available/)详细了解 IoT 参考体系结构。
