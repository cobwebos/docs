---
title: 日志-超大规模（Citus）-Azure Database for PostgreSQL
description: 如何访问 Azure Database for PostgreSQL 的数据库日志-超大规模（Citus）
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 7/13/2020
ms.openlocfilehash: 4ca64a7793d229e7910fb122fb33996b1ac0c4fe
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87096507"
---
# <a name="logs-in-azure-database-for-postgresql---hyperscale-citus"></a>Azure Database for PostgreSQL-超大规模（Citus）中的日志

PostgreSQL 日志在超大规模（Citus）服务器组的每个节点上都可用。 可以将日志发送到存储服务器，也可以将日志发送到分析服务。 这些日志可用于识别、排除和修复配置错误和性能不佳问题。

## <a name="accessing-logs"></a>访问日志

若要访问超大规模（Citus）协调器或辅助节点的 PostgreSQL 日志，请在 Azure 门户中打开节点：

![节点列表](media/howto-hyperscale-logging/choose-node.png)

对于所选节点，打开 "**诊断设置**"，然后单击 " **+ 添加诊断设置**"。

![添加诊断设置按钮](media/howto-hyperscale-logging/diagnostic-settings.png)

为新诊断设置选择一个名称，并选中 " **PostgreSQLLogs** " 框。  选择应接收日志的目标。

![选择 PostgreSQL 日志](media/howto-hyperscale-logging/diagnostic-create-setting.png)

## <a name="next-steps"></a>后续步骤

- [Log analytics 查询入门](/azure/azure-monitor/log-query/get-started-portal)
- 了解[Azure 事件中心](/azure/event-hubs/event-hubs-about)
