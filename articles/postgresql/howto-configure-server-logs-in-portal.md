---
title: 在 Azure 门户中配置和访问 PostgreSQL 的服务器日志
description: 本文介绍了如何从 Azure 门户配置和访问 Azure Database for PostgreSQL 中的服务器日志。
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: aa9823c65b342f922ca78a51ecd3055dfac62869
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2018
ms.locfileid: "29692158"
---
# <a name="configure-and-access-server-logs-in-the-azure-portal"></a>在 Azure 门户中配置和访问服务器日志

可以从 Azure 门户配置、列出和下载 [Azure Database for PostgreSQL 服务器日志](concepts-server-logs.md)。

## <a name="prerequisites"></a>先决条件
若要逐步执行本操作方法指南，需要：
- [Azure Database for PostgreSQL 服务器](quickstart-create-server-database-portal.md)

## <a name="configure-logging"></a>配置日志记录
配置对查询日志和错误日志的访问权限。 

1. 登录到 [Azure 门户](http://portal.azure.com/)。

2. 选择你的 Azure Database for PostgreSQL 服务器。

3. 在侧栏中的“监视”部分下，选择“服务器日志”。 

   ![选择服务器日志，然后选择“单击此处以启用...”](./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png)

4. 若要查看服务器参数，请选择标题“单击此处以启用日志并配置日志参数”。

5. 更改需要调整的参数。 在此会话中所做的更改都突出显示为紫色。

   更改参数之后，可以单击“保存”。 也可以放弃所做的更改。 

   ![参数的长列表，其中包含了要保存或丢弃的更改](./media/howto-configure-server-logs-in-portal/3-save-discard.png)

6. 单击“服务器参数”页上的“关闭”按钮（X 图标）返回到日志列表。

## <a name="view-list-and-download-logs"></a>查看列表并下载日志
日志记录开始后，可以在“服务器日志”面板上查看可用日志列表并下载各个日志文件。 

1. 打开 Azure 门户。

2. 选择你的 Azure Database for PostgreSQL 服务器。

3. 在侧栏中的“监视”部分下，选择“服务器日志”。 此页面将显示日志文件列表，如图所示：

   ![服务器日志列表](./media/howto-configure-server-logs-in-portal/4-server-logs-list.png)

   > [!TIP]
   > 日志的命名约定是 **postgresql-yyyy-mm-dd_hh0000.log**。 文件名称中的日期和时间是发布日志的时间。 每 1 小时或达到 100 MB 大小时日志文件会轮换一次，以先达到的限制为准。

4. 如果需要，可使用“搜索框”快速缩小范围，以根据日期/时间找到特定日志。 搜索按日志名称进行。

   ![对日志名称的示例搜索](./media/howto-configure-server-logs-in-portal/5-search.png)

5. 使用表行中日志文件旁边的“下载”按钮（向下箭头图标）下载各个日志文件，如图所示：

   ![单击“下载”图标](./media/howto-configure-server-logs-in-portal/6-download.png)

## <a name="next-steps"></a>后续步骤
- 若要了解如何以编程方式下载日志，请参阅[使用 CLI 访问服务器日志](howto-configure-server-logs-using-cli.md)。
- 详细了解 Azure DB for PostgreSQL 中的[服务器日志](concepts-server-logs.md)。 
- 有关参数定义和 PostgreSQL 日志记录的详细信息，请参阅[错误报告和日志记录](https://www.postgresql.org/docs/current/static/runtime-config-logging.html)上的 PostgreSQL 文档。

