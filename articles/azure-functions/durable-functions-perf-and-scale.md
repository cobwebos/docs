---
title: "Durable Functions 中的性能和缩放 - Azure"
description: "Azure Functions 的 Durable Functions 扩展简介。"
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 10ce74097388a0283797e4692126c5039e8d4dd0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="performance-and-scale-in-durable-functions-azure-functions"></a>Durable Functions 中的性能和缩放 (Azure Functions)

若要优化性能和可伸缩性，必须了解 [Durable Functions](durable-functions-overview.md) 的独特缩放特征。

若要了解缩放行为，必须了解 Durable Functions 所用的底层 Azure 存储提供程序的某些详细信息。

## <a name="history-table"></a>历史记录表

历史记录表是一个 Azure 存储表，包含所有业务流程实例的历史记录事件。 当实例运行时，会在此表中添加新行。 此表的分区键派生自业务流程的实例 ID。 这些值在大多数情况下是随机的，确保在 Azure 存储中以最佳方式分配内部分区。

## <a name="internal-queue-triggers"></a>内部队列触发器

业务流程协调程序函数和活动函数都由函数应用默认存储帐户中的内部队列触发。 Durable Functions 中有两种类型的队列：**控制队列**和**工作项队列**。

### <a name="the-work-item-queue"></a>工作项队列

Durable Functions 中的每个任务中心都有一个工作项队列。 这是一个基本队列，其行为类似于 Azure Functions 中的其他任何 `queueTrigger` 队列。 此队列用于触发无状态活动函数。 当 Durable Functions 应用程序横向扩展到多个 VM 时，所有这些 VM 将会竞争，以从工作项队列中获取工作。

### <a name="control-queues"></a>控制队列

相比较为简单的工作项队列，控制队列更加复杂。 它用于触发有状态的业务流程协调程序函数。 由于业务流程协调程序函数实例是有状态的单一实例，无法使用竞争性使用者模型在 VM 之间分配负载。 而是，业务流程协调程序消息会在多个控制队列中进行负载均衡。 后续部分将会更详细地介绍相关信息。

控制队列包含各种业务流程生命周期消息类型。 示例包括[业务流程协调程序控制消息](durable-functions-instance-management.md)、活动函数响应消息和计时器消息。

## <a name="orchestrator-scale-out"></a>业务流程协调程序横向扩展

活动函数是无状态的，可通过添加 VM 自动进行横向扩展。 另一方面，业务流程协调程序函数已在一个或多个控制队列中分区。 控制队列的数目是固定的，开始创建负载后无法更改此数目。

横向扩展到多个函数主机实例（通常在不同的 VM 上）时，每个实例会获取某个控制队列上的锁。 此锁确保一个业务流程实例每次只在一个 VM 上运行。 这意味着，如果为任务中心配置了三个控制队列，则最多可在三个 VM 上对业务流程实例进行负载均衡。 可以添加更多的 VM，以提高活动函数执行容量。  但是，不会将多出的资源用于运行业务流程协调程序函数。

下图演示了 Azure Functions 主机如何与横向扩展环境中的存储实体交互。

![缩放示意图](media/durable-functions-perf-and-scale/scale-diagram.png)

可以看到，所有 VM 都可竞争工作项队列中的消息。 但是，只有三个 VM 可从控制队列获取消息，每个 VM 锁定了单个控制队列。

业务流程实例针对业务流程的实例 ID 运行内部哈希函数，在控制队列实例之间分布。 默认情况下，实例 ID 是自动生成的且是随机的，确保在所有可用的控制队列之间均衡实例。 支持的控制队列分区的当前默认数目为 **4**。

> [!NOTE]
> 目前无法在 Azure Functions 中配置分区数。 [我们正在努力跟进此配置选项的支持工作](https://github.com/Azure/azure-functions-durable-extension/issues/73)。

一般而言，业务流程协调程序函数是轻量型的，应该不需要大量的计算能力。 因此，无需创建大量的控制队列分区即可获得极佳的吞吐量。 相反，大部分繁重工作将在可无限横向扩展的无状态活动函数中完成。

## <a name="auto-scale"></a>自动缩放

与消耗计划中运行的所有 Azure Functions 一样，Durable Functions 支持通过 [Azure Functions 缩放控制器](https://docs.microsoft.com/azure/azure-functions/functions-scale#runtime-scaling)自动缩放。 缩放控制器监视工作项队列和每个控制队列的长度，可相应地添加或删除 VM 资源。 如果控制队列的长度逐渐增大，缩放控制器会持续添加实例，直到达到控制队列分区计数。 如果工作项队列的长度逐渐增大，缩放控制器会持续添加 VM 资源，直到能够与负载相匹配，而不考虑控制队列分区计数。

## <a name="thread-usage"></a>线程用量

业务流程协调程序函数在单个线程上执行。 这就需要确保业务流程协调程序函数执行是确定性的。 有鉴于此，绝对不能让业务流程协调程序函数不必要地忙于执行 I/O（出于各种原因受到禁止）、阻塞或旋转操作等任务。 应将需要 I/O、阻塞或多个线程的任何工作移到活动函数中。

活动函数的行为与队列触发的正则函数完全相同。 这意味着，它们可以安全地执行 I/O、执行 CPU 密集型操作和使用多个线程。 由于活动触发器是无状态的，因此可以任意横向扩展到不限数量的 VM。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [安装 Durable Functions 扩展和示例](durable-functions-install.md)
