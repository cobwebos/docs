---
title: 配置审核日志-Azure 门户 Azure Database for MySQL-灵活服务器
description: 本文介绍如何在 Azure 门户中配置和访问 Azure Database for MySQL 灵活的服务器中的审核日志。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: 1d0687e6cdcc8208218183cb873f9620a5525d37
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90934777"
---
# <a name="configure-and-access-audit-logs-for-azure-database-for-mysql---flexible-server-using-the-azure-portal"></a>使用 Azure 门户配置和访问 Azure Database for MySQL 灵活服务器的审核日志

> [!IMPORTANT]
> Azure Database for MySQL 灵活服务器当前以公共预览版提供。

可以从 Azure 门户配置 Azure Database for MySQL 灵活的服务器 [审核日志](concepts-audit-logs.md) 和诊断设置。

## <a name="prerequisites"></a>必备知识
本文中的步骤要求您具有灵活的 [服务器](quickstart-create-server-portal.md)。

## <a name="configure-audit-logging"></a>配置审核日志记录

>[!IMPORTANT]
> 建议仅记录审核所需的事件类型和用户，以确保服务器的性能不会受到严重影响。

启用并配置审核日志记录。

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 选择您的灵活服务器。

1. 在侧栏的“设置”部分，选择“服务器参数”。 
    <!--:::image type="content" source="./media/howto-configure-audit-logs-portal/server-parameters.png" alt-text="Server parameters":::-->

1. 将 **audit_log_enabled** 参数更新为 ON。
    <!-- :::image type="content" source="./media/howto-configure-audit-logs-portal/audit-log-enabled.png" alt-text="Enable audit logs":::-->

1. 通过更新 **audit_log_events** 参数，选择要记录的[事件类型](concepts-audit-logs.md#configure-audit-logging)。
    <!-- :::image type="content" source="./media/howto-configure-audit-logs-portal/audit-log-events.png" alt-text="Audit log events":::-->

1. 通过更新 **audit_log_exclude_users** 参数添加不进行日志记录的 MySQL 用户。 通过提供 MySQL 用户名来指定用户。
    <!--:::image type="content" source="./media/howto-configure-audit-logs-portal/audit-log-exclude-users.png" alt-text="Audit log exclude users":::-->

1. 更改参数之后，可以单击“保存”。 也可以放弃所做的更改。
    <!--:::image type="content" source="./media/howto-configure-audit-logs-portal/save-parameters.png" alt-text="Save":::-->

## <a name="set-up-diagnostics"></a>设置诊断

审核日志与 Azure Monitor 的诊断设置集成，可让你将日志通过管道传输到 Azure Monitor 日志、事件中心或 Azure 存储。

1. 在侧栏的“监视”部分，选择“诊断设置” 。

1. 单击 "+ 添加诊断设置"  <!-- :::image type="content" source="./media/howto-configure-audit-logs-portal/add-diagnostic-setting.png" alt-text="Add diagnostic setting":::-->

1. 提供诊断设置名称。

1. 指定要将审核日志 (存储帐户、事件中心和/或 Log Analytics 工作区) 发送到的目标。

1. 选择 **MySqlAuditLogs** 作为日志类型。
    <!-- :::image type="content" source="./media/howto-configure-audit-logs-portal/configure-diagnostic-setting.png" alt-text="Configure diagnostic setting"::: -->

1. 配置可以通过管道向其传输审核日志的数据接收器以后，即可单击“保存”。
    <!-- :::image type="content" source="./media/howto-configure-audit-logs-portal/save-diagnostic-setting.png" alt-text="Save diagnostic setting":::-->

1. 访问审核日志时，可以在配置的数据接收器中浏览它们。 可能需要等待长达 10 分钟的时间这些日志才会出现。

如果将审核日志通过管道传输到 Azure Monitor 日志 (Log Analytics) ，请参阅可用于分析的一些 [示例查询](concepts-audit-logs.md#analyze-logs-in-azure-monitor-logs) 。  

## <a name="next-steps"></a>后续步骤

- 详细了解[审核日志](concepts-audit-logs.md)
- 了解 [慢速查询日志](concepts-slow-query-logs.md)
<!-- - Learn how to configure audit logs in the [Azure CLI](howto-configure-audit-logs-cli.md)-->