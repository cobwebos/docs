---
title: 了解 Azure Service Fabric 术语
description: 了解本文其余部分中使用的关键 Service Fabric 术语和概念。
author: masnider
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: masnider
ms.custom: sfrev
ms.openlocfilehash: a9266c2a8d2ad179cfdb12e367a14f37d1abc9b3
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2020
ms.locfileid: "76024891"
---
# <a name="service-fabric-terminology-overview"></a>Service Fabric 术语概述

Azure Service Fabric 是一种分布式系统平台，适用于打包、部署和管理可缩放的可靠微服务。  可以[在任何位置托管 Service Fabric 群集](service-fabric-deploy-anywhere.md)：Azure、本地数据中心或任何云提供程序。  Service Fabric 是支持 [Azure Service Fabric 网格](/azure/service-fabric-mesh)的业务流程协调程序。 可以使用任何框架编写服务，并从多个环境选项中选择运行应用程序的位置。 本文详细介绍 Service Fabric 所使用的术语，帮助了解文档中使用的术语。

## <a name="infrastructure-concepts"></a>基础结构概念

**群集**：一组通过网络连接在一起的虚拟机或物理计算机，微服务会在其中部署和管理。  群集可以扩展到成千上万台计算机。

节点：属于群集一部分的计算机或 VM 称为节点。 为每个节点分配一个节点名称（字符串）。 节点具有各种特征，如放置属性。 每个计算机或 VM 都有一个自动启动 Windows 服务 `FabricHost.exe`，此服务在引导时开始运行，并启动两个可执行文件：`Fabric.exe` 和 `FabricGateway.exe`。 这两个可执行文件构成了节点。 在测试方案中，可以通过运行 `Fabric.exe` 和 `FabricGateway.exe` 的多个实例，在单台计算机或 VM 上托管多个节点。

## <a name="application-and-service-concepts"></a>应用程序和服务概念

**Service Fabric 网格应用程序**：Service Fabric 网格应用程序是通过资源模型（YAML 和 JSON 资源文件）描述的，并且可以部署到运行 Service Fabric 的任何环境中。

**Service Fabric 本机应用程序**：Service Fabric 本机应用程序是通过本机应用程序模型（基于 XML 的应用程序和服务清单）描述的。  Service Fabric 本机应用程序无法在 Service Fabric 网格中运行。

### <a name="service-fabric-mesh-application-concepts"></a>Service Fabric 网格应用程序概念

**应用程序**：应用程序是网格应用程序的部署、版本控制和生存期的单位。 可以独立管理每个应用程序实例的生命周期。  应用程序由一个或多个服务代码包和设置组成。 应用程序是使用 Azure 资源模型 (RM) 架构定义的。  服务被描述为 RM 模板中的应用程序资源的属性。  应用程序使用的网络和卷由应用程序引用。  创建应用程序时，应用程序、服务、网络和卷均使用 Service Fabric 资源模型建模。

**服务**：应用程序中的服务代表微服务并执行完整且独立的功能。 每个服务都包含一个或多个代码包，用于描述运行与代码包关联的容器映像所需的所有内容。  应用程序中的服务数量可以增大和减小。

**网络**：网络资源为应用程序创建专用网络，并独立于可能引用它的应用程序或服务。 同一网络可以包含来自不同应用程序的多个服务。 网络是应用程序引用的可部署资源。

**代码包**：代码包描述运行与代码包关联的容器映像所需的所有内容，包括以下内容：

* 容器名称、版本和注册表
* 每个容器所需的 CPU 和内存资源
* 网络终结点
* 要在引用单独卷资的源容器中装载的卷。

若代码包被定义为应程序资源的一部分，则所有此类代码包都作为一组一起进行部署和激活。

**卷**：卷是装载在可用于保留状态的容器实例内的目录。 Azure 文件卷驱动程序将 Azure 文件共享安装到容器，并通过支持文件存储的任何 API 提供可靠的数据存储。 卷是应用程序引用的可部署资源。

