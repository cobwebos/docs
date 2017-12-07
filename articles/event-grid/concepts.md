---
title: "Azure 事件网格概念"
description: "介绍 Azure 事件网格及其概念。 定义事件网格的几个关键组件。"
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 09/18/2017
ms.author: babanisa
ms.openlocfilehash: ccbd861c985e54a3808c0d4e8ea6169b6a61f134
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/28/2017
---
# <a name="concepts-in-azure-event-grid"></a>Azure 事件网格中的概念

Azure 事件网格中的主要概念有：

## <a name="events"></a>事件

事件是完全描述系统中所发生情况的最小信息量。  每个事件都具有通用信息，如事件源、事件发生的时间和唯一标识符。  此外，每个事件还具有仅与特定事件类型相关的特定信息。 例如，与在 Azure 存储中创建的有关新文件的事件将包含有关该文件的详细信息，如 `lastTimeModified` 值。 或者，与虚拟机重启相关的事件包含该虚拟机的名称和重启原因。 每个事件被限制为 64 KB 的数据。

## <a name="event-sourcespublishers"></a>事件源/发布服务器

事件源即事件发生的位置。 例如，Azure 存储是 blob 创建事件的事件源。 Azure VM 结构是虚拟机事件的事件源。 事件源负责将事件发布到事件网格。

## <a name="topics"></a>主题

发布服务器将事件归类为主题。 主题包括发布服务器发送事件的终结点。 为了对某些类型的事件作出响应，订阅服务器将决定要订阅的主题。 主题还提供事件架构，以便订阅服务器了解如何适当使用事件。

系统主题是 Azure 服务提供的内置主题。 自定义主题是应用程序和第三方主题。

## <a name="event-subscriptions"></a>事件订阅

订阅指示一个主题上订阅服务器有兴趣接收的事件的事件网格。  订阅还包含应如何将事件传递到订阅服务器的信息。

## <a name="event-handlers"></a>事件处理程序

从事件网格的角度看，事件处理程序是发送事件的位置。 处理程序将执行一些进一步的操作来处理事件。  事件网格支持多种订阅服务器类型。 根据订阅服务器的类型，事件网格遵循不同机制，以保证事件的传递。  对于 HTTP webhook 事件处理程序，在处理程序返回状态代码 `200 – OK` 之前，将重试事件。 对于 Azure 存储队列，在队列服务能够成功处理推送到队列的消息之前，将重试事件。

## <a name="filters"></a>筛选器

在订阅主题时，可以筛选发送到终结点的事件。 可以按事件类型或使用者模式进行筛选。 有关详细信息，请参阅[事件网格订阅架构](subscription-creation-schema.md)。

## <a name="security"></a>安全

事件网格可为订阅主题和发布主题提供安全性。 订阅时，必须对资源或主题具有足够的权限。 发布时，必须具有该主题的 SAS 令牌或密钥身份验证。 有关详细信息，请参阅[事件网格安全性和身份验证](security-authentication.md)。

## <a name="failed-delivery"></a>传递失败

当事件网格无法确认订阅服务器的终结点已接收到事件时，它将重新传递该事件。 有关详细信息，请参阅[事件网格消息传递和重试](delivery-and-retry.md)。

## <a name="next-steps"></a>后续步骤

* 有关事件网格的介绍，请参阅[关于事件网格](overview.md)。
* 若要快速开始使用事件网格，请参阅[使用 Azure 事件网格创建和路由自定义事件](custom-event-quickstart.md)。
