---
title: 配置高可用性 - 超大规模（Citus） - 用于后格雷SQL的 Azure 数据库
description: 如何启用或禁用高可用性
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: a8d4b5949b34d16191e9ec10a1dd39faff3660dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74977639"
---
# <a name="configure-hyperscale-citus-high-availability"></a>配置超大规模 （Citus） 高可用性

用于 PostgreSQL - 超大规模 （Citus） 的 Azure 数据库提供高可用性 （HA）以避免数据库停机。 启用 HA 后，服务器组中的每个节点都将获得备用。 如果原始节点不正常，将升级其备用节点以替换它。

> [!IMPORTANT]
> 由于 HA 使组中的服务器数翻倍，因此成本也会加倍。

在服务器组创建期间，或在 Azure 门户中的服务器组的 **"配置"** 选项卡中启用 HA 是可能的。 在这两种情况下，用户界面看起来都相似。 将**高可用性**的滑块拖动为"是"：

![哈滑块](./media/howto-hyperscale-high-availability/01-ha-slider.png)

单击"**保存**"按钮以应用您的选择。 启用 HA 可能需要一些时间，因为服务器组会提供备用数据库并将数据流式传输给他们。

服务器组的 **"概述"** 选项卡将列出所有节点及其备用节点，以及指示是否为每个节点成功启用 HA**的高可用性**列。

![服务器组概述中的 ha 列](./media/howto-hyperscale-high-availability/02-ha-column.png)

### <a name="next-steps"></a>后续步骤

了解有关[高可用性的更多信息](concepts-hyperscale-high-availability.md)。
