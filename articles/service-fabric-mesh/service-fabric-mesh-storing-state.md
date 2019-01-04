---
title: Azure Service Fabric 网格上的状态存储选项 | Microsoft Docs
description: 了解 Azure Service Fabric 网格上运行的 Service Fabric 网格应用程序的可靠存储状态。
services: service-fabric-mesh
keywords: ''
author: rwike77
ms.author: ryanwi
ms.date: 11/27/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: jeconnoc
ms.openlocfilehash: ecdb36af786d96a5b343d11cd689642d59528445
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2018
ms.locfileid: "52888522"
---
# <a name="state-management-with-service-fabric"></a>Service Fabric 的状态管理

Service Fabric 支持用于状态存储的许多不同选项。 有关状态管理模式和 Service Fabric 的概念性概述，请参阅 [Service Fabric 概念：状态](/azure/service-fabric/service-fabric-concepts-state)。 无论服务是在 Service Fabric 网格内部还是外部运行，相同的概念均适用。 

利用 Service Fabric 网格，可以轻松部署新的应用程序，并将其连接到托管在 Azure 中的现有数据存储。 除了使用远程数据库外，还有一些用于存储数据的选项，具体取决于服务需要本地还是远程存储。 

## <a name="volumes"></a>卷

容器通常使用临时磁盘。 但是，临时磁盘是暂时的，你会获得一个新的临时磁盘并在容器崩溃时丢失信息。 此外，也很难在临时磁盘上与其他容器共享信息。 卷是装载在可用于保留状态的容器实例内的目录。 卷提供常规用途文件存储，并允许使用正常磁盘 I/O 文件 API 读取/写入文件。 卷资源介绍如何装载目录，以及要使用的后备存储。 可以选择 Azure 文件存储或 Service Fabric 卷磁盘来存储数据。

![卷][image3]

### <a name="service-fabric-reliable-volume"></a>Service Fabric Reliable Volume

Service Fabric Reliable Volume 是用于将本地卷装载到容器的 Docker 卷驱动程序。 读取和写入是本地操作，非常快速。 数据将复制到辅助节点，使其高度可用。 故障转移也非常快速。 当容器崩溃时，将故障转移到已具有数据的副本的节点。 请参阅[如何使用 Service Fabric Reliable Volume 部署应用](https://github.com/Azure-Samples/service-fabric-mesh/tree/2018-09-01-preview/templates/counter)查看相关示例。

### <a name="azure-files-volume"></a>Azure 文件卷

Azure 文件卷是将 Azure 文件共享装载到容器的 Docker 卷驱动程序。 Azure 文件存储使用网络存储，因此将通过网络进行读取和写入。 与 Service Fabric Reliable Volume 相比，Azure 文件存储的性能较低，但提供了一个更便宜且完全可靠的数据选项。 请参阅[如何使用 Azure 文件卷部署应用](service-fabric-mesh-howto-deploy-app-azurefiles-volume.md)查看相关示例。

## <a name="next-steps"></a>后续步骤

有关应用程序模型的信息，请参阅[Service Fabric 资源](service-fabric-mesh-service-fabric-resources.md)

[image3]: ./media/service-fabric-mesh-storing-state/volumes.png
