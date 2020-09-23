---
title: 管理日志-Azure 门户-Azure Database for PostgreSQL-单一服务器
description: 本文介绍了如何配置和访问服务器日志 ( 日志文件，) 在 Azure Database for PostgreSQL 中-单 Azure 门户服务器。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: cf22a165e9a6eadcd2e5d4cdb0efa301538be7e9
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90907536"
---
# <a name="configure-and-access-azure-database-for-postgresql---single-server-logs-from-the-azure-portal"></a>从 Azure 门户配置和访问 Azure Database for PostgreSQL（单一服务器）日志

可以从 Azure 门户配置、列出和下载 [Azure Database for PostgreSQL 日志](concepts-server-logs.md)。

## <a name="prerequisites"></a>必备知识
本文中的步骤要求具备 [Azure Database for PostgreSQL 服务器](quickstart-create-server-database-portal.md)。

## <a name="configure-logging"></a>配置日志记录
配置对查询日志和错误日志的访问权限。 

1. 登录 [Azure 门户](https://portal.azure.com/)。

2. 选择你的 Azure Database for PostgreSQL 服务器。

3. 在边栏中的“监视”部分下，选择“服务器日志”   。 

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png" alt-text="“服务器日志”选项的屏幕截图":::

4. 若要查看服务器参数，请选择“单击此处以启用日志并配置日志参数”  。

5. 更改需要调整的参数。 在此会话中所做的更改都突出显示为紫色。

   更改参数之后，请选择“保存”。**** 也可以放弃所做的更改。 

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/3-save-discard.png" alt-text="“服务器参数”选项的屏幕截图":::

在“服务器参数”  页上，可以通过关闭该页来返回到日志列表。

## <a name="view-list-and-download-logs"></a>查看列表并下载日志
日志记录开始后，可以查看可用日志列表，并下载单个日志文件。 

1. 打开 Azure 门户。

2. 选择你的 Azure Database for PostgreSQL 服务器。

3. 在边栏中的“监视”部分下，选择“服务器日志”   。 此页将显示日志文件列表。

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/4-server-logs-list.png" alt-text="“服务器日志”页的屏幕截图，其中突出显示了日志列表":::

   > [!TIP]
   > 日志的命名约定是 **postgresql-yyyy-mm-dd_hh0000.log**。 文件名中使用的日期和时间是发布日志的时间。 每小时或达到 100 MB 大小时日志文件会轮换一次，以先达到的条件为准。

4. 如果需要，可使用搜索框快速缩小范围，以根据日期和时间找到特定日志。 搜索按日志名称进行。

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/5-search.png" alt-text="“服务器日志”页的屏幕截图，其中突出显示了搜索框和结果":::

5. 若要下载单个日志文件，请选择表行中每个日志文件旁边的向下箭头图标。

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/6-download.png" alt-text="“服务器日志”页的屏幕截图，其中突出显示了向下箭头图标":::

## <a name="next-steps"></a>后续步骤
- 请参阅 [在 CLI 中访问服务器日志](howto-configure-server-logs-using-cli.md) 以了解如何以编程方式下载日志。
- 详细了解 Azure Database for PostgreSQL 中的[服务器日志](concepts-server-logs.md)。 
- 有关参数定义和 PostgreSQL 日志记录的详细信息，请参阅 [错误报告和日志记录](https://www.postgresql.org/docs/current/static/runtime-config-logging.html)上的 PostgreSQL 文档。

