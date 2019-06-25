---
title: 配置和访问审核日志的 Azure Database for MariaDB 在 Azure 门户中
description: 本文介绍如何配置和访问审核日志在 Azure Database for MariaDB 从 Azure 门户。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: c9ee106972cc78a08709d2ce55d2dfddc96edbf7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079231"
---
# <a name="configure-and-access-audit-logs-in-the-azure-portal"></a>配置和访问 Azure 门户中的审核日志

你可以配置[Azure Database for MariaDB 审核日志](concepts-audit-logs.md)和从 Azure 门户的诊断设置。

> [!IMPORTANT]
> 审核日志功能当前处于预览状态。

## <a name="prerequisites"></a>必备组件

若要逐步执行本操作方法指南，需要：

- [Azure Database for MariaDB 服务器](quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="configure-audit-logging"></a>配置审核日志记录

启用并配置审核日志记录。

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 选择 Azure Database for MariaDB 服务器。

1. 下**设置**侧栏，选择中的部分**服务器参数**。
    ![服务器参数](./media/howto-configure-audit-logs-portal/server-parameters.png)

1. 更新**audit_log_enabled**参数为 ON。
    ![启用审核日志](./media/howto-configure-audit-logs-portal/audit-log-enabled.png)

1. 选择要记录的更新的事件**audit_log_events**参数。
    ![审核日志事件](./media/howto-configure-audit-logs-portal/audit-log-events.png)

1. 添加要从日志记录通过更新中排除任何 MariaDB 用户**audit_log_exclude_users**参数。 指定用户通过提供其 MariaDB 用户名称。
    ![审核日志中排除用户](./media/howto-configure-audit-logs-portal/audit-log-exclude-users.png)

1. 更改参数之后，可以单击“保存”  。 也可以放弃所做的更改  。
    ![保存](./media/howto-configure-audit-logs-portal/save-parameters.png)

## <a name="set-up-diagnostic-logs"></a>设置诊断日志

1. 下**监视**侧栏，选择中的部分**诊断设置**。

1. 单击"+ 添加诊断设置"![添加诊断设置](./media/howto-configure-audit-logs-portal/add-diagnostic-setting.png)

1. 提供诊断设置的名称。

1. 指定哪些数据接收器发送审核日志 （存储帐户、 事件中心和/或 Log Analytics 工作区）。

1. 选择"MySqlAuditLogs"作为日志类型。
![配置诊断设置](./media/howto-configure-audit-logs-portal/configure-diagnostic-setting.png)

1. 后配置数据接收器可以通过管道传递到审核日志后，你可以单击**保存**。
![保存诊断设置](./media/howto-configure-audit-logs-portal/save-diagnostic-setting.png)

1. 通过浏览在你配置的数据接收器中访问审核日志。 可能需要最多 10 分钟才能显示日志。

## <a name="next-steps"></a>后续步骤

- 详细了解如何[审核日志](concepts-audit-logs.md)Azure Database for MariaDB 中。