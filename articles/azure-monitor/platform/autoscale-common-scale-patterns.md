---
title: 常见自动缩放模式的概述
description: 了解一些可在 Azure 中自动缩放资源的常见模式。
ms.topic: conceptual
ms.date: 05/07/2017
ms.subservice: autoscale
ms.openlocfilehash: a77cf1704c20abb77d432eab16569071208f6da8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75396097"
---
# <a name="overview-of-common-autoscale-patterns"></a>常见自动缩放模式的概述
本文介绍一些常见模式，这些模式可在 Azure 中缩放资源。

Azure Monitor 自动缩放仅适用于[虚拟机规模集](https://azure.microsoft.com/services/virtual-machine-scale-sets/)、[云服务](https://azure.microsoft.com/services/cloud-services/)、[应用服务 - Web 应用](https://azure.microsoft.com/services/app-service/web/)和 [API 管理服务](https://docs.microsoft.com/azure/api-management/api-management-key-concepts)。

## <a name="lets-get-started"></a>入门

本文假定使用者熟悉自动缩放。 你可以[开始在此处缩放资源][1]。 下面是一些常见缩放模式。

## <a name="scale-based-on-cpu"></a>基于 CPU 进行缩放

拥有 Web 应用（/VMSS/云服务角色）并且

- 要基于 CPU 进行扩大/缩小。
- 此外，你希望确保实例数最小。
- 同时，希望确保为可以扩展到的实例数设置最大限制。

![基于 CPU 进行缩放][2]

## <a name="scale-differently-on-weekdays-vs-weekends"></a>在工作日与周末以不同方式缩放

拥有 Web 应用（/VMSS/云服务角色）并且

- 默认情况下想要 3 个实例（在工作日）
- 不希望在周末有流量，因此希望在周末减少到 1 个实例。

![在工作日与周末以不同方式缩放][3]

## <a name="scale-differently-during-holidays"></a>在节假日期间以不同方式缩放

拥有 Web 应用（/VMSS/云服务角色）并且

- 希望在默认情况下基于 CPU 使用率扩展/缩减
- 但是，在节假日（或对于业务来说非常重要的特定日子）期间你想要覆盖默认值，并希望有更多容量可供支配。

![在节假日以不同方式缩放][4]

## <a name="scale-based-on-custom-metric"></a>基于自定义指标进行缩放

你有一个 web 前端和一个与后端通信的 API 层。

- 想要基于前端中的自定义事件缩放 API 层（示例：想要基于购物车中的项目数缩放结账进程）

![基于自定义指标进行缩放][5]

<!--Reference-->
[1]: ./autoscale-get-started.md
[2]: ./media/autoscale-common-scale-patterns/scale-based-on-cpu.png
[3]: ./media/autoscale-common-scale-patterns/weekday-weekend-scale.png
[4]: ./media/autoscale-common-scale-patterns/holidays-scale.png
[5]: ./media/autoscale-common-scale-patterns/custom-metric-scale.png

