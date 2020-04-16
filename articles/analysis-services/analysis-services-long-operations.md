---
title: Azure 分析服务中长时间运行操作的最佳做法 |微软文档
description: 本文介绍了长时间运行操作的最佳做法。
author: minewiskan
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: owend
ms.openlocfilehash: 3f6b2194cc422a827bbc7a15c012173b3f814b52
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428103"
---
# <a name="best-practices-for-long-running-operations"></a>长时间运行操作的最佳做法

在 Azure 分析服务中，*节点*表示服务器资源正在运行的主机虚拟机。 如果服务器资源移动到其他节点，某些操作（如长时间运行的查询、刷新操作和查询横向扩展同步）可能会失败。 此方案中的常见错误消息包括：

- 尝试查找长时间运行的 XMLA 请求时出错。 请求可能已被服务升级或服务器重新启动中断。
- 由于服务错误（不<guid>活动），"<database>使用 ID' 的"模型"的工作被取消，消息"取消刷新请求，因为它被卡住，没有任何更新。 这是一个内部服务问题。 如果此问题再次发生，请重新提交作业或提交票证以获得帮助。

长时间运行操作可能会中断的原因有很多。 例如，Azure 中的更新，例如： 
- 操作系统修补程序 
- 安全更新
- Azure 分析服务服务更新
- 服务结构更新。 Service Fabric 是许多 Microsoft 云服务（包括 Azure 分析服务）使用的平台组件。

除了服务中发生的更新之外，由于负载平衡，服务在节点之间自然移动。 节点移动是云服务的预期部分。 Azure 分析服务尝试将节点移动的影响降至最低，但不可能完全消除它们。 

除了节点移动之外，还可能发生其他故障。 例如，数据源数据库系统可能处于脱机状态或网络连接丢失。 如果在刷新期间，分区具有 1000 万行，并且第 9 百万行发生故障，则无法在失败点重新启动刷新。 服务必须从一开始就重新开始。 

## <a name="refresh-rest-api"></a>刷新 REST API

对于长时间运行的操作（如数据刷新），服务中断可能具有挑战性。 Azure 分析服务包括 REST API，以帮助减轻服务中断的负面影响。 要了解更多信息，请参阅[使用 REST API 进行异步刷新](analysis-services-async-refresh.md)。
 
除了 REST API 之外，您还可以使用其他方法在长时间运行刷新操作期间最小化潜在问题。 主要目标是避免从头开始重新启动刷新操作，而是以较小的批处理执行刷新，这些批处理可以分阶段提交。 
 
REST API 允许此类重新启动，但它不允许完全灵活地创建和删除分区。 如果方案需要复杂的数据管理操作，则解决方案应在其逻辑中包括某种形式的批处理。 例如，通过使用事务处理多个单独批处理中的数据，可以失败，不需要从头开始重新启动，而是从中间检查点重新启动。 
 
## <a name="scale-out-query-replicas"></a>横向扩展查询副本

无论是使用 REST 还是自定义逻辑，客户端应用程序查询仍可在处理批处理时返回不一致或中间的结果。 如果在处理期间需要客户端应用程序查询返回的一致数据，并且模型数据处于中间状态，则可以使用只读查询副本横向[扩展](analysis-services-scale-out.md)。

通过使用只读查询副本，在分批执行刷新时，客户端应用程序用户可以继续查询只读副本上数据的旧快照。 刷新完成后，可以执行 Synch 操作以使只读副本保持最新。


## <a name="next-steps"></a>后续步骤

[使用 REST API 执行异步刷新](analysis-services-async-refresh.md)  
[Azure Analysis Services 横向扩展](analysis-services-scale-out.md)  
[Analysis Services 高可用性](analysis-services-bcdr.md)  
[重试 Azure 服务指南](https://docs.microsoft.com/azure/architecture/best-practices/retry-service-specific)   

