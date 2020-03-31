---
title: Azure 事件网格高级层和基本层
description: 本文介绍 Azure 事件网格高级层和基本层之间的差异，以及这两个层的使用时机
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: overview
ms.date: 03/11/2020
ms.author: babanisa
ms.openlocfilehash: 7ae73337ccccc2b7d2402cfa2a54ae93552d2465
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/26/2020
ms.locfileid: "79300331"
---
# <a name="azure-event-grid-premium-and-basic-tiers"></a>Azure 事件网格高级层和基本层
Azure 事件网格有两个层：“高级”和“基本”。   基本层使用基于消耗量的定价或即用即付定价。 它提供所有必要的基本发布/订阅工具让你对事件驱动的编程模型使用事件网格。 高级层在此基础上更进一步，提供面向企业的安全功能。 高级层目前为早期**预览版**，其许多功能仍在开发中。

## <a name="overview"></a>概述
事件网格中的所有自定义主题和域均属于基本层或高级层。 从 API 版本 `2020-04-01-preview` 开始，可以在创建主题或域的过程中选择所需的层。 默认值为基本层。 使用旧 API 版本创建的主题和域默认属于基本层。 若要更改主题和域的定价层，请参阅[如何更新主题和域的层](update-tier.md)。

## <a name="capabilities-and-features"></a>功能和特性

下表描述了这两个层的差异：

|       &nbsp;                                           | 基本           | Premium        |
| ------------------------------------------------------ | --------------- | -------------- |
| 针对入口的 IP 防火墙规则                          | 预览  | 预览 |
| 针对出口的服务标记                                | 预览  | 预览 |
| 入口上的专用终结点 VNet 集成          | 不可用   | 预览 |

## <a name="availability"></a>可用性
在初始预览期，可在以下区域中使用提供专用终结点集成的高级层主题和域：

- 美国东部
- 美国西部 2
- 美国中南部

## <a name="pricing-and-quotas"></a>定价和配额
有关基本层的定价详细信息，请参阅[事件网格定价](https://azure.microsoft.com/pricing/details/event-grid/)。 高级层定价尚未公布，在给出定价之前可免费使用。

在公布高级层定价之前，针对主题和域计数以及吞吐量的现有配额同时适用于高级层和基本层资源。

## <a name="next-steps"></a>后续步骤
请参阅以下文章：

- 若要从基本层升级到高级层，请参阅[更新定价层](update-tier.md)一文。 
- 可将事件网格资源的 IP 防火墙配置为仅限从所选一组 IP 地址或 IP 地址范围通过公共 Internet 进行访问。 有关分步说明，请参阅[配置防火墙](configure-firewall.md)。
- 可将专用终结点配置为仅限从所选虚拟网络进行访问。 有关分步说明，请参阅[配置专用终结点](configure-private-endpoints.md)。