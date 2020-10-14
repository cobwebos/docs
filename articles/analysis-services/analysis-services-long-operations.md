---
title: Azure Analysis Services 中的长时间运行操作的最佳实践 |Microsoft Docs
description: 本文介绍了长时间运行的操作的最佳做法。
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: owend
ms.openlocfilehash: 4e069effae0cb7f834b2c3dac696d05304d841a7
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92014828"
---
# <a name="best-practices-for-long-running-operations"></a>长时间运行的操作的最佳做法

在 Azure Analysis Services 中，节点  表示运行服务器资源的主机虚拟机。 如果服务器资源移至其他节点，某些操作（例如长时间运行的查询、刷新操作、查询横向扩展同步）可能会失败。 此场景中的常见错误消息包括：

- “尝试查找长时间运行的 XMLA 请求时出错。 该请求可能已被服务升级或服务器重启中断。”
- “由于服务错误（不活动），针对模型‘<database>’的 ID 为 <guid> 的作业已取消，并出现消息‘正在取消刷新请求，因为它已停滞，无任何更新。’ 这是一个内部服务问题。 如果此问题重复发生，请重新提交作业或提交票证以获取帮助。”

有很多原因会导致长时间运行的操作被中断。 例如，Azure 中的下述更新： 
- 操作系统修补 
- 安全更新
- Azure Analysis Services 服务更新
- Service Fabric 更新 Service Fabric 是由许多 Microsoft 云服务使用的平台组件，包括 Azure Analysis Services。

除了服务中发生的更新外，由于需要进行负载均衡，服务还会在各个节点之间自然移动。 云服务需要进行节点移动。 Azure Analysis Services 会尝试最大程度地降低节点移动影响，但无法完全消除它们。 

除了节点移动以外，还可能发生其他故障。 例如，数据源数据库系统可能处于脱机状态或网络连接断开。 在刷新过程中，如果某个分区包含 1000 万行，而在第 900 万行出现故障，则无法在故障点重启刷新。 服务必须从头开始重启。 

## <a name="refresh-rest-api"></a>刷新 REST API

对于长时间运行的操作（例如数据刷新），服务中断可能是一个非常棘手的问题。 Azure Analysis Services 包含的 REST API 有助于缓解服务中断造成的负面影响。 若要了解详细信息，请参阅[使用 REST API 执行异步刷新](analysis-services-async-refresh.md)。
 
除了 REST API 之外，还可以使用其他方法，尽量减少长时间运行的刷新操作过程中可能出现的问题。 主要目标是避免从头开始重启刷新操作，改为以较小的批次执行刷新，这些批次可以分阶段提交。 
 
REST API 能实现这样的重启，但不能灵活实现部分创建和删除。 如果某个方案需要复杂的数据管理操作，则解决方案应在其逻辑中包含某种形式的批处理。 例如，通过使用事务在多个单独的批次中处理数据，失败时将不需要从头开始重启，只需从某个中间检查点重启。 
 
## <a name="scale-out-query-replicas"></a>横向扩展查询副本

无论是使用 REST 还是使用自定义逻辑，在处理各个批次时，客户端应用程序查询仍然可能会返回不一致的结果或中间结果。 如果在处理过程中要求客户端应用程序查询返回一致的数据，并且模型数据处于中间状态，则可以使用只读查询副本进行[横向扩展](analysis-services-scale-out.md)。

通过使用只读查询副本，在分批执行刷新时，客户端应用程序用户可以继续在只读副本上查询数据的旧快照。 完成刷新后，可以执行同步操作，使只读副本保持最新。


## <a name="next-steps"></a>后续步骤

[使用 REST API 执行异步刷新](analysis-services-async-refresh.md)  
[Azure Analysis Services 横向扩展](analysis-services-scale-out.md)  
[Analysis Services 高可用性](analysis-services-bcdr.md)  
[Azure 服务的重试指南](/azure/architecture/best-practices/retry-service-specific)