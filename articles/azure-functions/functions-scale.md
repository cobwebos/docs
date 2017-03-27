---
title: "选择 Azure Functions 托管计划 | Microsoft 文档"
description: "了解 Azure Functions 如何满足事件驱动工作负荷的需求。"
services: functions
documentationcenter: na
author: dariagrigoriu
manager: erikre
editor: 
tags: 
keywords: "Azure Functions, Functions, 事件处理, webhook, 动态计算, 无服务体系结构"
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/14/2017
ms.author: dariagrigoriu, glenga
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 9b5dabe5e27e68a4a9f140d4f07131caf7306e32
ms.lasthandoff: 03/15/2017


---
# <a name="choose-the-correct-service-plan-for-azure-functions"></a>为 Azure Functions 选择正确的服务计划

## <a name="introduction"></a>介绍

Azure Functions 有两个不同的服务计划：消耗量计划和应用服务计划。 消耗量计划会在代码运行时自动分配计算能力，根据处理负载的需要扩大规模，在代码不运行时缩小规模。 这表示不需为空闲 VM 付费，或者在需要使用它前保留容量。 本文重点介绍消耗量服务计划。 如需详细了解如何使用应用服务计划，请参阅 [Azure 应用服务计划深入概述](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)。 

如果尚不熟悉 Azure Functions，请参阅 [Azure Functions 概述](functions-overview.md)一文。

## <a name="service-plan-options"></a>服务计划选项

创建 Function App 时，必须为应用中包含的函数配置托管计划。 可用的托管计划有：**消耗量计划**和[应用服务计划](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)。 目前，必须在创建 Function App 的过程中进行此选择。 创建后，不能在这两个选项之间进行更改。 可以在[应用服务计划](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)上的层之间进行缩放。 消耗量计划当前不支持任何更改，因为缩放是动态的。

### <a name="consumption-plan"></a>消耗量计划

在“消耗量计划”中，将 Function App 分配给计算处理实例。 需要时，会动态添加或删除更多实例。 此外，函数并行运行，以最大程度地减少处理请求所需的总时间。 每个函数的执行时间由包含 Function App 汇总。 成本由内存大小和 Function App 中所有函数的总执行时间产生（以 GB-秒进行衡量）。 如果你的计算需求是间歇性的或作业时间往往很短，这会是很好的选择，因为它允许你仅在实际使用计算资源时为其付费。 下一部分详细介绍如何使用消耗量计划。

### <a name="app-service-plan"></a>App Service 计划

在“应用服务计划”中，Function App 将在专用 VM 上运行，类似如今的 Web 应用用于基本、标准或高级 SKU。 专用 VM 将分配到应用服务应用和 Function App，并且无论是否正在主动执行代码，其始终可用。 如果具有已在运行其他代码的现有、未充分利用的 VM，或希望持续或几乎持续运行函数，这将是个不错的选择。 VM 将运行时和内容大小进行成本分离。 结果是，可以将许多长时间运行的函数的成本限制为运行函数的 VM 的成本。 如需详细了解如何使用应用服务计划，请参阅 [Azure 应用服务计划深入概述](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)。 

## <a name="how-the-consumption-plan-works"></a>如何使用消耗量计划

消耗量计划会根据 Function App 中函数的运行时需求添加额外的处理实例，自动缩放 CPU 和内存资源。 会为每个 Function App 处理实例分配最多 1.5 GB 的内存资源。

基于消耗量计划运行时，如果 Function App 处于空闲状态，则在处理新 Blob 时，可能会出现某天耗时长达 10 分钟的情况。 Function App 运行以后，Blob 处理速度会加快。 若要避免这种初始延迟，可以使用常规的应用服务计划并启用“始终可用”，或者使用其他机制来触发 Blob 处理，例如使用包含 Blob 名称的队列消息。 

创建 Function App 时，必须创建或链接支持 Blob、队列和表存储的常规用途的 Azure 存储帐户。 Azure Functions 内部使用 Azure 存储以进行操作（如管理触发器和记录函数执行）。 某些存储帐户不支持队列和表，例如仅限 blob 的存储帐户（包括高级存储）和使用 ZRS 复制的常规用途的存储帐户。 创建新的 Function App 时，将从“存储帐户”边栏选项卡对这些帐户进行筛选。

使用消耗托管计划时，Function App 内容（如函数代码文件和绑定配置）存储在主存储帐户的 Azure 文件共享上。 如果删除主存储帐户，将删除此内容，并且无法恢复。

若要了解有关存储帐户类型的详细信息，请参阅 [Azure 存储服务简介] (.../ storage/storage-introduction.md#introducing-the-azure-storage-services)。

### <a name="runtime-scaling"></a>运行时缩放

Functions 使用缩放控制器，基于配置的触发器来评估计算需求，并决定何时扩大或缩小。 缩放控制器持续处理内存需求的提示和特定于触发器的数据点。 例如，在使用 Azure 队列存储触发器时，数据点包括队列长度和最早项的排队时间。

![](./media/functions-scale/central-listener.png)

缩放单位是 Function App。 在这种情况下扩大意味着添加 Function App 的更多实例。 相反，当计算需求下降时，将删除 Function App 实例。 实例数最终会降到零，此时没有任何实例运行。 

### <a name="billing-model"></a>计费模式

消耗量计划的计费在 [Azure Functions 定价页](https://azure.microsoft.com/pricing/details/functions)中有详细介绍。 只会在代码执行时，根据 Function App 报告使用量。 以下是计费单位： 
* **资源消耗量的单位是 GB-s（千兆字节/每秒）**，以内存大小和 Function App 中运行的所有函数的执行时间组合计算得出。 
* 每当为响应绑定触发的事件而执行函数时，计算一次“执行”。

