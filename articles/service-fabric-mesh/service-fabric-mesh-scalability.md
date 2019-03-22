---
title: Azure Service Fabric 网格应用的可伸缩性 | Microsoft Docs
description: 了解如何缩放 Azure Service Fabric 网格中的服务。
services: service-fabric-mesh
keywords: ''
author: dkkapur
ms.author: dekapur
ms.date: 10/26/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: 1688cac35ea9de43bac529a4994bd4ea55eb0ab7
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/21/2019
ms.locfileid: "58339069"
---
# <a name="scaling-service-fabric-mesh-applications"></a>缩放 Service Fabric 网格应用程序

将应用程序部署到 Service Fabric 网格的一个主要优势是能够轻松地对服务进行横向缩放。此功能应该用于处理服务上的各种数量的负载，或者用于改进可用性。 可以手动横向收缩或扩展服务，或者设置自动缩放策略。

## <a name="manual-scaling-instances"></a>手动缩放实例

在应用程序资源的部署模板中，每项服务都有一个 *replicaCount* 属性，该属性可以用来设置所需的服务部署次数。 应用程序由多项服务组成，每项服务都有唯一的 *replicaCount* 数，这些服务可以一起进行部署和管理。 若要缩放服务副本数，请在部署模板或参数文件中修改每项需要缩放的服务的 *replicaCount* 值。 然后，升级应用程序。

有关手动缩放服务实例的示例，请参阅[手动横向收缩或扩展服务](service-fabric-mesh-tutorial-template-scale-services.md)。

## <a name="autoscaling-service-instances"></a>自动缩放服务实例
自动缩放是 Service Fabric 的一项附加功能，允许动态缩放服务实例的数量（水平缩放）。 自动缩放提供了很大的弹性并可以预配或删除基于 CPU 或内存使用率的服务实例。  通过自动缩放，你可以根据工作负荷运行合适数量的服务实例并针对成本进行优化。

自动缩放策略是在服务资源文件中针对每个服务定义的。 每个缩放策略由两部分组成：

- 一个缩放触发器，它描述将于何时执行服务的缩放。 三个因素决定了何时缩放服务。 负载阈值下限是一个用于确定何时将横向收缩服务的值。 如果分区的所有实例的平均负载低于此值，将缩小该服务。 负载阈值上限是一个用于确定何时将横向扩展服务的值。如果分区的所有实例的平均负载高于此值，将扩大该服务。缩放间隔确定检查触发器的频率（以秒为单位）。 一旦检查触发器，如果需要缩放，则将应用该机制。 如果不需要缩放，则不会采取任何操作。 在这两种情况下，在缩放间隔到期之前，不会再次检查触发器。

- 一种缩放机制，它描述在触发时将如何执行缩放。 缩放增量确定该机制触发时将添加或删除多少个实例。 最大实例计数定义了缩放上限。 如果实例数量达到此限制，则无论负载如何，都不会横向扩展服务。 最小实例计数定义了缩放下限。 如果分区的实例数量达到此限制，则无论负载如何，都不会缩小服务。

若要了解如何为服务设置自动缩放策略，请阅读[自动缩放服务](service-fabric-mesh-howto-auto-scale-services.md)。

## <a name="next-steps"></a>后续步骤

有关应用程序模型的信息，请参阅[Service Fabric 资源](service-fabric-mesh-service-fabric-resources.md)
