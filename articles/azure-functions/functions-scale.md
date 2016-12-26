---
title: "如何缩放 Azure Functions | Microsoft Docs"
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
ms.date: 08/03/2016
ms.author: dariagrigoriu
translationtype: Human Translation
ms.sourcegitcommit: 1a8e8bdb95b1eff6feb992727d28051463710813
ms.openlocfilehash: fce766358c61494e77464a4506dcc0e466e4e1e4


---
# <a name="scaling-azure-functions"></a>缩放 Azure Functions

## <a name="introduction"></a>介绍

Azure Functions 平台会在代码运行时分配计算能力，根据需要增加以处理负载，而代码未运行时会相应减少。 这表示不需为空闲 VM 付费，或者在需要使用它前保留容量。 这项功能的机制是消耗量服务计划。 本文提供消耗量服务计划工作原理的概述。 

如果尚不熟悉 Azure Functions，请参阅 [Azure Functions 概述](functions-overview.md)一文。

## <a name="choose-a-service-plan"></a>选择服务计划

创建 Function App 时，必须为应用中包含的函数配置托管计划。 可用的托管计划有：**消耗量计划**和[应用服务计划](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)。 目前，必须在创建 Function App 的过程中进行此选择。 创建后，不能在这两个选项之间进行更改。 可以在[应用服务计划](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)上的层之间进行缩放。 消耗量计划当前不支持任何更改，因为缩放是动态的。

### <a name="consumption-plan"></a>消耗量计划

在“消耗量计划”中，将 Function App 分配给计算处理实例。 如果需要，会动态添加或删除更多实例。 此外，函数并行运行，以最大程度地减少处理请求所需的总时间。 每个函数的执行时间由包含的 Function App 汇总。 成本由内存大小和 Function App 中所有函数的总执行时间产生（以 GB/秒衡度量）。 如果你的计算需求是间歇性的或作业时间往往很短，这会是很好的选择，因为它允许你仅在实际使用计算资源时为其付费。 

### <a name="app-service-plan"></a>应用服务计划

在“应用服务计划”中，Function App 将在专用 VM 上运行，类似如今 Web 应用用于基本、标准或高级 SKU。 专用 VM 将分配到应用服务应用和 Function App，并且无论是否正在主动执行代码，其始终可用。 如果具有已在运行其他代码的现有、未充分利用的 VM，或希望持续或几乎持续运行函数，这将是个不错的选择。 VM 将运行时和内容大小进行成本分离。 结果是，可以将许多长时间运行的函数的成本限制为在其上运行的一个或多个 VM 的成本。

## <a name="consumption-service-plan"></a>消耗量服务计划

通过基于 Function App 中函数的运行时需求添加额外的处理实例，消耗量服务计划自动缩放 CPU 和内存资源。 会为每个 Function App 处理实例分配最多 1.5GB 的内存资源。

### <a name="runtime-scaling"></a>运行时缩放

Azure Functions 平台使用中央侦听器，基于配置的触发器来评估计算需求，并决定何时扩大或缩小。 中央侦听器不断处理内存需求的提示并触发特定数据点。 例如，在 Azure 队列存储触发的情况下，数据点包括队列长度和最早项的排队时间。

![](./media/functions-scale/central-listener.png)

缩放单位是 Function App。 在这种情况下扩大意味着添加 Function App 的更多实例。 相反，当计算需求减少时，会删除 Function App 实例 - 最终在没有任何实例运行时缩小为零。 

### <a name="billing-model"></a>计费模式

消耗量服务计划的计费在 [Azure Functions 定价页](https://azure.microsoft.com/pricing/details/functions)中有详细介绍。 按 Function App 报告使用情况，仅报代码告执行时的时间。 以下是计费单位： 
* **资源消耗量的单位是 GB-s（千兆字节/每秒）**，以内存大小和 Function App 中运行的所有函数的执行时间组合计算得出。 
* 每当为响应绑定触发的事件而执行函数时，计算一次“执行”。



<!--HONumber=Nov16_HO4-->


