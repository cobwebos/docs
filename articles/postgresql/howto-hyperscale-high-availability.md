---
title: 配置高可用性-超大规模（Citus）-Azure Database for PostgreSQL
description: 如何启用或禁用高可用性
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 78e6e8c18e67ce2dff0de94d298f06693a40135f
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82584053"
---
# <a name="configure-hyperscale-citus-high-availability"></a>配置超大规模（Citus）高可用性

Azure Database for PostgreSQL 超大规模（Citus）提供高可用性（HA），以避免数据库停机。 启用 HA 后，服务器组中的每个节点都将获得备用。 如果原始节点变得不正常，则会升级其备用节点来替换它。

> [!IMPORTANT]
> 由于 HA 增加了组中服务器的数目，因此它也会加倍。

在创建服务器组的过程中或在服务器组的 "**配置**" 选项卡中，在 Azure 门户中启用 HA。 在这两种情况下，用户界面的外观类似。 将 "**高可用性**" 滑块拖至 "是"：

![ha 滑块](./media/howto-hyperscale-high-availability/01-ha-slider.png)

单击 "**保存**" 按钮以应用您的选择。 启用 HA 可能需要一些时间，因为服务器组预配待机并流式传输数据。

服务器组的 "**概览**" 选项卡将列出所有节点及其待机，以及一个 "**高可用性**" 列，指示是否为每个节点成功启用了 HA。

![服务器组中的 ha 列概述](./media/howto-hyperscale-high-availability/02-ha-column.png)

### <a name="next-steps"></a>后续步骤

了解有关[高可用性](concepts-hyperscale-high-availability.md)的详细信息。
