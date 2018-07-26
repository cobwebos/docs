---
title: Azure Service Fabric 网格上的状态存储选项 | Microsoft Docs
description: 了解 Azure Service Fabric 网格上运行的 Service Fabric 网格应用程序的可靠存储状态。
services: service-fabric-mesh
keywords: ''
author: rwike77
ms.author: ryanwi
ms.date: 07/12/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: 6aa268cf56bfb8be9c27a9e0d9e5c9f4464b0c9d
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/17/2018
ms.locfileid: "39075262"
---
# <a name="state-management-with-service-fabric"></a>Service Fabric 的状态管理
Service Fabric 支持用于状态存储的许多不同选项。 有关状态管理模式和 Service Fabric 的概念性概述，请参阅 [Service Fabric 概念：状态](/azure/service-fabric/service-fabric-concepts-state)。 无论服务是在 Service Fabric 网格内部还是外部运行，相同的概念均适用。 

## <a name="state-storage-options-in-azure-service-fabric-mesh"></a>Azure Service Fabric 网格中的状态存储选项
利用 Service Fabric 网格，可以轻松部署新的应用程序，并将其连接到托管在 Azure 中的现有数据存储。 除了使用远程数据库外，还有一些用于存储数据的选项，具体取决于服务需要本地还是远程存储。 

* 本地存储的复制数据
  * 可靠集合（不适用于预览版）
    * 实现服务中使用的队列和键值对等数据结构的库
    * 这是与数据交互的最简单、最快捷方式，不仅可提供简单分区路由，还可提供 Service Fabric 网格中的智能路由
  * Service Fabric 卷驱动程序（不适用于预览版）
    * 将本地卷装载到容器的 docker 卷驱动程序
    * 可提供通过任何 API 本地存储数据的高度灵活性，并支持文件存储。

* 远程存储
  * Azure 文件卷驱动程序
    * 将 Azure 文件共享装载到容器的 docker 卷驱动程序
    * 此数据选项（通过任何 API）虽然性能略低，但成本较低、完全灵活可靠，且支持文件存储。
    * [操作方法指南：使用 Azure 文件卷部署应用](service-fabric-mesh-howto-deploy-app-azurefiles-volume.md)
    
## <a name="next-steps"></a>后续步骤

有关应用程序模型的信息，请参阅[Service Fabric 资源](service-fabric-mesh-service-fabric-resources.md)
