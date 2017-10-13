---
title: "定义和管理 Azure 微服务中的状态 | Microsoft Docs"
description: "如何定义和管理 Service Fabric 中的服务状态"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: f5e618a5-3ea3-4404-94af-122278f91652
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 103fd6c3d536bc11f4e39444043a332a1d8f6c01
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="service-state"></a>服务状态
服务状态指的是服务正常运行所需的内存或磁盘数据。 这包括（例如）服务为正常工作而读取和写入的数据结构和成员变量等。 这可能还包括存储在磁盘上的文件或其他资源，具体取决于服务的体系结构。 例如，数据库用于存储数据和事务日志的文件。

例如，请设想一个计算器服务。 基本计算器服务需要两个数字并返回总和。 执行此计算不涉及成员变量或其他信息。

现在，考虑相同的计算器，但采用其他方法来存储和返回上次所计算的总和。 此服务现在有状态。 有状态表示它包含写入的状态（计算新的总和时）和读取的状态（请求其返回上次计算出的总和时）。

在 Azure Service Fabric 中，第一个服务称为无状态服务。 第二个服务则称为有状态服务。

## <a name="storing-service-state"></a>存储服务状态
状态可以外部化或与操作状态的代码共存。 通常使用一个外部数据库或（在网络中不同计算机上运行或同一计算机进程外运行的）其他存储空间来实现状态的外部化。 在我们的计算器示例中，数据存储可以是 SQL 数据库或 Azure 表存储实例。 每个计算总和的请求均对此数据执行更新，且要求服务返回相应值的请求会导致从存储中提取当前值。 

状态也可以与操作此状态的代码共存。 Service Fabric 中的有状态服务通常是使用此模型生成的。 Service Fabric 提供基础结构，以确保此状态高度可用、一致和持久，并且以这种方式构建的服务可轻松进行缩放。

## <a name="next-steps"></a>后续步骤
有关 Service Fabric 概念的详细信息，请参阅以下文章：

* [Service Fabric 服务的可用性](service-fabric-availability-services.md)
* [Service Fabric 服务的可伸缩性](service-fabric-concepts-scalability.md)
* [Service Fabric 服务分区](service-fabric-concepts-partitioning.md)
* [Service Fabric Reliable Services](service-fabric-reliable-services-introduction.md)
