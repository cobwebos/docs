---
title: 从 Azure 门户配置和访问 Azure Database for MariaDB 中的服务器日志
description: 本文介绍如何从 Azure 门户配置和访问 Azure Database for MariaDB 中的服务器日志。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/30/2019
ms.openlocfilehash: c7e95521e503ad2d92bf13ae7e410b71ed6dfcd3
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/03/2019
ms.locfileid: "71844701"
---
# <a name="configure-and-access-server-logs-from-the-azure-portal"></a>从 Azure 门户配置和访问服务器日志

可以从 Azure 门户配置、列出并下载 [Azure Database for MariaDB 慢查询日志](concepts-server-logs.md)。

## <a name="prerequisites"></a>先决条件
本文中的步骤要求具备 [Azure Database for MariaDB 服务器](quickstart-create-mariadb-server-database-using-azure-portal.md)。

## <a name="configure-logging"></a>配置日志记录
配置慢查询日志的访问权限。 

1. 登录到 [Azure 门户](https://portal.azure.com/)。

2. 选择 Azure Database for MariaDB 服务器。

3. 在边栏中的“监视”部分下，选择“服务器日志”。 
   ![“服务器日志”选项的屏幕截图](./media/howto-configure-server-logs-portal/1-select-server-logs-configure.png)

4. 若要查看服务器参数，请选择“单击此处以启用日志并配置日志参数”。

5. 更改需要调整的参数，包括将“slow_query_log”转换为“ON”。 在此会话中所做的更改都突出显示为紫色。 

   更改参数之后，请选择“保存”。 也可以放弃所做的更改。

   ![“服务器参数”选项的屏幕截图](./media/howto-configure-server-logs-portal/3-save-discard.png)

在“服务器参数”页上，可以通过关闭该页来返回到日志列表。

## <a name="view-list-and-download-logs"></a>查看列表并下载日志
日志记录开始后，可以查看可用慢查询日志列表，并下载单个日志文件。 

1. 打开 Azure 门户。

2. 选择 Azure Database for MariaDB 服务器。

3. 在边栏中的“监视”部分下，选择“服务器日志”。 此页将显示日志文件列表。

   ![“服务器日志”页的屏幕截图，其中突出显示了日志列表](./media/howto-configure-server-logs-portal/4-server-logs-list.png)

   > [!TIP]
   > 日志命名约定是 mysql-slow-< your server name>-yyyymmddhh.log。 文件名中使用的日期和时间是发布日志的时间。 日志文件每 24 小时或每 7.5 GB 轮换一次（以先达到的条件为准）。

4. 如果需要，可使用搜索框快速缩小范围，以根据日期和时间找到特定日志。 按日志名称进行搜索。

5. 若要下载单个日志文件，请选择表行中每个日志文件旁边的向下箭头图标。

   ![“服务器日志”页的屏幕截图，其中突出显示了向下箭头图标](./media/howto-configure-server-logs-portal/5-download.png)

## <a name="set-up-diagnostic-logs"></a>设置诊断日志

1. 在边栏中的“监视”部分下，选择“诊断设置” > “添加诊断设置”。

   ![“诊断设置”选项的屏幕截图](./media/howto-configure-server-logs-portal/add-diagnostic-setting.png)

1. 提供诊断设置名称。

1. 指定向哪些数据接收器（存储帐户、事件中心或 Log Analytics 工作区）发送慢查询日志。

1. 选择 **MySqlSlowLogs** 作为日志类型。
![“诊断设置配置”选项的屏幕截图](./media/howto-configure-server-logs-portal/configure-diagnostic-setting.png)

1. 配置可以通过管道向其传送慢查询日志的数据接收器后，选择“保存”。
![“诊断设置配置”选项的屏幕截图，其中突出显示了“保存”](./media/howto-configure-server-logs-portal/save-diagnostic-setting.png)

1. 可以通过在配置的数据接收器中浏览慢查询日志来对其进行访问。 最多需要等待 10 分钟的时间，这些日志就会出现。

## <a name="next-steps"></a>后续步骤
- 若要了解如何以编程方式下载慢查询日志，请参阅[在 CLI 中访问慢查询日志](howto-configure-server-logs-cli.md)。
- 详细了解 Azure Database for MariaDB 中的[慢查询日志](concepts-server-logs.md)。
- 有关参数定义和日志记录的详细信息，请参阅[日志](https://mariadb.com/kb/en/library/slow-query-log-overview/)上的 MariaDB 文档。