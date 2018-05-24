---
title: Azure 事件网格概念
description: 介绍 Azure 事件网格及其概念。 定义事件网格的几个关键组件。
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/24/2018
ms.author: babanisa
ms.openlocfilehash: fd82d163ba8407a3dfa088cd322f3e236be5d7ea
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/18/2018
---
# <a name="concepts-in-azure-event-grid"></a>Azure 事件网格中的概念

本文介绍了 Azure 事件网格中的主要概念。

## <a name="events"></a>活动

事件是完全描述系统中所发生情况的最小信息量。 每个事件都具有通用信息，如事件源、事件发生的时间和唯一标识符。 此外，每个事件还具有仅与特定事件类型相关的特定信息。 例如，与在 Azure 存储中创建的有关新文件的事件将包含有关该文件的详细信息，如 `lastTimeModified` 值。 再如，事件中心事件具有 Capture 文件的 URL。 每个事件被限制为 64 KB 的数据。

## <a name="event-sourcespublishers"></a>事件源/发布服务器

事件源即事件发生的位置。 例如，Azure 存储是 blob 创建事件的事件源。 Azure VM 结构是虚拟机事件的事件源。 事件源负责将事件发布到事件网格。

有关实现任何受支持的事件网格源的信息，请参阅 [Azure 事件网格中的事件源](event-sources.md)。

## <a name="topics"></a>主题

发布服务器将事件归类为主题。 事件网格主题包括发布服务器在其中发送事件的终结点。 为了对某些类型的事件作出响应，订阅服务器将决定要订阅的主题。 主题还提供事件架构，以便订阅服务器了解如何适当地使用事件。

系统主题是 Azure 服务提供的内置主题。 自定义主题是应用程序和第三方主题。

设计应用程序时，可以灵活地决定要创建多少个主题。 对于大型解决方案，请为每个类别的相关事件创建自定义主题。 例如，假设有一个应用程序发送与修改用户帐户和处理订单相关的事件。 任何事件处理程序都不太可能同时需要这两个类别的事件。 请创建两个自定义主题，让事件处理程序订阅其感兴趣的主题。 对于小型解决方案，可能希望将所有事件发送到单个主题。 事件订阅者可以筛选他们所需的事件类型。

## <a name="event-subscriptions"></a>事件订阅

订阅指示一个主题上订阅服务器有兴趣接收的事件的事件网格。 订阅还包含应如何将事件传递到订阅服务器的信息。

## <a name="event-handlers"></a>事件处理程序

从事件网格的角度看，事件处理程序是发送事件的位置。 处理程序将执行一些进一步的操作来处理事件。 事件网格支持多种订阅服务器类型。 根据订阅服务器的类型，事件网格遵循不同机制，以保证事件的传递。 对于 HTTP webhook 事件处理程序，在处理程序返回状态代码 `200 – OK` 之前，将重试事件。 对于 Azure 存储队列，在队列服务能够成功处理推送到队列的消息之前，将重试事件。

有关实现任何受支持的事件网格处理程序的信息，请参阅 [Azure 事件网格中的事件处理程序](event-handlers.md)。

## <a name="filters"></a>筛选器

订阅事件网格主题时，可以筛选发送到终结点的事件。 可以按事件类型或使用者模式进行筛选。 有关详细信息，请参阅[事件网格订阅架构](subscription-creation-schema.md)。

## <a name="security"></a>安全

事件网格可为订阅主题和发布主题提供安全性。 订阅时，必须对资源或事件网格主题具有足够的权限。 发布时，必须具有该主题的 SAS 令牌或密钥身份验证。 有关详细信息，请参阅[事件网格安全性和身份验证](security-authentication.md)。

## <a name="failed-delivery"></a>传递失败

如果事件网格无法确认订阅服务器的终结点已接收到事件，它将重新传递该事件。 有关详细信息，请参阅[事件网格消息传递和重试](delivery-and-retry.md)。

## <a name="next-steps"></a>后续步骤

* 有关事件网格的介绍，请参阅[关于事件网格](overview.md)。
* 若要快速开始使用事件网格，请参阅[使用 Azure 事件网格创建和路由自定义事件](custom-event-quickstart.md)。