### <a name="service-fabric-native-application-concepts"></a>Service Fabric 本机应用程序概念

**应用程序**：应用程序是由执行一个或多个特定功能的成分服务组成的集合。 可以独立管理每个应用程序实例的生命周期。

**服务**：服务执行完整且独立的功能，可以独立于其他服务启动和运行。 服务由代码、配置和数据组成。 对于每个服务，代码由可执行二进制文件组成，配置由可在运行时加载的服务设置组成，数据则由可供该服务使用的任意静态数据组成。

应用程序类型：分配给服务类型集合的名称/版本。 在 `ApplicationManifest.xml` 文件中定义并嵌入到应用程序包目录。 然后将目录复制到 Service Fabric 群集的映像存储。 然后，可以基于此应用程序类型，在群集内创建命名的应用程序。

有关详细信息，请阅读[应用程序模型](service-fabric-application-model.md)一文。

应用程序包：一个磁盘目录，其中包含应用程序类型的 `ApplicationManifest.xml` 文件。 引用构成应用程序类型的每个服务类型的服务包。 应用程序包目录中的文件将复制到 Service Fabric 群集的映像存储。 例如，电子邮件应用程序类型的应用程序包可能包含对队列服务包、前端服务包和数据库服务包的引用。

命名应用程序：将应用程序包复制到映像存储后，在群集中创建应用程序实例。 当你指定应用程序包的应用程序类型时，通过使用其名称或版本来创建一个实例。 将为每个应用程序类型实例分配一个类似如下的统一资源标识符 (URI) 名称：`"fabric:/MyNamedApp"`。 在群集中，可以从单个应用程序类型创建多个命名应用程序。 还可以从不同的应用程序类型创建命名应用程序。 可单独管理每个命名应用程序并设置其版本。

服务类型：分配给服务的代码包、数据包、配置包的名称/版本。 服务类型在 `ServiceManifest.xml` 文件中定义，并嵌入到服务包目录中。 然后，服务包目录由应用程序包的 `ApplicationManifest.xml` 文件引用。 在群集中创建命名应用程序后，可以从应用程序类型的服务类型之一创建命名服务。 服务类型的 `ServiceManifest.xml` 文件描述该服务。

有关详细信息，请阅读[应用程序模型](service-fabric-application-model.md)一文。

有两种类型的服务：

* 无状态：服务的持久状态存储在 Azure 存储、Azure SQL 数据库、Azure Cosmos DB 等外部存储服务中时，请使用无状态服务。 当服务没有永久性存储时，请使用无状态服务。 以计算器服务为例，首先要将值传递给该服务，然后服务使用这些值执行计算并返回结果。
* 有状态：如果要让 Service Fabric 通过其 Reliable Collections 或 Reliable Actors 编程模型管理服务状态，请使用有状态服务。 创建命名服务时，请指定要将状态分布于其中的分区数量，以实现伸缩性。 此外，指定跨节点复制状态的次数，以实现可靠性。 每个命名服务都有一个主副本和多个辅助副本。 在写入主要副本时修改命名服务的状态。 然后 Service Fabric 将此状态复制到所有次要副本以使状态保持同步。Service Fabric 自动检测主副本失败的时间，并将现有辅助副本升级为主副本。 然后，Service Fabric 会创建新的次要副本。  

副本或实例是指已部署或运行中的服务的代码（和有状态服务的状态）。 请参阅[副本和实例](service-fabric-concepts-replica-lifecycle.md)。

重新配置是指服务副本集中任何更改的过程。 请参阅[重新配置](service-fabric-concepts-reconfiguration.md)。

服务包：一个磁盘目录，其中包含服务类型的 `ServiceManifest.xml` 文件。 此文件引用服务类型的代码、静态数据和配置包。 应用程序类型的 `ApplicationManifest.xml` 文件引用服务包目录中的文件。 例如，服务包可能引用构成数据库服务的代码、静态数据和配置包。

