---
title: include 文件
description: include 文件
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 05/18/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 220759e8ed31c091887bd55f8d12aa4cc03a065f
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83590275"
---
以下限制适用于 Azure 事件网格系统主题和自定义主题，不适用于事件域。

| 资源 | 限制 |
| --- | --- |
| 每个 Azure 订阅的自定义主题数 | 100 |
| 每个主题的事件订阅数 | 500 |
| 自定义主题的发布速率（入口） | 每个主题每秒 5,000 个事件 |
| 发布请求数 | 每秒 250 个 |
| 事件大小 | 1MB。 不过，操作以 64KB 为增量进行收费。 因此，超过 64KB 的事件会产生操作费用，就像它们是多个事件一样。 例如，大小为 130KB 的事件会产生操作费用，就像它是 3 个不同的事件一样。  |
| 每个事件域的主题数 | 100,000 |
| 域中每个主题的事件订阅数 | 500 |
| 域范围事件订阅数 | 50 |
| 事件域（入口）的发布速率 | 每秒 5,000 个事件 |
| 事件域的发布请求数 | 每秒 250 个 |
| 每个 Azure 订阅的事件域数 | 100 |
| 每个主题或域的专用终结点连接数 | 64 | 
| 每个主题或域的 IP 防火墙规则数 | 16 | 