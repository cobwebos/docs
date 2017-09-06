---
title: "Azure Functions 消耗计划和应用服务计划 | Microsoft Docs"
description: "了解 Azure Functions 如何进行扩展，满足事件驱动工作负荷的需求。"
services: functions
documentationcenter: na
author: lindydonna
manager: cfowler
editor: 
tags: 
keywords: "Azure Functions, Functions, 事件处理, webhook, 动态计算, 无服务体系结构"
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 06/12/2017
ms.author: glenga
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 1c730c65194e169121e3ad1d1423963ee3ced8da
ms.openlocfilehash: 0e677fb35279d155241a95cd5f33b63e8294fad2
ms.contentlocale: zh-cn
ms.lasthandoff: 08/30/2017

---
# <a name="azure-functions-consumption-and-app-service-plans"></a>Azure Functions 消耗计划和应用服务计划 

## <a name="introduction"></a>介绍

可在两种不同模式下运行 Azure Functions：消耗量计划和 Azure 应用服务计划。 代码运行时，消耗计划会自动分配计算能力，根据处理负载的需要进行扩展，然后在代码停止运行时进行缩减。 因此，无需为空闲的 VM 付费，且无需提前保留容量。 本文重点介绍消耗计划。 如需详细了解如何使用应用服务计划，请参阅 [Azure 应用服务计划深入概述](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)。 

如果不熟悉 Azure Functions，请参阅 [Azure Functions 概述](functions-overview.md)。

创建函数应用时，必须为应用中包含的函数配置托管计划。 在任一模式下，Azure Functions 主机实例均可执行函数。 计划类型可控制：

* 主机实例的扩展方式。
* 可供每个主机使用的资源。

目前，创建函数应用时必须选择计划类型。 之后不能再进行更改。 

可在应用服务计划上的层之间进行缩放。 对于消耗计划，Azure Functions 会自动处理所有资源分配。

## <a name="consumption-plan"></a>消耗量计划

使用消耗计划时，会根据传入事件数自动添加和删除 Azure Functions 主机实例。 此计划会自动缩放，仅当函数运行时，才会产生计算资源费用。 对于消耗计划，一个函数最长可运行 10 分钟。 

> [!NOTE]
> 对于消耗量计划，函数的默认超时时间为 5 分钟。 可通过更改 [host.json](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json)中的属性 `functionTimeout`，将函数应用的该值增加到 10 分钟。

费用以执行时间和使用的内存量为基础，且会针对函数应用中的所有函数累积。 有关详细信息，请参阅 [Azure Functions 定价页]。

消耗计划是默认设置，它提供以下优势：
- 仅当函数运行时才产生费用。
- 即使是在负载较高期间也可自动扩展。

## <a name="app-service-plan"></a>应用服务计划

在应用服务计划中，函数应用在基本、标准或高级 SKU 中的专用 VM 上运行，类似于 Web 应用。 专用 VM 将分配到应用服务应用，这意味着函数主机始终会运行。

对于以下情况，可以考虑使用应用服务计划：
- 具有已运行其他应用服务实例的、未充分利用的现成 VM。
- 预期函数应用会持续或几乎持续运行。 在这种情况下，应用服务计划可能更经济高效。
- 所需 CPU 或内存选项超出消耗计划提供的选项。
- 运行时间超过消耗计划允许的最长执行时间。
- 需要仅对应用服务计划可用的功能，例如应用服务环境支持、VNET/VPN 连接性和更大的 VM。 

VM 将运行时和内容大小进行成本分离。 因此，支付的费用不会超过分配的 VM 实例的费用。 如需详细了解如何使用应用服务计划，请参阅 [Azure 应用服务计划深入概述](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)。 