命名服务：创建命名应用程序后，可以在群集中创建它的其中一个服务类型的实例。 通过使用其名称/版本指定服务类型。 需为每个服务类型实例分配一个 URI 名称，该名称归并到实例的命名应用程序的 URI 之下。 例如，如果在命名应用程序“MyNamedApp”中创建命名服务“MyDatabase”，则 URI 将类似于：`"fabric:/MyNamedApp/MyDatabase"`。 在一个命名应用程序中可以创建多个命名服务。 每个命名服务可以有自身的分区方案和实例或副本计数。

代码包：一个磁盘目录，其中包含服务类型的可执行文件，通常是 EXE/DLL 文件。 服务类型的 `ServiceManifest.xml` 文件引用代码包目录中的文件。 创建命名服务后，会将代码包复制到选定来运行命名服务的一个或多个节点。 然后代码将开始运行。 有两种类型的代码包可执行文件：

* **来宾可执行文件**：在主机操作系统（Windows 或 Linux）上按原样运行的可执行文件。 这些可执行文件不会链接到或引用任何 Service Fabric 运行时文件，因此不会使用任何 Service Fabric 编程模型。 这些可执行文件不能使用某些 Service Fabric 功能，例如终结点发现的命名服务。 来宾可执行文件无法报告特定于每个服务实例的负载指标。
* 服务主机可执行文件：通过链接到 Service Fabric 运行时文件、启用 Service Fabric 功能来使用 Service Fabric 编程模型的可执行文件。 例如，命名服务实例可在 Service Fabric 命名服务中注册终结点，还可以报告负载指标。

数据包：一个磁盘目录，其中包含服务类型的静态只读数据文件，通常是照片、音频和视频文件。 服务类型的 `ServiceManifest.xml` 文件引用数据包目录中的文件。 创建命名服务后，会将数据包复制到选定来运行命名服务的一个或多个节点。 代码开始运行，现在即可访问数据文件。

配置包：一个磁盘目录，其中包含服务类型的静态只读配置文件，通常是文本文件。 服务类型的 `ServiceManifest.xml` 文件引用配置包目录中的文件。 创建命名服务时，会将配置包中的文件复制到选定运行命名服务的一个或多个节点。 然后，代码开始运行，现在即可访问配置文件。

**容器**：默认情况下，Service Fabric 以进程形式部署和激活这些服务。 Service Fabric 还可以部署容器映像中的服务。 容器是一种虚拟化技术，用于从应用程序中提取基础操作系统。 应用程序及其运行时、依赖项和系统库在一个容器内运行。 容器对自己的操作系统构造隔离视图具有全部专有访问权限。 Service Fabric 支持 Linux 上的 Windows Server 容器和 Docker 容器。 有关详细信息，请参阅 [Service Fabric 和容器](service-fabric-containers-overview.md)。

分区方案：创建命名服务时，需要指定一个分区方案。 包含大量状态的服务跨分区拆分数据，将状态分散在群集的节点上。 通过在分区之间拆分数据，可以扩展命名服务的状态。 在分区中，无状态命名服务具有实例，而有状态命名服务具有副本。 通常，无状态命名服务只有一个分区，因为它们没有内部状态。 分区实例提供可用性。 如果一个实例失败，其他实例可继续正常运行，然后 Service Fabric 将创建新的实例。 有状态命名服务在副本中保持其状态，每个分区都有自己的副本集，因此状态保持同步。如果副本失败，Service Fabric 会从现有副本生成新的副本。

有关详细信息，请阅读 [Service Fabric Reliable Services 分区](service-fabric-concepts-partitioning.md)一文。

## <a name="system-services"></a>系统服务

系统在每个群集中创建了一些系统服务，用于提供 Service Fabric 的平台功能。

