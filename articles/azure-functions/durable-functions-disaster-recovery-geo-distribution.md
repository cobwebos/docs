---
title: Durable Functions 中的灾难恢复和异地分发 - Azure
description: 了解 Durable Functions 中的灾难恢复和异地分发
services: functions
author: MS-Santi
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: 8eb42a60045304416ec6aa1099a84b1e264c692d
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2018
---
# <a name="disaster-recovery-and-geo-distribution"></a>灾难恢复和异地分发

## <a name="overview"></a>概述
在 Azure Durable Functions 中，所有状态保留在 Azure 存储中。 [任务中心](durable-functions-task-hubs.md)是用于业务流程的 Azure 存储资源的逻辑容器。 只有当业务流程协调程序函数与活动函数属于同一任务中心时，它们才能彼此进行交互。
所述的方案会提议一些部署选项，以提高可用性，并在灾难恢复活动期间尽量减少停机时间。
必须注意，这些方案基于“主动-被动”配置，因为它们是使用 Azure 存储引导执行的。 此模式的操作包括将一个后备（被动）函数应用部署到不同的区域。 流量管理器将监视主要（主动）函数应用的可用性。 如果主要应用发生故障，流量管理器会故障转移到后备函数应用。 有关详细信息，请参阅[流量管理器](https://azure.microsoft.com/services/traffic-manager/)的[优先级流量路由方法](../traffic-manager/traffic-manager-routing-methods.md#a-name--priorityapriority-traffic-routing-method)。


>[!NOTE]
>- 提议的“主动-被动”配置可确保客户端始终能够通过 HTTP 触发新业务流程。 但是，由于两个函数应用共享同一个存储，因此后台处理负载将分散在两者之间，导致争用相同队列中的消息。 对辅助函数应用使用此配置会产生额外的传出费用。
>- 基础存储帐户和任务中心在主要区域中创建，并由两个函数应用共享。
>- 如果通过 HTTP 激活，冗余部署的所有函数应用必须共享相同的函数访问密钥。 Functions 运行时公开[管理 API](https://github.com/Azure/azure-functions-host/wiki/Key-management-API)，让使用者以编程方式添加、删除和更新函数密钥。

## <a name="scenario-1---load-balanced-compute-with-shared-storage"></a>方案 1 - 使用共享存储进行负载均衡的计算
如果 Azure 中的计算基础结构出现故障，函数应用可能不可用。 为了尽量减少出现这种停机的可能性，此方案使用了不同区域中部署的两个函数应用。 流量管理器配置为检测主要函数应用中的问题，并自动将流量重定向到次要区域中的函数应用。 此函数应用共享相同的 Azure 存储帐户和任务中心。 因此，函数应用的状态不会丢失，并且可以正常恢复工作。 主要区域中的运行状况恢复后，Azure 流量管理器会自动开始将请求路由到该函数应用。


![显示方案 1 的示意图。](media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario01.png)

使用此部署方案可获得多种好处：
- 如果计算基础结构出现故障，可以在故障转移区域中恢复工作，且不丢失状态。
- 流量管理器负责自动故障转移到正常的函数应用。
- 服务中断得到解决后，流量管理器会自动在主要函数应用中重建流量。

但是，使用此方案时需要考虑以下问题：
- 如果函数应用是使用专用应用服务计划部署的，则在故障转移数据中心复制计算基础结构会增加成本。
- 此方案考虑到了计算基础结构的中断，但存储帐户仍旧是函数应用的单一故障点。 如果出现存储中断，应用程序会遭遇停机。
- 如果函数应用已故障转移，则延迟会增大，因为它会跨区域访问其存储帐户。
- 由于网络出口流量方面的原因，从其他区域（不是函数应用所在的区域）访问存储服务会产生更高的成本。
- 此方案依赖于流量管理器。 考虑到[流量管理器的工作原理](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works)，使用 Durable Function 的客户端应用程序可能在一段时间之后才需要再次从流量管理器查询函数应用地址。 


## <a name="scenario-2---load-balanced-compute-with-regional-storage"></a>方案 2 - 使用区域存储进行负载均衡的计算
前面的方案仅考虑到了计算基础结构发生故障的情况。 如果存储服务出现故障，将会导致函数应用中断。
为确保 Durable Functions 的持续运行，此方案在函数应用部署到的每个区域中使用了一个本地存储帐户。

![显示方案 2 的示意图。](media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario02.png)

此方法在前一方案的基础上做了改进：
- 如果函数应用出现故障，流量管理器会负责故障转移到次要区域。 但是，由于函数应用依赖于其自身的存储帐户，因此 Durable Functions 可继续工作。
- 在故障转移期间，故障转移区域中的延迟不会增大，因为函数应用和存储帐户是并置的。
- 存储层故障会导致 Durable Functions 故障，从而又会触发到故障转移区域的重定向。 同样，由于函数应用和存储已按区域隔离，因此 Durable Functions 可继续工作。
 
此方案的重要注意事项：
- 如果函数应用是使用专用应用服务计划部署的，则在故障转移数据中心复制计算基础结构会增加成本。
- 当前状态不会故障转移，这意味着，执行和检查点函数会失败。 客户端应用程序负责重试/重启工作。

## <a name="scenario-3---load-balanced-compute-with-grs-shared-storage"></a>方案 3 - 使用 GRS 共享存储进行负载均衡的计算
此方案在第一种方案的基础上做了修改，它实施共享存储帐户。 主要区别在于，存储帐户是在启用异地复制的情况下创建的。
从功能上讲，此方案提供的优势与方案 1 相同，但它可以实现更大的数据恢复优势：
- 异地冗余存储 (GRS) 和读取访问 GRS (RA-GRS) 可最大程度地提高存储帐户的可用性。
- 如果存储服务发生区域中断，一种可能的解决方法是让数据中心运营部门确定是否必须将存储故障转移到次要区域。 在这种情况下，存储帐户访问权限将以透明方式定向到存储帐户的异地复制副本，且无需用户干预。
- 在这种情况下，Durable Functions 的状态最长可保留到上次复制存储帐户为止（存储帐户每隔几分钟就会复制一次）。
与其他方案一样，需注意以下重要事项：
- 故障转移到副本由数据中心操作员完成，可能需要一段时间。 在完成之前，函数应用会遭遇中断。
- 使用异地复制的存储帐户不会增加成本。
- GRS 以异步方式进行。 由于复制过程中的延迟，某些最新事务可能会丢失。

![显示方案 3 的示意图。](media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario03.png)


## <a name="next-steps"></a>后续步骤

详细了解如何[使用 RA-GRS 设计高度可用的应用程序](../storage/common/storage-designing-ha-apps-with-ragrs.md)
