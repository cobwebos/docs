---
title: 配置和访问日志-灵活的服务器-Azure Database for PostgreSQL
description: 如何访问 Azure Database for PostgreSQL 灵活服务器的数据库日志
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 0b7b09696cbbe12a57d066e452b4c8ea7a7b8f27
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90934213"
---
# <a name="configure-and-access-logs-in-azure-database-for-postgresql---flexible-server"></a>在 Azure Database for PostgreSQL 灵活的服务器中配置和访问日志

> [!IMPORTANT]
> Azure Database for PostgreSQL 灵活服务器以预览版提供

PostgreSQL 日志在灵活服务器的每个节点上都可用。 可以将日志发送到存储服务器，也可以将日志发送到分析服务。 这些日志可用于识别、排除和修复配置错误和性能不佳问题。

## <a name="configure-diagnostic-settings"></a>配置诊断设置

可以使用 Azure 门户、CLI、REST API 和 PowerShell 为 Postgres 服务器启用诊断设置。 要选择的日志类别为“PostgreSQLLogs”。

若要使用 Azure 门户启用资源日志：

1. 在门户上 Postgres 服务器的导航菜单中，转到“诊断设置”。
   
2. 选择“添加诊断设置”。
   :::image type="content" source="media/howto-logging/diagnostic-settings.png" alt-text="添加诊断设置按钮":::

3. 为此设置命名。 

4. 选择首选的终结点（存储帐户、事件中心、Log Analytics）。 

5. 选择日志类型“PostgreSQLLogs”。
   :::image type="content" source="media/howto-logging/diagnostic-create-setting.png" alt-text="选择 PostgreSQL 日志":::

7. 保存设置。

若要使用 PowerShell、CLI 或 REST API 启用资源日志，请访问 [诊断设置](../../azure-monitor/platform/diagnostic-settings.md) 一文。

### <a name="access-resource-logs"></a>访问资源日志

访问日志的方式取决于所选的终结点。 有关 Azure 存储，请参阅[日志存储帐户](../../azure-monitor/platform/resource-logs-collect-storage.md)一文。 有关事件中心，请参阅[流式传输 Azure 日志](../../azure-monitor/platform/resource-logs-stream-event-hubs.md)一文。

Azure Monitor 日志将发送到所选的工作区。 Postgres 日志使用 **AzureDiagnostics** 收集模式，因此可以从 AzureDiagnostics 表查询它们。 下面描述了该表中的字段。 在 [Azure Monitor 日志查询](../../azure-monitor/log-query/log-query-overview.md)概述中详细了解查询和警报。

下面是可帮助你入门的查询。 可以基于查询配置警报。

搜索特定的服务器在过去一天生成的所有 Postgres 日志

```kusto
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where Category == "PostgreSQLLogs"
| where TimeGenerated > ago(1d) 
```

搜索所有非 localhost 连接尝试

```kusto
AzureDiagnostics
| where Message contains "connection received" and Message !contains "host=127.0.0.1"
| where Category == "PostgreSQLLogs" and TimeGenerated > ago(6h)
```

上面的查询将显示在此工作区中记录的任何 Postgres 服务器在过去 6 小时内的结果。

## <a name="next-steps"></a>后续步骤

- [Log analytics 查询入门](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)
- 了解 [Azure 事件中心](https://docs.microsoft.com/azure/event-hubs/event-hubs-about)