命名服务：每个 Service Fabric 群集均有一个命名服务，该服务将服务名称解析到群集中的某个位置。 使用类似于管理群集的 Internet 域名系统 (DNS) 的方式管理服务名称和属性。 客户端可以使用命名服务安全地与群集中的任何节点进行通信，以解析服务名称及其位置。 应用程序在群集内移动。 例如，原因可以是故障、资源平衡或重设群集大小。 可开发解析当前网络位置的服务和客户端。 客户端会获得实际计算机 IP 地址及其当前运行位置所在的端口。

有关使用搭配命名服务运行的客户端与服务通信 API 的详细信息，请阅读[与服务通信](service-fabric-connect-and-communicate-with-services.md)。

映像存储服务：每个 Service Fabric 群集都有一个映像存储服务，其中保存已部署且版本化的应用程序包。 将应用程序包复制到映像存储，并注册该应用程序包内包含的应用程序类型。 预配应用程序类型后，根据它创建命名应用程序。 在删除某个应用程序类型的所有命名应用程序之后，可以从映像存储服务中注销该应用程序类型。

有关映像存储服务的详细信息，请参阅[了解 ImageStoreConnectionString 设置](service-fabric-image-store-connection-string.md)。

有关将应用程序部署到映像存储服务的详细信息，请阅读[部署应用程序](service-fabric-deploy-remove-applications.md)一文。

**故障转移管理器服务**：每个 Service Fabric 群集都具有一个故障转移管理器服务，负责执行以下操作：

 - 执行与服务的高可用性和一致性相关的功能。
 - 协调应用程序和群集升级。
 - 与其他系统组件交互。

**修复管理器服务**：这是一项可选的系统服务，可在集群上实现自动、透明、安全的修复操作。 修复管理器可用于：

   - 在[白银和黄金持久性](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) Azure Service Fabric 集群上执行 Azure 维护修复。
   - 实施面向[修补业务流程应用](service-fabric-patch-orchestration-application.md)的修复操作

## <a name="deployment-and-application-models"></a>部署和应用程序模型

若要部署服务，需要描述服务的运行方式。 Service Fabric 支持 3 种不同的部署模型：

### <a name="resource-model-preview"></a>资源模型（预览版）

Service Fabric 资源是可以单独部署到 Service Fabric 的任何内容，包括应用程序、服务、网络和卷。 资源是使用可以部署到集群终结点的 JSON 文件定义的。  对于 Service Fabric 网格，使用 Azure 资源模型架构。 YAML 文件架构还可用于更轻松地创作定义文件。 可将资源部署到 Service Fabric 运行的任何位置。 资源模型是用来描述 Service Fabric 应用程序的最简单方法。 其主要重点在于容器化服务的简单部署和管理。 要了解详细信息，请参阅 [Service Fabric 资源模型简介](/azure/service-fabric-mesh/service-fabric-mesh-service-fabric-resources)。

### <a name="native-model"></a>本机模块

本机应用程序模型为应用程序提供对 Service Fabric 的完整低级别访问权限。 应用程序和服务被定义为 XML 清单文件中的已注册类型。

本机模型支持 Reliable Services 和 Reliable Actors 框架，该框架提供对 C# 和 Java 中 Service Fabric 运行时 API 和群集管理 API 的访问权限。 本机模型还支持任意容器和可执行文件。 [Service Fabric 网格环境](/azure/service-fabric-mesh/service-fabric-mesh-overview)中不支持本机模型。

**Reliable Services**：用于构建无状态和有状态服务的 API。 有状态服务将其状态存储在 Reliable Collections（例如字典或队列）中。 也可插入各种通信堆栈，如 Web API 和 Windows Communication Foundation (WCF)。

**Reliable Actors**：用于通过虚拟执行组件编程模型构建无状态和有状态对象的 API。 如果有大量的独立计算或状态单位，此模型可能十分有用。 此模型使用基于轮次的线程模型，因此最好避免使用向外调用其他执行组件或服务的代码，原因是只有在单个执行组件的所有出站请求都已完成后，该执行组件才能处理其他传入请求。

还可以在 Service Fabric 上运行现有应用程序：

