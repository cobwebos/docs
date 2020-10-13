---
title: 访问审核日志 - Azure 门户 - Azure Database for MariaDB
description: 本文介绍如何从 Azure 门户配置和访问 Azure Database for MariaDB 中的审核日志。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: how-to
ms.date: 6/24/2020
ms.openlocfilehash: 30ca62305c325943e17ee574873cfbbbaee97f77
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86113031"
---
# <a name="configure-and-access-audit-logs-in-the-azure-portal"></a>在 Azure 门户中配置和访问审核日志

可以从 Azure 门户配置 [Azure Database for MariaDB 审核日志](concepts-audit-logs.md)和诊断设置。

## <a name="prerequisites"></a>先决条件

若要逐步执行本操作方法指南，需要：

- [Azure Database for MariaDB 服务器](quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="configure-audit-logging"></a>配置审核日志记录

>[!IMPORTANT]
> 建议仅记录审核所需的事件类型和用户，以确保服务器的性能不会受到严重影响。

启用并配置审核日志记录。

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 选择 Azure Database for MariaDB 服务器。

1. 在侧栏的“设置”部分，选择“服务器参数”。 
    ![服务器参数](./media/howto-configure-audit-logs-portal/server-parameters.png)

1. 将 **audit_log_enabled** 参数更新为 ON。
    ![启用审核日志](./media/howto-configure-audit-logs-portal/audit-log-enabled.png)

1. 通过更新 **audit_log_events** 参数，选择要记录的[事件类型](concepts-audit-logs.md#configure-audit-logging)。
    ![审核日志事件](./media/howto-configure-audit-logs-portal/audit-log-events.png)

1. 通过更新 **audit_log_exclude_users** 参数添加要从日志记录中排除的 MariaDB 用户。 通过提供 MariaDB 用户名来指定用户。
    ![审核日志排除用户](./media/howto-configure-audit-logs-portal/audit-log-exclude-users.png)

1. 更改参数之后，可以单击“保存”。 也可以放弃所做的更改。
    ![保存](./media/howto-configure-audit-logs-portal/save-parameters.png)

## <a name="set-up-diagnostic-logs"></a>设置诊断日志

1. 在侧栏的“监视”部分，选择“诊断设置” 。

1. 单击“+ 添加诊断设置”![添加诊断设置](./media/howto-configure-audit-logs-portal/add-diagnostic-setting.png)

1. 提供诊断设置名称。

1. 指定向哪些数据接收器（存储帐户、事件中心和/或 Log Analytics 工作区）发送审核日志。

1. 选择“MySqlAuditLogs”作为日志类型。
![配置诊断设置](./media/howto-configure-audit-logs-portal/configure-diagnostic-setting.png)

1. 配置可以通过管道向其传输审核日志的数据接收器以后，即可单击“保存”。
![保存诊断设置](./media/howto-configure-audit-logs-portal/save-diagnostic-setting.png)

1. 访问审核日志时，可以在配置的数据接收器中浏览它们。 可能需要等待长达 10 分钟的时间这些日志才会出现。

## <a name="next-steps"></a>后续步骤

- 详细了解 Azure Database for MariaDB 中的[审核日志](concepts-audit-logs.md)
- 了解如何在 [Azure CLI](howto-configure-audit-logs-cli.md) 中配置审核日志