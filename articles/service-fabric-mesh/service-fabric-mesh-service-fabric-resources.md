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
ms.date: 07/12/2018
ms.author: vturecek
ms.custom: mvc, devcenter
ms.openlocfilehash: 6a96995a46cd2ad0a1afe4f4afe49d62409f2b68
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/17/2018
ms.locfileid: "39075082"
---
# <a name="introduction-to-service-fabric-resource-model"></a>Service Fabric 资源模型简介

Service Fabric 资源模型描述了一种定义包含 Service Fabric 网格应用程序的资源的简单方法。 此模型当前支持以下类型的资源：

- 应用程序
- 服务
- 卷
- 网络

每个资源都在资源文件中进行声明性描述，该文件是描述网格应用程序的简单 YAML 或 JSON 文档，并且由 Service Fabric 平台进行预配。

## <a name="resource-overview"></a>资源概述

### <a name="applications-and-services"></a>应用程序和服务

应用程序资源是网格应用程序的部署、版本控制和生存期的单位。 它由一个或多个表示微服务的服务资源组成。 每个服务资源又由一个或多个代码包组成，这些代码包描述运行与代码包关联的容器映像所需的所有内容，包括以下内容：

- 容器名称、版本和注册表
- 每个容器所需的 CPU 和内存资源
- 网络终结点
- 要在引用单独卷资的源容器中装载的卷。

定义为服务资源一部分的所有代码包作为一个组一起进行部署和激活。 服务资源还描述要运行的服务的实例数，并引用它所依赖的其他资源（如网络资源）。

如果网格应用程序由多个服务组成，则不能保证这些服务在同一个节点上一起运行。 此外，在升级应用程序期间，升级单个服务失败将导致所有服务回退到其以前版本。



```yaml
    services:
      - name: helloWorldService
        properties:
          description: Hello world service.
          osType: linux
          codePackages:
            - name: helloworld
              image: myapp:1.0-alpine
              resources:
                requests:
                  cpu: 2
                  memoryInGB: 2
              endpoints:
                - name: helloWorldEndpoint
                  port: 8080
    replicaCount: 3
    networkRefs:
      - name: mynetwork
```

如前所述，可以独立管理每个应用程序实例的生命周期。 例如，一个应用程序实例可以独立于其他应用程序实例进行升级。 通常，应将应用程序中的服务数量保持在相当少的范围，因为放入应用程序的服务越多，独立管理每个服务就越难。

### <a name="networks"></a>网络

网络资源是可单独部署的资源，独立于可能将其称为依赖项的应用程序或服务资源。 这种资源用于创建应用程序的专用网络。 来自不同应用程序的多个服务可以是同一网络的一部分。

> [!NOTE]
> 当前预览版仅支持应用程序和网络之间的一对一映射

### <a name="volumes"></a>卷

卷是装载在可用于保留状态的容器实例内的目录。 卷资源是一种声明性方式，描述装载目录的方式以及目录的存储备份方式。

## <a name="programming-models"></a>编程模型
服务资源只需要运行与其关联的代码包中引用的容器映像。 可以使用容器内的任何框架运行以任何语言编写的代码，而无需了解或使用 Service Fabric 网格特定的 API。 

即使不在 Service Fabric 网格中，仍可移植应用程序代码，并且无论用于实现服务的语言或框架如何，应用程序部署都保持一致。 无论应用程序是 ASP.NET Core、Go，还是只是一组进程和脚本，Service Fabric 网格资源部署模型都保持不变。 

## <a name="deployment"></a>部署

部署到 Service Fabric 网格时，资源作为 Azure 资源管理器模板通过 HTTP 或 Azure CLI 部署到 Azure。 


## <a name="next-steps"></a>后续步骤 
若要详细了解 Service Fabric 网格，请阅读概述：
- [Service Fabric 网格概述](service-fabric-mesh-overview.md)
