---
title: 使用 Azure Cosmos DB 中的更改源处理器库
description: 使用 Azure Cosmos DB 更改源处理器库。
author: rafats
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: rafats
ms.reviewer: sngun
ms.openlocfilehash: 64763ebc145685e4dc7a4559eac6db9923d31dc5
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2019
ms.locfileid: "55455552"
---
# <a name="change-feed-processor-in-azure-cosmos-db"></a>Azure Cosmos DB 更改源处理器 

借助 [Azure Cosmos DB 更改源处理器库](sql-api-sdk-dotnet-changefeed.md)，可以在多个使用者之间分配事件处理负载。 此库简化了跨分区的以及并行工作的多个线程中的更改的读取。

更改源处理器库的主要优势在于，无需管理每个分区和继续标记，也无需手动轮询每个容器。

更改源处理器库简化了跨分区的以及并行工作的多个线程中的更改的读取。 它使用租约机制自动管理跨分区的更改读取。 如下图中所示，如果启动两个使用更改源处理器库的客户端，它们会在彼此之间划分工作。 如果不断增加客户端的数目，它们仍会在彼此之间划分工作。

![使用 Azure Cosmos DB 更改源处理器库](./media/change-feed-processor/change-feed-output.png)

左侧的客户端先启动并开始监视所有分区，然后第二个客户端启动，随后，第一个客户端让某些租约转移到第二个客户端。 这是在不同的计算机和客户端之间分配工作的有效方法。

如果有两个无服务器 Azure 函数正在监视同一个容器并使用相同的租约，则根据处理器库决定处理分区的方式，这两个函数可能会收到不同的文档。

## <a name="implementing-the-change-feed-processor-library"></a>实施更改源处理器库

实施更改源处理器库需要四个主要组件： 

1. **监视的容器：** 监视的容器是用于生成更改源的数据。 对监视容器的任何插入和更改都会反映在容器的更改源中。

1. **租约容器：** 租约容器协调处理跨多个辅助角色的更改源。 单独的容器用于存储租约，一个分区一个租约。 最好将此租约容器存储在不同的帐户（写入区域更靠近更改源处理器运行位置）。 租用对象有以下属性：

   * 所有者：指定拥有租约的主机。

   * 继续：为特殊分区指定更改源中的位置（继续标记）。

   * 时间戳：租约最近更新时间；时间戳可用于检查租约是否到期。

1. **处理器主机：** 每个主机根据具有活动租用的其他主机实例数目，确定要处理的分区数目。

   * 主机启动时，将获取租用，以在所有主机中均衡工作负荷。 主机定期续订租用，使租用保持活动状态。

   * 主机为每次读取检查其租用的最近继续标记。 为确保并发安全，主机会检查 ETag 的每次租用更新。 此外还支持其他检查点策略。

   * 关闭后，主机会释放所有租用，但保留继续信息，以便稍后从存储检查点继续读取。

   目前，主机数量不能大于分区（租约）数量。

1. **使用者：** 使用者或辅助角色是执行每个主机启动的更改源处理的线程。 每个处理器主机可以有多个使用者。 每个使用者从分配给其的分区上读取更改源，并就更改和过期的租用通知主机。

若要进一步了解更改源处理器的四个元素是如何协同工作的，请看下图中的一个示例。 监视的集合存储文档，并将“City”用作分区键。 可以看到蓝色分区包含“A - E”中的“City”字段的文档。 有两个主机，每个主机有两个从四个并行分区读取的使用者。 箭头显示的是从更改源中特定位置读取的使用者。 在第一个分区中，深蓝色表示更改源上未读取的更改，而浅蓝色表示更改源上已读取的更改。 主机使用租用集合来存储“继续”值，跟踪每个使用者的当前读取位置。

![更改源处理器示例](./media/change-feed-processor/changefeedprocessor.png)

### <a name="change-feed-and-provisioned-throughput"></a>更改源和预配吞吐量

消耗的 RU 会产生费用，将数据移入和移出 Cosmos 容器始终会消耗 RU。 租约容器消耗的 RU 也会产生费用。

## <a name="additional-resources"></a>其他资源

* [Azure Cosmos DB 更改源处理器库](sql-api-sdk-dotnet-changefeed.md)
* [NugGet 包](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)
* [GitHub 上的其他示例](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/ChangeFeedProcessor)

## <a name="next-steps"></a>后续步骤

接下来，请通过以下文章继续详细了解更改源：

* [更改源概述](change-feed.md)
* [读取更改源的方式](read-change-feed.md)
* [将更改源与 Azure Functions 配合使用](change-feed-functions.md)
