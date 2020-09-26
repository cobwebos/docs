---
title: 配置慢查询日志-Azure 门户 Azure Database for MySQL-灵活服务器
description: 本文介绍如何在 Azure 门户中配置和访问 Azure Database for MySQL 灵活服务器中的慢速查询日志。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: 1a829f4e5f45394d9c5bbed0db9289727b816917
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91315024"
---
# <a name="configure-and-access-slow-query-logs-for-azure-database-for-mysql---flexible-server-using-the-azure-portal"></a>使用 Azure 门户配置和访问 Azure Database for MySQL 灵活服务器的慢速查询日志

> [!IMPORTANT]
> Azure Database for MySQL 灵活服务器当前以公共预览版提供。

可以从 Azure 门户配置、列出和下载 Azure Database for MySQL 灵活的服务器 [缓慢查询日志](concepts-slow-query-logs.md) 。

## <a name="prerequisites"></a>先决条件
本文中的步骤要求您具有灵活的 [服务器](quickstart-create-server-portal.md)。

## <a name="configure-logging"></a>配置日志记录
配置 MySQL 慢查询日志的访问权限。 

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 选择您的灵活服务器。

1. 在侧栏的“设置”部分，选择“服务器参数”。 
   <!-- :::image type="content" source="./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png" alt-text="Screenshot of Server logs options":::-->

1. 将 **slow_query_log** 参数更新为 **ON**。

1. 更改所需的任何其他参数 (例如。 `long_query_time`, `log_slow_admin_statements`). 有关更多参数，请参阅 [慢速查询日志](./concepts-slow-query-logs.md#configure-slow-query-logging) 文档。  

1. 选择“保存”。 

   <!-- :::image type="content" source="./media/howto-configure-server-logs-in-portal/3-save-discard.png" alt-text="Screenshot of slow query log parameters and save."::: -->

在“服务器参数”  页上，可以通过关闭该页来返回到日志列表。

## <a name="set-up-diagnostics"></a>设置诊断

> [!NOTE]
> 在部署过程中，与访问日志 Azure Monitor 诊断设置的集成正在部署，并且功能会很快可用。

慢速查询日志与 Azure Monitor 诊断设置集成，使你可以通过管道将日志传递到 Azure Monitor 日志、事件中心或 Azure 存储。

1. 在边栏中的“监视”  部分下，选择“诊断设置”   > “添加诊断设置”  。

   <!--:::image type="content" source="./media/howto-configure-server-logs-in-portal/add-diagnostic-setting.png" alt-text="Screenshot of Diagnostic settings options":::-->

1. 提供诊断设置名称。

1. 指定 (存储帐户、事件中心或 Log Analytics 工作区) 发送慢查询日志的目标。

1. 选择 **MySqlSlowLogs** 作为日志类型。
    <!--:::image type="content" source="./media/howto-configure-server-logs-in-portal/configure-diagnostic-setting.png" alt-text="Screenshot of Diagnostic settings configuration options":::-->

1. 配置可以通过管道向其传送慢查询日志的数据接收器后，选择“保存”。 
    <!--:::image type="content" source="./media/howto-configure-server-logs-in-portal/save-diagnostic-setting.png" alt-text="Screenshot of Diagnostic settings configuration options, with Save highlighted":::-->

1. 可以通过在配置的数据接收器中浏览慢查询日志来对其进行访问。 最多需要等待 10 分钟的时间，这些日志就会出现。

如果将日志通过管道传输到 Azure Monitor 日志 (Log Analytics) ，请参阅可用于分析的一些 [示例查询](concepts-audit-logs.md#analyze-logs-in-azure-monitor-logs) 。 

## <a name="next-steps"></a>后续步骤
<!-- - See [Access slow query Logs in CLI](howto-configure-server-logs-in-cli.md) to learn how to download slow query logs programmatically.-->
- 了解有关[慢速查询日志](concepts-slow-query-logs.md)的详细信息
- 有关参数定义和 MySQL 日志记录的详细信息，请参阅[日志](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html)上的 MySQL 文档。
- 了解 [审核日志](concepts-audit-logs.md)