借助应用服务计划，可通过添加更多 VM 实例手动进行扩展，也可启用自动缩放。 有关详细信息，请参阅[手动或自动缩放实例计数](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service-web%2ftoc.json)。 还可以通过选择不同的应用服务计划来增加。 有关详细信息，请参阅[增加 Azure 中的应用](../app-service-web/web-sites-scale.md)。 如果计划在应用服务计划上运行 JavaScript 函数，则应选择具有较少核心的计划。 有关详细信息，请参阅[函数的 JavaScript 参考](functions-reference-node.md#choose-single-core-app-service-plans)。  

<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 
<a name="always-on"></a>
### 始终可用

如果在应用服务计划上运行，应该启用“始终可用”设置，使函数应用能正常运行。 在应用服务计划中，若函数运行时处于不活动状态，几分钟后就会进入空闲状态，因此只有 HTTP 触发器才能“唤醒”函数。 必须为 Web 作业启用 Always On 的原因与此类似。 

只能对应用服务计划使用“始终可用”。 在消耗计划中，平台会自动激活函数应用。

## <a name="storage-account-requirements"></a>存储帐户要求

无论是在消耗计划还是应用服务计划中，函数应用都需要一个支持 Azure Blob、队列和表存储的 Azure 存储帐户。 Azure Functions 在内部使用 Azure 存储，执行管理触发器和记录函数执行等操作。 某些存储帐户不支持队列和表，例如仅限 blob 的存储帐户（包括高级存储）和使用区域冗余存储空间复制的常规用途存储帐户。 创建函数应用时，将在“存储帐户”边栏选项卡中筛选这些帐户。

若要了解有关存储帐户类型的详细信息，请参阅 [Azure 存储服务简介](../storage/common/storage-introduction.md#introducing-the-azure-storage-services)。

## <a name="how-the-consumption-plan-works"></a>如何使用消耗量计划

消耗计划通过根据触发函数的事件数添加额外的 Functions 主机实例来自动缩放 CPU 和内存资源。 Functions 主机的每个实例的上限为 1.5 GB 内存。

使用消耗托管计划时，函数代码文件存储在主存储帐户的 Azure 文件共享上。 删除主存储帐户时，此内容将随之删除且无法恢复。

> [!NOTE]
> 在消耗计划中使用 Blob 触发器时，如果函数应用处于空闲状态，则在处理新 Blob 时，可能会出现长达 10 分钟的延迟。 函数应用运行后，就会立即处理 Blob。 若要避免此初始延迟，请考虑以下选项之一：
> - 使用已启用“始终可用”的应用服务计划。
> - 使用另一种机制来触发 Blob 处理，例如包含 Blob 名称的队列消息。 有关示例，请参阅[具有 Blob 输入绑定的队列触发器](functions-bindings-storage-blob.md#input-sample)。

### <a name="runtime-scaling"></a>运行时缩放

Azure Functions 使用名为*缩放控制器*的组件来监视事件率以及确定是要扩展还是缩减。 缩放控制器针对每种触发器类型使用试探法。 例如，使用 Azure 队列存储触发器时，它会根据队列长度和最旧队列消息的期限进行缩放。

缩放单位是 Function App。 扩展函数应用时，将分配更多的资源来运行多个 Azure Functions 主机的实例。 相反，计算需求下降时，扩展控制器将删除函数主机实例。 实例数最终会缩减为零，此时 Function App 中没有任何函数运行。

![用于监视事件和创建实例的扩展控制器](./media/functions-scale/central-listener.png)

### <a name="billing-model"></a>计费模式

消耗量计划的计费在 [Azure Functions 定价页]中有详细介绍。 使用量在函数应用级别合计得出，仅计算函数代码运行的时间。 以下是计费单位： 
* 以千兆字节/秒 (GB-s) 计量的资源消耗量。 根据内存大小和函数应用中所有函数的执行时间组合计算得出。 
* **执行**。 每当为响应绑定触发的事件而执行函数时统计一次。

[Azure Functions 定价页]: https://azure.microsoft.com/pricing/details/functions

