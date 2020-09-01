---
title: Azure Durable Functions 中的灾难恢复和异地分布
description: 了解 Durable Functions 中的灾难恢复和异地分发
author: MS-Santi
ms.topic: conceptual
ms.date: 08/27/2020
ms.author: azfuncdf
ms.openlocfilehash: 01c400f51cce85ef39e9d39bcad1221253c6942d
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2020
ms.locfileid: "89071204"
---
# <a name="disaster-recovery-and-geo-distribution-in-azure-durable-functions"></a>Azure Durable Functions 中的灾难恢复和异地分布

Microsoft 致力于确保 Azure 服务一直可用。 不过，可能会发生计划外服务中断。 如果你的应用程序需要复原，Microsoft 建议为你的应用配置异地冗余。 此外，客户还应制定用于处理区域服务中断的灾难恢复计划。 灾难恢复计划的一个重要部分是准备故障转移到应用的辅助副本，并在主副本变得不可用时进行存储。

在 Durable Functions 中，默认情况下，所有状态都保存在 Azure 存储中。 [任务中心](durable-functions-task-hubs.md)是用于[业务流程](durable-functions-types-features-overview.md#orchestrator-functions)和[实体](durable-functions-types-features-overview.md#entity-functions)的 Azure 存储资源的逻辑容器。 当 Orchestrator、activity 和 entity 函数属于同一任务中心时，它们只能相互交互。 本文介绍了如何将这些 Azure 存储资源保持高度可用的方案。

可以使用通过 HTTP 或其他受支持的 Azure Functions 触发器类型之一触发的 [客户端函数](durable-functions-types-features-overview.md#client-functions) 触发业务流程和实体。 还可以使用 [内置的 HTTP api](durable-functions-http-features.md#built-in-http-apis)触发它们。 为简单起见，本文将重点介绍涉及 Azure 存储和基于 HTTP 的函数触发器的方案，以及在灾难恢复活动期间提高可用性和最小化停机时间的选项。 其他触发器类型（如服务总线或 Cosmos DB 触发器）将不会明确覆盖。

以下方案基于主动-被动配置，因为它们由 Azure 存储的使用来指导。 此模式的操作包括将一个后备（被动）函数应用部署到不同的区域。 流量管理器将监视主 (active) 函数应用的 HTTP 可用性。 如果主要应用发生故障，流量管理器会故障转移到后备函数应用。 有关详细信息，请参阅 [Azure 流量管理器](https://azure.microsoft.com/services/traffic-manager/)的 [优先级流量路由方法。](../../traffic-manager/traffic-manager-routing-methods.md#priority-traffic-routing-method)

> [!NOTE]
> - 提议的“主动-被动”配置可确保客户端始终能够通过 HTTP 触发新业务流程。 但是，由于两个 function app 在存储中共享同一任务中心，某些后台存储事务将在这两个事务之间分布。 因此，此配置会对辅助函数应用产生一些额外的出口费用。
> - 基础存储帐户和任务中心在主要区域中创建，并由两个函数应用共享。
> - 在通过 HTTP 激活的情况下，所有冗余部署的函数应用必须共享相同的功能访问密钥。 Functions 运行时公开[管理 API](https://github.com/Azure/azure-functions-host/wiki/Key-management-API)，让使用者以编程方式添加、删除和更新函数密钥。 使用 [Azure 资源管理器 api](https://www.markheath.net/post/managing-azure-functions-keys-2)也可以实现密钥管理。

## <a name="scenario-1---load-balanced-compute-with-shared-storage"></a>方案 1 - 使用共享存储进行负载均衡的计算

如果 Azure 中的计算基础结构出现故障，函数应用可能不可用。 为了尽量减少出现这种停机的可能性，此方案使用了不同区域中部署的两个函数应用。
流量管理器配置为检测主要函数应用中的问题，并自动将流量重定向到次要区域中的函数应用。 此函数应用共享相同的 Azure 存储帐户和任务中心。 因此，函数应用的状态不会丢失，并且可以正常恢复工作。 主要区域中的运行状况恢复后，Azure 流量管理器会自动开始将请求路由到该函数应用。

![显示方案 1 的示意图。](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario01.png)

使用此部署方案可获得多种好处：

- 如果计算基础结构出现故障，工作可以在故障转移区域中恢复，而不会丢失数据。
- 流量管理器会自动将自动故障转移到正常运行的函数应用。
- 服务中断得到解决后，流量管理器会自动在主要函数应用中重建流量。

但是，使用此方案时需要考虑以下问题：

- 如果使用专用应用服务计划部署函数应用，则在故障转移数据中心复制计算基础结构会增加成本。
- 此方案考虑到了计算基础结构的中断，但存储帐户仍旧是函数应用的单一故障点。 如果发生存储中断，应用程序会造成停机。
- 如果函数应用已故障转移，则延迟会增大，因为它会跨区域访问其存储帐户。
- 由于网络出口流量方面的原因，从其他区域（不是函数应用所在的区域）访问存储服务会产生更高的成本。
- 此方案依赖于流量管理器。 考虑到[流量管理器的工作原理](../../traffic-manager/traffic-manager-how-it-works.md)，使用 Durable Function 的客户端应用程序可能在一段时间之后才需要再次从流量管理器查询函数应用地址。

> [!NOTE]
> 从 Durable Functions 扩展的 **v 2.3.0** 开始，可以通过相同的存储帐户和任务中心配置，同时安全地运行两个 function app。 第一个要启动的应用程序将获取应用程序级别的 blob 租约，以防止其他应用从任务中心队列中偷窃消息。 如果第一个应用停止运行，则其租约将过期并可通过第二个应用程序获取，后者随后将继续处理任务中心消息。
> 
> 在 2.3.0 v 之前，配置为使用同一存储帐户的函数应用将同时处理消息和更新存储项目，导致总体延迟和传出成本更高。 如果在主应用和副本应用上部署了不同的代码（即使暂时这样做），则业务流程也可能无法正确执行，因为这两个应用的业务流程协调程序不一致。 因此，建议所有需要地理分布以进行灾难恢复的应用使用 v 2.3.0 或更高版本的持久扩展。

## <a name="scenario-2---load-balanced-compute-with-regional-storage"></a>方案 2 - 使用区域存储进行负载均衡的计算

前面的方案仅考虑到了计算基础结构发生故障的情况。 如果存储服务出现故障，将会导致函数应用中断。
为确保 Durable Functions 的持续运行，此方案在函数应用部署到的每个区域中使用了一个本地存储帐户。

![显示方案 2 的示意图。](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario02.png)

此方法在前一方案的基础上做了改进：

- 如果函数应用出现故障，流量管理器会负责故障转移到次要区域。 但是，由于函数应用依赖于其自身的存储帐户，因此 Durable Functions 可继续工作。
- 在故障转移期间，故障转移区域没有额外的延迟，因为函数应用和存储帐户被定位。
- 存储层发生故障将导致持久性函数出错，进而导致重定向到故障转移区域。 同样，由于函数应用和存储已按区域隔离，因此 Durable Functions 可继续工作。

此方案的重要注意事项：

- 如果使用专用应用服务计划部署函数应用，则在故障转移数据中心复制计算基础结构会增加成本。
- 当前状态不会进行故障转移，这意味着现有的业务流程和实体将有效地暂停，并且在主区域恢复前不可用。

总而言之，第一种和第二种方案的折衷在于，延迟被保留，传出成本会降到最低，但在停机期间现有的业务流程和实体将不可用。 这些权衡是否可接受取决于应用程序的要求。

## <a name="scenario-3---load-balanced-compute-with-grs-shared-storage"></a>方案 3 - 使用 GRS 共享存储进行负载均衡的计算

此方案在第一种方案的基础上做了修改，它实施共享存储帐户。 主要区别在于，创建的存储帐户已启用异地复制。
从功能上讲，此方案提供的优势与方案 1 相同，但它可以实现更大的数据恢复优势：

- 异地冗余存储 (GRS) 和读取访问 GRS (RA-GRS) 可最大程度地提高存储帐户的可用性。
- 如果存储服务发生地区性中断，可以 [手动启动到辅助副本的故障转移](../../storage/common/storage-initiate-account-failover.md)。 在由于重大灾难而导致区域丢失的极端情况下，Microsoft 可能会启动区域故障转移。 在此情况下，不需要采取任何操作。
- 发生故障转移时，持久性函数的状态将被保留到存储帐户的上一次复制中，这通常每隔几分钟发生一次。

与其他方案一样，需注意以下重要事项：

- 故障转移到副本可能需要一段时间。 在故障转移完成并更新 Azure 存储 DNS 记录之前，该函数应用将会发生中断。
- 使用异地复制的存储帐户不会增加成本。
- GRS 复制以异步方式复制数据。 由于复制过程中的延迟，某些最新事务可能会丢失。

![显示方案 3 的示意图。](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario03.png)

> [!NOTE]
> 如方案1中所述，强烈建议使用此策略部署的函数应用使用 **v 2.3.0** 或更高版本的 Durable Functions 扩展。

有关详细信息，请参阅 [Azure 存储灾难恢复和存储帐户故障转移](../../storage/common/storage-disaster-recovery-guidance.md) 文档。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [详细了解如何在 Azure 存储中设计高度可用的应用程序](../../storage/common/geo-redundant-design.md)
