---
title: Azure Service Fabric 网格术语 | Microsoft Docs
description: 了解 Azure Service Fabric 网格的常用术语。
services: service-fabric-mesh
keywords: ''
author: dkkapur
ms.author: dekapur
ms.date: 11/28/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: 2d2661593ba3d9be2755d81803c8e248a2f7d0e1
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/21/2019
ms.locfileid: "58335804"
---
# <a name="service-fabric-mesh-terminology"></a>Service Fabric 网格术语

Azure Service Fabric 网格是一个完全托管的服务，由此开发者可部署微服务应用程序，而无需管理虚拟机、存储或网络。 本文详细介绍 Azure Service Fabric 网格所使用的术语，帮助理解文档中使用的术语。

## <a name="service-fabric"></a>Service Fabric

[Service Fabric](/azure/service-fabric/) 是一种分布式开源系统平台，通过它可轻松打包、部署和管理可缩放且可靠的微服务。 Service Fabric 是支持 Service Fabric 网格业务流程协调程序。 Service Fabric 提供可用于构建和运行微服务应用程序的选项。 可以使用任何框架编写服务，并从多个环境选项中选择运行应用程序的位置。

## <a name="application-and-service-concepts"></a>应用程序和服务概念

**Service Fabric 网格应用程序**：Service Fabric 网格应用程序由[资源模型](/azure/service-fabric-mesh/service-fabric-mesh-service-fabric-resources)（YAML 和 JSON 资源文件）描述，可以部署到运行 Service Fabric 的任何环境中。

**Service Fabric 本机应用程序**：Service Fabric 本机应用程序由[本机应用程序模型](/azure/service-fabric/service-fabric-application-model)（基于 XML 的应用程序和服务清单）描述。  Service Fabric 本机应用程序无法在 Service Fabric 网格中运行。

**应用程序**：Service Fabric 网格应用程序是网格应用程序的部署、版本控制和生存期的单位。 可以独立管理每个应用程序实例的生命周期。  应用程序由一个或多个服务代码包和设置组成。 应用程序是使用 Azure 资源模型 (RM) 架构定义的。  服务被描述为 RM 模板中的应用程序资源的属性。  应用程序使用的网络和卷由应用程序引用。  创建应用程序时，应用程序、服务、网络和卷均使用 Service Fabric 资源模型建模。

**服务**：应用程序中的服务代表微服务并执行完整且独立的功能。 每个服务由一个或多个代码包组成，这些代码包描述运行与代码包关联的容器映像所需的所有内容。  可以缩放应用程序中的服务副本数量。

**代码包**：代码包描述运行与代码包关联的容器映像所需的所有内容，包括以下内容：

* 容器名称、版本和注册表
* 每个容器所需的 CPU 和内存资源
* 网络终结点
* 要在引用单独卷资的源容器中装载的卷。

## <a name="deployment-and-application-models"></a>部署和应用程序模型 

若要部署服务，需要描述服务的运行方式。 Service Fabric 支持 3 种不同的部署模型：

### <a name="resource-model"></a>资源模型
Service Fabric 资源是可以单独部署到 Service Fabric 的任何内容，包括应用程序、服务、网络和卷。 资源是使用可以部署到集群终结点的 JSON 文件定义的。  对于 Service Fabric 网格，使用 Azure 资源模型架构。 YAML 文件架构还可用于更轻松地创作定义文件。 可将资源部署到 Service Fabric 运行的任何位置。 资源模型是用来描述 Service Fabric 应用程序的最简单方法。 其主要重点在于容器化服务的简单部署和管理。 要了解详细信息，请参阅 [Service Fabric 资源模型简介](/azure/service-fabric-mesh/service-fabric-mesh-service-fabric-resources)。

### <a name="native-model"></a>本机模块
本机应用程序模型为应用程序提供对 Service Fabric 的完整低级别访问权限。 应用程序和服务被定义为 XML 清单文件中的已注册类型。

本机模型支持 Reliable Services 框架，该框架提供对 C# 和 Java 中 Service Fabric 运行时 API 和群集管理 API 的访问权限。 本机模型还支持任意容器和可执行文件。

Service Fabric 网格环境中不支持本机模型。  有关详细信息，请参阅[编程模型概述](/azure/service-fabric/service-fabric-choose-framework)。

### <a name="docker-compose"></a>Docker Compose 
[Docker Compose](https://docs.docker.com/compose/) 是 Docker 项目的一部分。 Service Fabric 对使用 Docker Compose 模型部署应用程序提供有限支持。

## <a name="environments"></a>环境

Service Fabric 是一种开放源平台技术，多种不同的服务和产品都以它为基础。 Microsoft 提供以下选项：

 - **Service Fabric 网格**：一种完全托管服务，用于在 Microsoft Azure 中运行 Service Fabric 应用程序。
 - **Azure Service Fabric**：Azure 托管的 Service Fabric 群集服务/产品。 它提供 Service Fabric 和 Azure 基础结构之间的集成，以及 Service Fabric 群集的升级和配置管理。
 - **Service Fabric 独立**：一组安装和配置工具，可[在任何位置部署 Service Fabric 群集](/azure/service-fabric/service-fabric-deploy-anywhere)（在本地或任何云提供程序）。 不由 Azure 管理。
 - **Service Fabric 开发群集**：在 Windows、Linux 或 Mac 上提供本地开发经验，用于开发 Service Fabric 应用程序。

## <a name="environment-framework-and-deployment-model-support-matrix"></a>环境、框架和部署模型支持矩阵
不同的环境对框架和部署模型提供不同级别的支持。 下表介绍了支持的框架和部署模型组合。

| 应用程序类型 | 介绍依据 | Azure Service Fabric 网格 | Azure Service Fabric 群集（任何 OS）| 本地群集 | 独立群集 |
|---|---|---|---|---|---|
| Service Fabric 网格应用程序 | 资源模型（YAML 和 JSON） | 支持 |不支持 | Windows - 支持，Linux 和 Mac - 不支持 | Windows - 不支持 |
|Service Fabric 本机应用程序 | 本机应用程序模型 (XML) | 不支持| 支持|支持|Windows - 支持|

下表介绍了不同的应用模型以及针对 Service Fabric 为它们提供的工具。

| 应用程序类型 | 介绍依据 | Visual Studio | Eclipse | SFCTL | AZ CLI | Powershell|
|---|---|---|---|---|---|---|
| Service Fabric 网格应用程序 | 资源模型（YAML 和 JSON） | VS 2017 |不支持 |不支持 | 支持 - 仅网格环境 | 不支持|
|Service Fabric 本机应用程序 | 本机应用程序模型 (XML) | VS 2017 和 VS 2015| 支持|支持|支持|支持|

## <a name="next-steps"></a>后续步骤

若要详细了解 Service Fabric 网格，请阅读 [Service Fabric 网格概述](service-fabric-mesh-overview.md)。

找到[常见问题](service-fabric-mesh-faq.md)的答案。
