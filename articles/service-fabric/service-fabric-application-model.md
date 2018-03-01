---
title: "Azure Service Fabric 应用程序模型 | Microsoft Docs"
description: "如何在 Service Fabric 中建模和描述应用程序。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: mani-ramaswamy
ms.assetid: 17a99380-5ed8-4ed9-b884-e9b827431b02
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: ryanwi
ms.openlocfilehash: 506daa2dc0612fc49a67c5faf3c7ab51ac90126f
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/24/2018
---
# <a name="model-an-application-in-service-fabric"></a>在 Service Fabric 中对应用程序建模
本文概述 Azure Service Fabric 应用程序模型以及如何通过清单文件定义应用程序和服务。

## <a name="understand-the-application-model"></a>了解应用程序模型
应用程序是由执行一个或多个特定功能的成分服务组成的集合。 服务执行完整且独立的功能，可以独立于其他服务启动和运行。  服务由代码、配置和数据组成。 对于每个服务，代码由可执行二进制文件组成，配置由可在运行时加载的服务设置组成，数据则由可供该服务使用的任意静态数据组成。 可对此层次应用程序模型中的每个组件进行独立的版本控制和升级。

![Service Fabric 应用程序模型][appmodel-diagram]

应用程序类型是一个应用程序分类，包含大量服务类型。 服务类型是一个服务分类。 该分类可以具有不同的设置和配置，但核心功能保持相同。 服务实例是相同服务类型的不同服务配置变体。  

通过 XML 文件（应用程序清单和服务清单）描述应用程序和服务的类（或“类型”）。  清单描述应用程序和服务，可对照这样的模板从群集的映像存储区中对应用程序进行实例化。  清单将在[应用程序和服务清单](service-fabric-application-and-service-manifests.md)中详细介绍。 ServiceManifest.xml 和 ApplicationManifest.xml 文件的架构定义随 Service Fabric SDK 和工具一起安装到 *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*。 XML 架构记录在 [ServiceFabricServiceModel.xsd 架构文档](service-fabric-service-model-schema.md)中。

即使在由同一 Service Fabric 节点托管时，不同应用程序实例的代码也将作为单独的进程运行。 此外，可以独立管理每个应用程序实例的生命周期（如升级）。 下图显示了应用程序类型如何由服务类型组成，而服务类型又如何由代码、配置和数据包组成。 为了简化示意图，这里只显示了 `ServiceType4` 的代码/配置/数据包，不过每个服务类型包括其中的部分或全部包类型。

![Service Fabric 应用程序类型和服务类型][cluster-imagestore-apptypes]

群集中可以有一个或多个服务类型实例处于活动状态。 例如，有状态服务实例或副本通过复制位于群集中不同节点上的副本之间的状态实现高可靠性。 复制本质上是提供冗余，使服务即使在群集中的一个节点失败时也可用。 [分区服务](service-fabric-concepts-partitioning.md)进一步跨群集中的节点划分其状态（和该状态的访问模式）。

下图显示应用程序和服务实例、分区与副本之间的关系。

![服务中的分区和副本][cluster-application-instances]

> [!TIP]
> 可以使用 http://&lt;yourclusteraddress&gt;:19080/Explorer 上提供的 Service Fabric Explorer 工具查看群集中应用程序的布局。 有关详细信息，请参阅[使用 Service Fabric Explorer 可视化群集](service-fabric-visualizing-your-cluster.md)。
> 
> 


## <a name="next-steps"></a>后续步骤
- 了解[应用程序可伸缩性](service-fabric-concepts-scalability.md)。
- 了解服务[状态](service-fabric-concepts-state.md)、[分区](service-fabric-concepts-partitioning.md)和[可用性](service-fabric-availability-services.md)。
- 阅读有关如何在[应用程序和服务清单](service-fabric-application-and-service-manifests.md)中定义应用程序和服务的信息。
- [应用程序托管模型](service-fabric-hosting-model.md)说明已部署的服务和服务主机进程二者的副本（或实例）之间的关系。

<!--Image references-->
[appmodel-diagram]: ./media/service-fabric-application-model/application-model.png
[cluster-imagestore-apptypes]: ./media/service-fabric-application-model/cluster-imagestore-apptypes.png
[cluster-application-instances]: media/service-fabric-application-model/cluster-application-instances.png


