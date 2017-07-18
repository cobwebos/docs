---
title: "常见自动缩放模式的概述 | Microsoft Docs"
description: "了解一些可在 Azure 中自动缩放资源的常见模式。"
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: d37d3fda-8ef1-477c-a360-a855b418de84
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2017
ms.author: ancav
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: c697b49769ffbbb4aee7a482bb067990b893490c
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017

---
# <a name="overview-of-common-autoscale-patterns"></a>常见自动缩放模式的概述
本文介绍一些常见模式，这些模式可在 Azure 中缩放资源。

Azure Monitor 自动缩放仅适用于虚拟机规模集 (VMSS)、云服务、应用服务计划和应用服务环境。 

# <a name="lets-get-started"></a>让我们开始

本文假定你熟悉自动缩放。 可以[开始在此处缩放资源][1]。 下面是一些常见缩放模式。

## <a name="scale-based-on-cpu"></a>基于 CPU 进行缩放

你拥有 Web 应用（/VMSS/云服务角色）并且 

- 要基于 CPU 进行扩大/缩小。
- 此外，你希望确保实例数最小。 
- 同时，希望确保为可以扩展到的实例数设置最大限制。

![基于 CPU 进行缩放][2]

## <a name="scale-differently-on-weekdays-vs-weekends"></a>在工作日与周末以不同方式缩放

你拥有 Web 应用（/VMSS/云服务角色）并且

- 默认情况下想要 3 个实例（在工作日）
- 你不希望在周末有流量，因此希望在周末减少到 1 个实例。

![在工作日与周末以不同方式缩放][3]

## <a name="scale-differently-during-holidays"></a>在节假日期间以不同方式缩放

你拥有 Web 应用（/VMSS/云服务角色）并且 

- 希望在默认情况下基于 CPU 使用率向上扩展/向下扩展
- 但是，在节假日（或对于业务非常重要的特定日子）期间你想要覆盖默认值，并希望有更多容量可由你支配。

![在节假日以不同方式缩放][4]

## <a name="scale-based-on-custom-metric"></a>基于自定义指标进行缩放

你有一个 Web 前端和一个可与后端通信的 API 层。 

- 你想要基于前端中的自定义事件缩放 API 层（示例：想要基于购物车中的项目数缩放结账进程）

![基于自定义指标进行缩放][5]

<!--Reference-->
[1]: ./monitoring-autoscale-get-started.md
[2]: ./media/monitoring-autoscale-common-scale-patterns/scale-based-on-cpu.png
[3]: ./media/monitoring-autoscale-common-scale-patterns/weekday-weekend-scale.png
[4]: ./media/monitoring-autoscale-common-scale-patterns/holidays-scale.png
[5]: ./media/monitoring-autoscale-common-scale-patterns/custom-metric-scale.png