**容器**：Service Fabric 支持在 Linux 上部署 Docker 容器，在 Windows Server 2016 上部署 Windows Server 容器，同时支持 Hyper-V 隔离模式。 在 Service Fabric [应用程序模型](service-fabric-application-model.md)中，容器表示放置多个服务副本的应用程序主机。 Service Fabric 可运行任何容器，该方案类似于来宾可执行的方案，可在容器内打包现有应用程序。 此外，也可[在容器内运行 Service Fabric 服务](service-fabric-services-inside-containers.md)。

**来宾可执行文件**：可以在 Azure 中运行任何类型的代码（如 Node.js、Python、Java 或C++作为服务） Service Fabric。 Service Fabric 将这些类型的服务称为来宾可执行文件，视其为无状态服务。 在 Service Fabric 群集中运行来宾可执行文件的优点包括高可用性、运行状况监视、应用程序生命周期管理、高密度和可发现性。

有关详细信息，请阅读[为服务选择编程模型](service-fabric-choose-framework.md)一文。

### <a name="docker-compose"></a>Docker Compose 

[Docker Compose](https://docs.docker.com/compose/) 是 Docker 项目的一部分。 Service Fabric 对[使用 Docker Compose 模型部署应用程序](service-fabric-docker-compose.md)提供有限支持。

## <a name="environments"></a>环境

Service Fabric 是一种开放源平台技术，多种不同的服务和产品都以它为基础。 Microsoft 提供以下选项：

 - **Azure Service Fabric 网格**：一种完全托管服务，用于在 Microsoft Azure 中运行 Service Fabric 应用程序。
 - **Azure Service Fabric**：Azure 托管的 Service Fabric 群集服务/产品。 它提供 Service Fabric 和 Azure 基础结构之间的集成，以及 Service Fabric 群集的升级和配置管理。
 - **Service Fabric 独立**：一组安装和配置工具，可[在任何位置部署 Service Fabric 群集](/azure/service-fabric/service-fabric-deploy-anywhere)（在本地或任何云提供程序）。 不由 Azure 管理。
 - **Service Fabric 开发群集**：在 Windows、Linux 或 Mac 上提供本地开发经验，用于开发 Service Fabric 应用程序。

## <a name="environment-framework-and-deployment-model-support-matrix"></a>环境、框架和部署模型支持矩阵

不同环境对框架和部署模型具有不同的支持级别。 下表介绍了支持的框架和部署模型组合。

| 应用程序类型 | 介绍依据 | Azure Service Fabric 网格 | Azure Service Fabric 群集（任何 OS）| 本地群集 | 独立群集 |
|---|---|---|---|---|---|
| Service Fabric 网格应用程序 | 资源模型（YAML 和 JSON） | 受支持 |不支持 | Windows - 支持，Linux 和 Mac - 不支持 | Windows - 不支持 |
|Service Fabric 本机应用程序 | 本机应用程序模型 (XML) | 不支持| 受支持|受支持|Windows - 支持|

下表介绍了不同的应用模型以及针对 Service Fabric 为它们提供的工具。

| 应用程序类型 | 介绍依据 | Visual Studio | Eclipse | SFCTL | AZ CLI | PowerShell|
|---|---|---|---|---|---|---|
| Service Fabric 网格应用程序 | 资源模型（YAML 和 JSON） | VS 2017 |不支持 |不支持 | 支持 - 仅网格环境 | 不支持|
|Service Fabric 本机应用程序 | 本机应用程序模型 (XML) | VS 2017 和 VS 2015| 受支持|受支持|受支持|受支持|

## <a name="next-steps"></a>后续步骤

了解有关 Service Fabric 的详细信息：

* [Service Fabric 概述](service-fabric-overview.md)
* [为什么通过微服务的方法构建应用程序？](service-fabric-overview-microservices.md)
* [应用程序方案](service-fabric-application-scenarios.md)

了解有关 Service Fabric 网格的详细信息：

* [Service Fabric 网格概述](/azure/service-fabric-mesh/service-fabric-mesh-overview)
