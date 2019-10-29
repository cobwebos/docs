---
title: 概念-Azure 事件网格 IoT Edge |Microsoft Docs
description: IoT Edge 上的事件网格中的概念。
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 73309e10e88c11e639e6ac6fd3bb061e1b5c685b
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992543"
---
# <a name="event-grid-concepts"></a>事件网格概念

本文介绍了 Azure 事件网格中的主要概念。

## <a name="events"></a>事件

事件是完全描述系统中所发生情况的最小信息量。 每个事件都具有通用信息，如事件源、事件发生的时间和唯一标识符。 此外，每个事件还具有仅与特定事件类型相关的特定信息。 最大为 1 MB 的事件支持目前处于预览状态。

有关事件中包含的属性，请参阅[Azure 事件网格事件架构](event-schemas.md)。

## <a name="publishers"></a>发布者

发布服务器是决定将事件发送到事件网格的用户或组织。 可以从自己的应用程序发布事件。

## <a name="event-sources"></a>事件源

事件源即事件发生的位置。 每个事件源都与一个或多个事件类型相关。 例如，Azure 存储是 blob 创建事件的事件源。 你的应用程序是你定义的自定义事件的事件源。 事件源负责将事件发送到事件网格。

## <a name="topics"></a>主题

事件网格主题提供源在其中发送事件的终结点。 发布服务器创建事件网格主题，并决定事件源需要一个主题还是多个主题。 主题用于收集相关事件。 为了对某些类型的事件作出响应，订阅服务器将决定要订阅的主题。

设计应用程序时，您可以灵活地确定要创建的主题数量。 对于大型解决方案，请为每个类别的相关事件创建自定义主题。 例如，假设有一个应用程序发送与修改用户帐户和处理订单相关的事件。 任何事件处理程序都不太可能同时需要这两个类别的事件。 请创建两个自定义主题，让事件处理程序订阅其感兴趣的主题。 对于小型解决方案，可能希望将所有事件发送到单个主题。 事件订阅者可以筛选他们所需的事件类型。

请参阅[REST API 文档](api.md)，了解如何在事件网格中管理主题。

## <a name="event-subscriptions"></a>事件订阅

订阅告诉事件网格你有兴趣接收主题上的哪些事件。 在创建订阅时，你提供处理事件的终结点。 可以筛选发送到终结点的事件。 

请参阅[REST API 文档](api.md)，了解如何在事件网格中管理订阅。

## <a name="event-handlers"></a>事件处理程序

从事件网格的角度看，事件处理程序是发送事件的位置。 处理程序采取进一步的操作来处理事件。 事件网格支持多个处理程序类型。 你可以使用受支持的 Azure 服务或你自己的 web 挂钩作为处理程序。 根据处理程序的类型，事件网格遵循不同机制，以保证事件的传递。 如果目标事件处理程序是 HTTP web 挂钩，则在处理程序返回 `200 – OK`状态代码时将重试该事件。 对于 edge 中心，如果在未发生任何异常的情况下传递事件，则认为该事件是成功的。

## <a name="security"></a>“安全”

事件网格可为订阅主题和发布主题提供安全性。 有关详细信息，请参阅[事件网格安全性和身份验证](security-authentication.md)。

## <a name="event-delivery"></a>事件传送

如果事件网格无法确认订阅服务器的终结点已接收到事件，它将重新传递该事件。 有关详细信息，请参阅[事件网格消息传递和重试](delivery-retry.md)。

## <a name="batching"></a>批处理

使用自定义主题时，必须始终在数组中发布事件。 对于低吞吐量方案，数组将只有一个值。 对于大容量用例，我们建议你按 "发布" 将多个事件分批分批，以实现更高的效率。 批的大小最大可达 1 MB。 每个事件都应不超过 1 MB （预览）。