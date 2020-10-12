---
title: Azure Durable Functions 中的灾难恢复和异地分布
description: 了解 Durable Functions 中的灾难恢复和异地分发
author: MS-Santi
ms.topic: conceptual
ms.date: 08/27/2020
ms.author: azfuncdf
ms.openlocfilehash: 01c400f51cce85ef39e9d39bcad1221253c6942d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89071204"
---
# <a name="disaster-recovery-and-geo-distribution-in-azure-durable-functions"></a>Azure Durable Functions 中的灾难恢复和异地分布

Microsoft 致力于确保 Azure 服务一直可用。 不过，可能会发生计划外服务中断。 如果你的应用程序需要复原，Microsoft 建议将应用配置为异地冗余。 此外，客户还应制定用于处理区域服务中断的灾难恢复计划。 灾难恢复计划的一个重要组成部分是，准备在主要副本不可用时将故障转移到应用的次要副本。

在 Durable Functions 中，默认情况下所有状态都保存在 Azure 存储中。 [任务中心](durable-functions-task-hubs.md)是用于[业务流程](durable-functions-types-features-overview.md#orchestrator-functions)和[实体](durable-functions-types-features-overview.md#entity-functions)的 Azure 存储资源的逻辑容器。 只有当业务流程协调程序、活动与实体函数属于同一任务中心时，它们才能彼此进行交互。 本文档在说明保持这些 Azure 存储资源高度可用的方案时，将引用任务中心。

可以使用通过 HTTP 或其他受支持的 Azure Functions 触发器类型之一触发的[客户端函数](durable-functions-types-features-overview.md#client-functions)来触发业务流程和实体。 还可以使用[内置 HTTP API](durable-functions-http-features.md#built-in-http-apis) 来触发它们。 为简单起见，本文将重点介绍涉及 Azure 存储和基于 HTTP 的函数触发器的方案，以及在灾难恢复活动期间增加可用性和最大限度地减少停机时间的选项。 本文不会明确涉及其他触发器类型（如服务总线或 Cosmos DB 触发器）。

以下方案基于“主动-被动”配置，因为它们是使用 Azure 存储引导执行的。 此模式的操作包括将一个后备（被动）函数应用部署到不同的区域。 流量管理器将监视主要（主动）函数应用的 HTTP 可用性。 如果主要应用发生故障，流量管理器会故障转移到后备函数应用。 有关详细信息，请参阅 [Azure 流量管理器](https://azure.microsoft.com/services/traffic-manager/)的[优先级流量路由方法](../../traffic-manager/traffic-manager-routing-methods.md#priority-traffic-routing-method)。

> [!NOTE]
> - 提议的“主动-被动”配置可确保客户端始终能够通过 HTTP 触发新业务流程。 但是，由于两个函数应用在存储中共享同一个任务中心，因此某些后台存储事务将分散在两者之间。 因此，对辅助函数应用使用此配置会产生一些额外的传出费用。
> - 基础存储帐户和任务中心在主要区域中创建，并由两个函数应用共享。
> - 如果通过 HTTP 激活，冗余部署的所有函数应用都必须共享相同的函数访问密钥。 Functions 运行时公开[管理 API](https://github.com/Azure/azure-functions-host/wiki/Key-management-API)，让使用者以编程方式添加、删除和更新函数密钥。 还可以使用 [Azure 资源管理器 API](https://www.markheath.net/post/managing-azure-functions-keys-2) 实现密钥管理。

## <a name="scenario-1---load-balanced-compute-with-shared-storage"></a>方案 1 - 使用共享存储进行负载均衡的计算

如果 Azure 中的计算基础结构出现故障，函数应用可能不可用。 为了尽量减少出现这种停机的可能性，此方案使用了不同区域中部署的两个函数应用。
流量管理器配置为检测主要函数应用中的问题，并自动将流量重定向到次要区域中的函数应用。 此函数应用共享相同的 Azure 存储帐户和任务中心。 因此，函数应用的状态不会丢失，并且可以正常恢复工作。 主要区域中的运行状况恢复后，Azure 流量管理器会自动开始将请求路由到该函数应用。

![显示方案 1 的示意图。](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario01.png)

使用此部署方案可获得多种好处：

- 如果计算基础结构出现故障，可以在故障转移区域中恢复工作，且不会丢失数据。
- 流量管理器负责自动故障转移到正常的函数应用。
- 服务中断得到解决后，流量管理器会自动在主要函数应用中重建流量。

但是，使用此方案时需要考虑以下问题：

- 如果函数应用是使用专用应用服务计划部署的，则在故障转移数据中心复制计算基础结构会增加成本。
- 此方案考虑到了计算基础结构的中断，但存储帐户仍旧是函数应用的单一故障点。 如果出现存储中断，应用程序会遭遇停机。
- 如果函数应用已故障转移，则延迟会增大，因为它会跨区域访问其存储帐户。
- 由于网络出口流量方面的原因，从其他区域（不是函数应用所在的区域）访问存储服务会产生更高的成本。
- 此方案依赖于流量管理器。 考虑到[流量管理器的工作原理](../../traffic-manager/traffic-manager-how-it-works.md)，使用 Durable Function 的客户端应用程序可能在一段时间之后才需要再次从流量管理器查询函数应用地址。

> [!NOTE]
> 从 Durable Functions 扩展的 v2.3.0 开始，可以使用相同的存储帐户和任务中心配置同时安全地运行两个函数应用。 要启动的第一个应用将获取应用程序级 Blob 租约，以防止其他应用从任务中心队列中窃取消息。 如果第一个应用停止运行，其租约将过期，并可由第二个应用获取，然后该应用将继续处理任务中心消息。
> 
> 在 v2.3.0 之前，配置为使用相同的存储帐户的函数应用将同时处理消息并更新存储项目，从而导致更高的总体延迟和传出费用。 如果主应用和复制应用曾经部署过不同的代码（即使是暂时的），则业务流程也可能由于两个应用中的业务流程协调程序函数不一致而无法正确执行。 因此，建议所有需要用于灾难恢复目的的地理分布的应用都使用 Durable 扩展的 v2.3.0 或更高版本。

## <a name="scenario-2---load-balanced-compute-with-regional-storage"></a>方案 2 - 使用区域存储进行负载均衡的计算

前面的方案仅考虑到了计算基础结构发生故障的情况。 如果存储服务出现故障，将会导致函数应用中断。
为确保 Durable Functions 的持续运行，此方案在函数应用部署到的每个区域中使用了一个本地存储帐户。

![显示方案 2 的示意图。](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario02.png)

此方法在前一方案的基础上做了改进：

- 如果函数应用出现故障，流量管理器会负责故障转移到次要区域。 但是，由于函数应用依赖于其自身的存储帐户，因此 Durable Functions 可继续工作。
- 在故障转移期间，故障转移区域中的延迟不会增大，因为函数应用和存储帐户是并置的。
- 存储层故障会导致 Durable Functions 故障，从而又会触发到故障转移区域的重定向。 同样，由于函数应用和存储已按区域隔离，因此 Durable Functions 可继续工作。

此方案的重要注意事项：

- 如果函数应用是使用专用应用服务计划部署的，则在故障转移数据中心复制计算基础结构会增加成本。
- 当前状态不是故障转移，这意味着现有业务流程和实体将有效暂停并不可用，直到主要区域恢复。

总之，第一个和第二个方案之间的权衡是保留延迟和降低传出费用，但现有业务流程和实体在停机期间将不可用。 这些权衡是否可接受取决于应用程序的要求。

## <a name="scenario-3---load-balanced-compute-with-grs-shared-storage"></a>方案 3 - 使用 GRS 共享存储进行负载均衡的计算

此方案在第一种方案的基础上做了修改，它实施共享存储帐户。 主要区别在于，存储帐户是在启用异地复制的情况下创建的。
从功能上讲，此方案提供的优势与方案 1 相同，但它可以实现更大的数据恢复优势：

- 异地冗余存储 (GRS) 和读取访问 GRS (RA-GRS) 可最大程度地提高存储帐户的可用性。
- 如果存储服务发生地区性中断，可以 [手动启动到辅助副本的故障转移](../../storage/common/storage-initiate-account-failover.md)。 在由于重大灾难而导致区域丢失的极端情况下，Microsoft 可能会启动区域故障转移。 在此情况下，不需要采取任何操作。
- 发生故障转移时，Durable Functions 的状态最长可保留到上次复制存储帐户为止（存储帐户通常每隔几分钟就会复制一次）。

与其他方案一样，需注意以下重要事项：

- 故障转移到副本可能需要一段时间。 在故障转移完成和 Azure 存储 DNS 记录更新之前，函数应用将发生中断。
- 使用异地复制的存储帐户不会增加成本。
- GRS 复制以异步方式复制数据。 由于复制过程中的延迟，某些最新事务可能会丢失。

![显示方案 3 的示意图。](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario03.png)

> [!NOTE]
> 如方案 1 中所述，强烈建议通过此策略部署的函数应用使用 v2.3.0 或更高版本的 Durable Functions 扩展。

有关详细信息，请参阅 [Azure 存储灾难恢复和存储帐户故障转移](../../storage/common/storage-disaster-recovery-guidance.md)文档。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [详细了解在 Azure 存储中设计高度可用的应用程序](../../storage/common/geo-redundant-design.md)
