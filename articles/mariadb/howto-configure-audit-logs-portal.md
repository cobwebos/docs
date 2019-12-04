---
title: 访问审核日志-Azure 门户-Azure Database for MariaDB
description: 本文介绍如何在 Azure 门户中配置和访问 Azure Database for MariaDB 中的审核日志。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: e3c87143652895c57b464cba229a0e68049ffeb2
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74767511"
---
# <a name="configure-and-access-audit-logs-in-the-azure-portal"></a>在 Azure 门户中配置和访问审核日志

可以从 Azure 门户配置[Azure Database for MariaDB 审核日志](concepts-audit-logs.md)和诊断设置。

> [!IMPORTANT]
> 审核日志功能目前处于预览阶段。

## <a name="prerequisites"></a>必备组件

若要逐步执行本操作方法指南，需要：

- [Azure Database for MariaDB 服务器](quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="configure-audit-logging"></a>配置审核日志记录

启用和配置审核日志记录。

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 选择 Azure Database for MariaDB 服务器。

1. 在边栏中的 "**设置**" 部分下，选择 "**服务器参数**"。
    ![服务器参数](./media/howto-configure-audit-logs-portal/server-parameters.png)

1. 将**audit_log_enabled**参数更新为 ON。
    ![启用审核日志](./media/howto-configure-audit-logs-portal/audit-log-enabled.png)

1. 通过更新**audit_log_events**参数选择要记录的[事件类型](concepts-audit-logs.md#configure-audit-logging)。
    ![审核日志事件](./media/howto-configure-audit-logs-portal/audit-log-events.png)

1. 通过更新**audit_log_exclude_users**参数添加要从日志中排除的任何 MariaDB 用户。 通过提供用户的 MariaDB 用户名来指定用户。
    ![审核日志排除用户](./media/howto-configure-audit-logs-portal/audit-log-exclude-users.png)

1. 更改参数之后，可以单击“保存”。 也可以放弃所做的更改。
    ![保存](./media/howto-configure-audit-logs-portal/save-parameters.png)

## <a name="set-up-diagnostic-logs"></a>设置诊断日志

1. 在边栏中的 "**监视**" 部分下，选择 "**诊断设置**"。

1. 单击 "+ 添加诊断设置" ![添加诊断设置](./media/howto-configure-audit-logs-portal/add-diagnostic-setting.png)

1. 提供诊断设置名称。

1. 指定发送审核日志的数据接收器（存储帐户、事件中心和/或 Log Analytics 工作区）。

1. 选择 "MySqlAuditLogs" 作为日志类型。
![配置诊断设置](./media/howto-configure-audit-logs-portal/configure-diagnostic-setting.png)

1. 配置数据接收器以将审核日志传输到之后，可以单击 "**保存**"。
![保存诊断设置](./media/howto-configure-audit-logs-portal/save-diagnostic-setting.png)

1. 通过在配置的数据接收器中浏览审核日志来访问这些日志。 可能需要长达10分钟的时间才会显示日志。

## <a name="next-steps"></a>后续步骤

- 详细了解 Azure Database for MariaDB 中的[审核日志](concepts-audit-logs.md)。