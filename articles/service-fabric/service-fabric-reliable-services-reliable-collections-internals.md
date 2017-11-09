---
title: "Azure Service Fabric 可靠状态管理器和可靠集合内部 | Microsoft Docs"
description: "深入了解 Azure Service Fabric 中的可靠集合概念和设计。"
services: service-fabric
documentationcenter: .net
author: mcoskun
manager: timlt
editor: rajak
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 5/1/2017
ms.author: mcoskun
ms.openlocfilehash: d607449a16e886337ab1bd96213fbb4231124353
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="azure-service-fabric-reliable-state-manager-and-reliable-collection-internals"></a>Azure Service Fabric 可靠状态管理器和可靠集合内部
本文档深入探讨可靠状态管理器和可靠集合，了解核心组件如何在后台工作。

> [!NOTE]
> 本文档还在编写中。 请将注释添加到本文，告诉我们你想要详细了解哪个主题。
>

##  <a name="local-persistence-model-log-and-checkpoint"></a>本地持久性模型：日志和检查点
可靠状态管理器和可靠集合都遵循一个名为日志和检查点的持久性模型。
在此模型中，每个状态更改先记录在磁盘上，再应用于内存中。
仅在某些时候保存自身的完整状态（又称 检查点）。
其优点是增量转变成磁盘上只能追加的顺序写入，从而提高了性能。

为了更好地了解日志和检查点模型，我们先来看一下无限磁盘方案。
可靠状态管理器在复制操作之前记录每个操作。
通过记录，可靠集合可以只应用内存中的操作。
由于保存了日志，因此，即使副本出现故障并且需要重新启动，可靠状态管理器在其日志中也有足够的信息来重播副本丢失的所有操作。
由于磁盘无限大，因此，永远不需要删除日志记录，可靠集合只需管理内存中的状态。

现在让我们看一下有限磁盘方案。
随着日志记录的累积，可靠状态管理器将耗尽磁盘空间。
在这种情况出现之前，可靠状态管理器需截断其日志，以便为较新的记录腾出空间。
可靠状态管理器将请求可靠集合在磁盘中添加其内存中状态的检查点。
此时，可靠集合将保留其内存中的状态。
在 Reliable Collections 完成其检查点后，可靠状态管理器便可以截断日志以释放磁盘空间。
当副本需要重新启动时，可靠集合将恢复其检查点状态，而可靠状态管理器将恢复并播放自最后一个检查点以来发生的所有状态更改。

检查点的另一增值优点在于它可缩短常见方案中的恢复时间。 日志包含自最后一个检查点以来发生的所有操作。
因此它可能包括某个项的多个版本，如 Reliable Dictionary 中给定行的多个值。
相反，可靠集合仅将键的每个值的最新版本添加为检查点。

## <a name="next-steps"></a>后续步骤
* [事务和锁](service-fabric-reliable-services-reliable-collections-transactions-locks.md)

