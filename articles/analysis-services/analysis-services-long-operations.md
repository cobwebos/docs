---
title: Azure Analysis Services 中的长时间运行操作的最佳实践 |Microsoft Docs
description: 本文介绍了长时间运行的操作的最佳实践。
author: minewiskan
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: owend
ms.openlocfilehash: 3f6b2194cc422a827bbc7a15c012173b3f814b52
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81428103"
---
# <a name="best-practices-for-long-running-operations"></a>长时间运行操作的最佳实践

在 Azure Analysis Services 中，*节点*表示运行服务器资源的主机虚拟机。 如果服务器资源移至其他节点，某些操作（例如长时间运行的查询、刷新操作和查询扩展同步）可能会失败。 此方案中的常见错误消息包括：

- "尝试查找长时间运行的 XMLA 请求时出错。 请求可能已被服务升级或服务器重新启动中断。 "
- 由于服务错误（非<guid>活动），<database>模型 "" 的作业 ID "已取消，消息为" 正在取消刷新请求，因为它在没有任何更新的情况下停滞。 这是一个内部服务问题。 如果此问题重复出现，请重新提交作业或提交票证以获取帮助。 "

有很多原因会导致长时间运行的操作中断。 例如，Azure 中的更新如下： 
- 操作系统修补程序 
- 安全更新
- Azure Analysis Services 服务更新
- Service Fabric 更新。 Service Fabric 是由许多 Microsoft 云服务使用的平台组件，包括 Azure Analysis Services。

除了服务中发生的更新外，由于负载平衡，还会在节点上自然移动服务。 节点移动是云服务的预期组成部分。 Azure Analysis Services 尝试最大程度地减少节点移动影响，但无法完全消除它们。 

除了节点移动以外，还可以发生其他故障。 例如，数据源数据库系统可能处于脱机状态或网络连接中断。 如果在刷新过程中，分区包含10000000行，而在9秒行发生失败，则无法在故障点重新启动刷新。 服务必须从头开始重新启动。 

## <a name="refresh-rest-api"></a>刷新 REST API

对于长时间运行的操作（例如数据刷新），服务中断可能会很困难。 Azure Analysis Services 包含一个 REST API 来帮助减轻服务中断造成的负面影响。 若要了解详细信息，请参阅[REST API 的异步刷新](analysis-services-async-refresh.md)。
 
除了 REST API 之外，还可以使用其他方法来最大程度地减少长时间运行的刷新操作过程中出现的潜在问题。 主要目标是避免从头开始重新启动刷新操作，而是以较小的批处理执行刷新，可以分阶段提交。 
 
REST API 允许这样的重新启动，但它不允许分区创建和删除的完全灵活性。 如果某个方案需要复杂的数据管理操作，则解决方案应在其逻辑中包含某种形式的批处理。 例如，通过使用事务处理多个中的数据，单独的批处理允许失败，而不需要从中间检查点重新启动。 
 
## <a name="scale-out-query-replicas"></a>横向扩展查询副本

无论是使用 REST 逻辑还是自定义逻辑，在处理批处理时，客户端应用程序查询仍可能返回不一致或中间的结果。 如果在处理过程中，客户端应用程序查询所返回的数据是一致的，并且模型数据处于中间状态，则可以使用具有只读查询副本的[横向扩展](analysis-services-scale-out.md)。

通过使用只读查询副本，在批处理中执行刷新时，客户端应用程序用户可以继续查询只读副本上数据的旧快照。 完成刷新后，可以执行同步操作，使只读副本保持最新。


## <a name="next-steps"></a>后续步骤

[使用 REST API 执行异步刷新](analysis-services-async-refresh.md)  
[Azure Analysis Services 横向扩展](analysis-services-scale-out.md)  
[Analysis Services 高可用性](analysis-services-bcdr.md)  
[Azure 服务的重试指南](https://docs.microsoft.com/azure/architecture/best-practices/retry-service-specific)   

