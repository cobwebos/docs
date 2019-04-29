---
title: Azure Service Fabric 资源模型简介 | Microsoft Docs
description: 了解 Service Fabric 资源模型，这是一种定义 Service Fabric 网格应用程序的简化方法。
services: service-fabric-mesh
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/23/2018
ms.author: vturecek
ms.custom: mvc, devcenter
ms.openlocfilehash: 3cee0ada75c4ea265c7e9c598408eb6b01477d6c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60810744"
---
# <a name="introduction-to-service-fabric-resource-model"></a>Service Fabric 资源模型简介

Service Fabric 资源模型描述了一种定义包含 Service Fabric 网格应用程序的资源的简单方法。 可将单个资源部署到任何 Service Fabric 环境。  此外，Azure 资源管理器模型还与 Azure 资源管理器模型兼容。 此模型当前支持以下类型的资源：

- 应用程序和服务
- 网络
- 网关
- 机密和机密/值
- 卷

每个资源都在资源文件中进行声明性描述，该文件是描述网格应用程序的简单 YAML 或 JSON 文档，并且由 Service Fabric 平台进行预配。

## <a name="applications-and-services"></a>应用程序和服务

应用程序资源是网格应用程序的部署、版本控制和生存期的单位。 它由一个或多个表示微服务的服务资源组成。 每个服务资源又由一个或多个代码包组成，这些代码包描述运行与代码包关联的容器映像所需的所有内容。

![应用和服务][Image1]

服务资源将声明以下内容：

- 容器名称、版本和注册表
- 每个容器所需的 CPU 和内存资源
- 网络终结点
- 对其他资源的引用，如网络、卷和机密 

定义为服务资源一部分的所有代码包作为一个组一起进行部署和激活。 服务资源还描述要运行的服务的实例数，并引用它所依赖的其他资源（如网络资源）。

如果网格应用程序由多个服务组成，则不能保证这些服务在同一个节点上一起运行。 此外，在升级应用程序期间，升级单个服务失败将导致所有服务回退到其以前版本。

如前所述，可以独立管理每个应用程序实例的生命周期。 例如，一个应用程序实例可以独立于其他应用程序实例进行升级。 通常，应将应用程序中的服务数量保持在相当少的范围，因为放入应用程序的服务越多，独立管理每个服务就越难。

## <a name="networks"></a>网络

网络资源是可单独部署的资源，独立于可能将其称为依赖项的应用程序或服务资源。 这种资源用于创建应用程序的网络。 来自不同应用程序的多个服务可以是同一网络的一部分。  有关详细信息，请参阅 [Service Fabric 网格应用程序中的网络](service-fabric-mesh-networks-and-gateways.md)。

> [!NOTE]
> 当前预览版仅支持应用程序和网络之间的一对一映射

![网络和网关][Image2]

## <a name="gateways"></a>网关
网关资源将连接两个网络，并路由流量。  网关使服务可以与外部客户端通信，并提供服务入口。  此外，还可以使用网关将网格应用程序和自己现有的虚拟网络连接在一起。 有关详细信息，请参阅 [Service Fabric 网格应用程序中的网络](service-fabric-mesh-networks-and-gateways.md)。

![网络和网关][Image2]

## <a name="secrets"></a>机密

机密资源可独立于可能将其称为依赖项的应用程序或服务资源单独部署。 它用于将机密安全地传送到应用程序。 来自不同应用程序的多个服务可以引用同一机密的值。

## <a name="volumes"></a>卷

容器通常会提供临时磁盘。 但是，临时磁盘是暂时的，你会获得一个新的临时磁盘并在容器崩溃时丢失信息。 此外，也很难在临时磁盘上与其他容器共享信息。 卷是装载在可用于保留状态的容器实例内的目录。 卷提供常规用途文件存储，并允许使用正常磁盘 I/O 文件 API 读取/写入文件。 卷资源是一种声明性方式，描述装载目录的方式以及目录的存储备份方式（Azure 文件卷或 Service Fabric Reliable Volume）。  有关详细信息，请参阅[存储状态](service-fabric-mesh-storing-state.md#volumes)。

![卷][Image3]

## <a name="programming-models"></a>编程模型
服务资源只需要运行与其关联的代码包中引用的容器映像。 可以使用容器内的任何框架运行以任何语言编写的代码，而无需了解或使用 Service Fabric 网格特定的 API。 

即使不在 Service Fabric 网格中，仍可移植应用程序代码，并且无论用于实现服务的语言或框架如何，应用程序部署都保持一致。 无论应用程序是 ASP.NET Core、Go，还是只是一组进程和脚本，Service Fabric 网格资源部署模型都保持不变。 

## <a name="packaging-and-deployment"></a>打包和部署

基于资源模型的 Service Fabric 网格应用程序打包为 Docker 容器。  Service Fabric 网格是共享的多租户环境，容器可提供高级别的隔离。  使用 JSON 格式或 YAML 格式（随后将其转换为 JSON）描述这些应用程序。 网格应用程序部署到 Azure Service Fabric 网格，用来描述应用程序的 JSON 时，Azure 资源管理器模板。 资源将映射到 Azure 资源。  部署到 Service Fabric 群集的网格应用程序时 (独立版或 Azure 托管服务)，用来描述应用程序的 JSON 是一种格式类似于 Azure 资源管理器模板。  部署后，可通过 HTTP 接口或 Azure CLI 管理网格应用程序。 


## <a name="next-steps"></a>后续步骤 
若要详细了解 Service Fabric 网格，请阅读概述：
- [Service Fabric 网格概述](service-fabric-mesh-overview.md)

[Image1]: media/service-fabric-mesh-service-fabric-resources/AppsAndServices.png
[Image2]: media/service-fabric-mesh-service-fabric-resources/NetworkAndGateway.png
[Image3]: media/service-fabric-mesh-service-fabric-resources/volumes.png
