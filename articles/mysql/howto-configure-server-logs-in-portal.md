---
title: 访问慢查询日志 - Azure 门户 - Azure Database for MySQL
description: 本文介绍如何从 Azure 门户配置和访问 Azure Database for MySQL 中的慢查询日志。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 4/13/2020
ms.openlocfilehash: 51b05ea016880d04fd6a2123962afefbdb229be1
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2020
ms.locfileid: "86101777"
---
# <a name="configure-and-access-slow-query-logs-from-the-azure-portal"></a>从 Azure 门户配置和访问慢查询日志

可以从 Azure 门户配置、列出并下载 [Azure Database for MySQL 慢查询日志](concepts-server-logs.md)。

## <a name="prerequisites"></a>先决条件
本文中的步骤要求具备 [Azure Database for MySQL 服务器](quickstart-create-mysql-server-database-using-azure-portal.md)。

## <a name="configure-logging"></a>配置日志记录
配置 MySQL 慢查询日志的访问权限。 

1. 登录到 [Azure 门户](https://portal.azure.com/)。

2. 选择 Azure Database for MySQL 服务器。

3. 在边栏中的“监视”部分下，选择“服务器日志”   。 
   ![“服务器日志”选项的屏幕截图](./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png)

4. 若要查看服务器参数，请选择“单击此处以启用日志并配置日志参数”  。

5. 打开“slow_query_log”  的开关  。

6. 使用 log_output  选择要将日志输出到的位置。 若要将日志同时发送到本地存储和 Azure Monitor 诊断日志，请选择“File”  。 

7. 更改所需的任何其他参数。 

8. 选择“保存”  。 

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/3-save-discard.png" alt-text="显示慢查询日志参数和“保存”的屏幕截图。":::

在“服务器参数”  页上，可以通过关闭该页来返回到日志列表。

## <a name="view-list-and-download-logs"></a>查看列表并下载日志
日志记录开始后，可以查看可用慢查询日志列表，并下载单个日志文件。

1. 打开 Azure 门户。

2. 选择 Azure Database for MySQL 服务器。

3. 在边栏中的“监视”部分下，选择“服务器日志”   。 此页将显示日志文件列表。

   ![“服务器日志”页的屏幕截图，其中突出显示了日志列表](./media/howto-configure-server-logs-in-portal/4-server-logs-list.png)

   > [!TIP]
   > 日志命名约定是 mysql-slow-< your server name>-yyyymmddhh.log  。 文件名中使用的日期和时间是发布日志的时间。 日志文件每 24 小时或每 7.5 GB 轮换一次（以先达到的条件为准）。 

4. 如果需要，可使用搜索框快速缩小范围，以根据日期和时间找到特定日志。 搜索按日志名称进行。

5. 若要下载单个日志文件，请选择表行中每个日志文件旁边的向下箭头图标。

   ![“服务器日志”页的屏幕截图，其中突出显示了向下箭头图标](./media/howto-configure-server-logs-in-portal/5-download.png)

## <a name="set-up-diagnostic-logs"></a>设置诊断日志

1. 在边栏中的“监视”  部分下，选择“诊断设置”   > “添加诊断设置”  。

   ![“诊断设置”选项的屏幕截图](./media/howto-configure-server-logs-in-portal/add-diagnostic-setting.png)

1. 提供诊断设置名称。

1. 指定向哪些数据接收器（存储帐户、事件中心或 Log Analytics 工作区）发送慢查询日志。

1. 选择 **MySqlSlowLogs** 作为日志类型。
![“诊断设置配置”选项的屏幕截图](./media/howto-configure-server-logs-in-portal/configure-diagnostic-setting.png)

1. 配置可以通过管道向其传送慢查询日志的数据接收器后，选择“保存”。 
![“诊断设置配置”选项的屏幕截图，其中突出显示了“保存”](./media/howto-configure-server-logs-in-portal/save-diagnostic-setting.png)

1. 可以通过在配置的数据接收器中浏览慢查询日志来对其进行访问。 最多需要等待 10 分钟的时间，这些日志就会出现。

## <a name="next-steps"></a>后续步骤
- 若要了解如何以编程方式下载慢查询日志，请参阅[在 CLI 中访问慢查询日志](howto-configure-server-logs-in-cli.md)。
- 详细了解 Azure Database for MySQL 中的[慢查询日志](concepts-server-logs.md)。
- 有关参数定义和 MySQL 日志记录的详细信息，请参阅[日志](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html)上的 MySQL 文档。