---
title: 配置 Azure Database for PostgreSQL-超大规模（Citus）服务器组的高可用性
description: 如何启用或禁用高可用性
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 5ed29be1e890ddf2c4208ce9c03f01ce44f0e0d1
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515910"
---
# <a name="configure-hyperscale-citus-high-availability"></a>配置超大规模（Citus）高可用性

Azure Database for PostgreSQL 超大规模（Citus）提供高可用性（HA），以避免数据库停机。 启用 HA 后，服务器组中的每个节点都将获得备用。 如果原始节点变得不正常，则会升级其备用节点来替换它。

> [!IMPORTANT]
> 由于 HA 增加了组中服务器的数目，因此它也会加倍。

在创建服务器组的过程中或在服务器组的 "**配置**" 选项卡中，在 Azure 门户中启用 HA。 在这两种情况下，用户界面的外观类似。 将滑块的**高可用性**拖到 "开" 或 "关"：

![ha 滑块](./media/howto-hyperscale-high-availability/01-ha-slider.png)

单击 "**保存**" 按钮以应用您的选择。 启用 HA 可能需要一些时间，因为服务器组预配待机并流式传输数据。

服务器组的 "**概览**" 选项卡将列出所有节点及其待机，以及一个 "**高可用性**" 列，指示是否为每个节点成功启用了 HA。

### <a name="next-steps"></a>后续步骤

了解有关[高可用性](concepts-hyperscale-high-availability.md)的详细信息。
