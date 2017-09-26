---
title: "将数据载入 Azure SQL 数据仓库 – 数据工厂 | Microsoft 文档"
description: "本教程使用 Azure 数据工厂将数据载入 Azure SQL 数据仓库，并使用 SQL Server 数据库作为数据源。"
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: jhubbard
editor: 
tags: azure-sql-data-warehouse;azure-data-factory
ms.service: sql-data-warehouse
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: loading
ms.date: 02/08/2017
ms.author: cakarst;barbkess
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 6399f1a3390119685c1c9fd7332937e0cdb6f9ea
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---

# <a name="load-data-into-sql-data-warehouse-with-data-factory"></a>使用数据工厂将数据载入 SQL 数据仓库

可以使用 Azure 数据工厂将数据从任何[支持的源数据存储](../data-factory/copy-activity-overview.md)载入 Azure SQL 数据仓库。 例如，可以使用数据工厂将数据从 Azure SQL 数据库或 Oracle 数据库载入 SQL 数据仓库。 本文中的教程介绍如何将数据从本地 SQL Server 数据库载入 SQL 数据仓库。

**估计所需时间**：如果满足先决条件，完成本教程大约需要 10-15 分钟。

## <a name="prerequisites"></a>先决条件

- 需要一个 **SQL Server 数据库**，内附表包含要复制到 SQL 数据仓库的数据。  

- 需要一个联机 **SQL 数据仓库**。 如果没有数据仓库，请学习如何[创建 Azure SQL 数据仓库](sql-data-warehouse-get-started-provision.md)。

- 需要一个 **Azure 存储帐户**。 如果还没有存储帐户，请了解[如何创建存储帐户](../storage/common/storage-create-storage-account.md)。 为获得最佳性能，请将存储帐户和数据仓库放在同一个 Azure 区域。

## <a name="configure-a-data-factory"></a>配置数据工厂
1. 登录到 [Azure 门户][]。
2. 找到数据仓库，然后单击将它打开。
3. 在主边栏选项卡中，单击“加载数据” > “Azure 数据工厂”。

    ![启动加载数据向导](media/sql-data-warehouse-load-with-data-factory/launch-load-data-wizard.png)

4. 如果 Azure 订阅中没有数据工厂，浏览器中的一个单独的标签页上会显示“新建数据工厂”对话框。 请填写所需的信息，并单击“创建”。 创建数据工厂后，“新建数据工厂”对话框会关闭，同时出现“选择数据工厂”对话框。

    如果 Azure 订阅中已有一个或多个数据工厂，会显示“选择数据工厂”对话框。 在此对话框中，可以选择一个现有的数据工厂，或单击“创建新数据工厂”来新建一个数据工厂。

    ![配置数据工厂](media/sql-data-warehouse-load-with-data-factory/configure-data-factory.png)

5. 在“选择数据工厂”对话框中，默认已选择“加载数据”选项。 单击“下一步”开始创建数据加载任务。

## <a name="configure-the-data-factory-properties"></a>配置数据工厂属性
创建数据工厂后，下一个步骤是配置数据加载计划。

1. 对于“任务名称”，请输入 **DWLoadData-fromSQLServer**。
2. 使用默认的“立即运行一次”选项，单击“下一步”。

    ![配置加载计划](media/sql-data-warehouse-load-with-data-factory/configure-load-schedule.png)

## <a name="configure-the-source-data-store-and-gateway"></a>配置源数据存储和网关
现在，请告知数据工厂有关要从中加载数据的本地 SQL Server 数据库的信息。

1. 从支持的源数据存储目录中选择“SQL Server”，并单击“下一步”。

    ![选择 SQL Server 源](media/sql-data-warehouse-load-with-data-factory/choose-sql-server-source.png)

2. 此时会显示“指定本地 SQL Server 数据库”对话框。 将自动填充第一个“连接名称”字段。 第二个字段要求提供**网关**的名称。 如果使用已有网关的现有数据工厂，可以重复使用该网关，从下拉列表中选择它即可。 单击“创建网关”链接创建数据管理网关。  

    > [!NOTE]
    > 如果源数据存储位于本地或者 Azure IaaS 虚拟机中，则需要数据管理网关。 网关与数据工厂之间存在一一对应的关系。 不能从另一个数据工厂使用不对应的网关，但是，一个网关可由同一数据工厂中的多个数据加载任务使用。 运行数据加载任务时，可以使用网关连接到多个数据存储。
    >
    > 有关网关的详细信息，请参阅[数据管理网关](../data-factory/v1/data-factory-data-management-gateway.md)一文。

3. 此时会显示“创建网关”对话框。 在“名称”中输入 **GatewayForDWLoading**，并单击“创建”。

4. 此时会显示“配置网关”对话框。 单击“在此计算机上启动快速安装”，在当前计算机上自动下载、安装并注册数据管理网关。 弹出窗口中会显示进度。 如果该计算机无法连接到数据存储，则可在能够连接到数据存储的计算机上手动[下载并安装该网关](https://www.microsoft.com/download/details.aspx?id=39717)，并使用密钥注册。
    > [!NOTE]
    > 快速安装会在 Microsoft Edge 和 Internet Explorer 中本地运行。 如果使用 Google Chrome，请先通过 Chrome Web Store 安装 ClickOnce 扩展。

    ![启动快速安装程序](media/sql-data-warehouse-load-with-data-factory/launch-express-setup.png)

5. 等待网关安装程序完成。 网关成功注册并联机后，弹出窗口会关闭，新网关将出现在网关字段中。 然后填写其余必需字段，如下所示，并单击“下一步”。
    - **服务器名称**：本地 SQL Server 的名称。
    - **数据库名称**：SQL Server 数据库。
    - **凭据加密**：使用默认的“按 Web 浏览器”。
    - **身份验证类型**：选择正在使用的身份验证类型。
    - **用户名称**和**密码**：输入有权复制数据的用户的用户名和密码。

    ![启动快速安装程序](media/sql-data-warehouse-load-with-data-factory/configure-sql-server.png)

6. 下一个步骤是选择要从其中复制数据的表。 可以使用关键字来筛选表。 可以在底部面板中预览数据和表架构。 完成选择后，单击“下一步”。

    ![选择表](media/sql-data-warehouse-load-with-data-factory/select-tables.png)

## <a name="configure-the-destination-your-sql-data-warehouse"></a>配置目标，即 SQL 数据仓库

现在可以告知数据工厂目标信息。

1. 系统会自动填充 SQL 数据仓库连接信息。 输入用户名的密码。 单击“下一步”。

    ![配置目标](media/sql-data-warehouse-load-with-data-factory/configure-destination.png)

2. 此时会显示智能表映射，它可以根据表名将源映射到目标表。 如果目标位置中没有表格，默认情况下，ADF 将创建一个名称相同的表格（这适用于作为源的 SQL Server 或 Azure SQL 数据库）。 还可选择映射到现有表。 检查信息，并单击“下一步”。

    ![映射表](media/sql-data-warehouse-load-with-data-factory/table-mapping.png)

3. 检查架构映射并查看错误或警告消息。 智能映射基于列名。 如果在源列与目标列之间进行不受支持的数据类型转换，会显示错误消息以及相应的表。 如果选择让数据工厂自动创建表格，则当需要修复源和目标存储中的不兼容时，可能会出现适当的数据类型转换。

    ![映射架构](media/sql-data-warehouse-load-with-data-factory/schema-mapping.png)

4. 单击“下一步”。

## <a name="configure-the-performance-settings"></a>配置性能设置
在“性能配置”中，可以配置一个 Azure 存储帐户，在使用 [PolyBase](sql-data-warehouse-best-practices.md#use-polybase-to-load-and-export-data-quickly) 以执行方式将数据载入 SQL 数据仓库之前，可以使用该帐户来暂存数据。 复制完成后，会自动清除存储中的临时数据。

选择现有的 Azure 存储帐户，并单击“下一步”。

![配置暂存 Blob](media/sql-data-warehouse-load-with-data-factory/configure-staging-blob.png)

## <a name="review-summary-information-and-deploy-the-pipeline"></a>检查摘要信息，并部署管道

查看配置，并单击“完成”按钮以部署管道。

![部署数据工厂](media/sql-data-warehouse-load-with-data-factory/deploy-data-factory.png)

## <a name="monitor-data-loading-progress"></a>监视数据加载进度

可以在“部署”页中查看部署进度和结果。

1. 部署完成后，单击带有“单击此处监视复制管道”字样的链接监视数据加载进度。

    ![监视管道](media/sql-data-warehouse-load-with-data-factory/monitor-pipeline.png)

2. 将自动从左侧的“资源浏览器”中选中新创建的 **DWLoadData-fromSQLServer** 数据加载管道。

    ![查看管道](media/sql-data-warehouse-load-with-data-factory/view-pipeline.png)

3. 单击中间面板中的管道可查看映射到活动的每个表的详细状态。

    ![查看表活动](media/sql-data-warehouse-load-with-data-factory/view-table-activity.png)

4. 进一步单击活动可在右侧面板中查看数据加载详细信息，包括数据大小、行、吞吐量，等等。

    ![查看表活动详细信息](media/sql-data-warehouse-load-with-data-factory/view-table-activity-details.png)

5. 以后要启动此监视视图，请转到 SQL 数据仓库，单击“加载数据”>“Azure 数据工厂”，选择工厂，然后选择“监视现有加载任务”。

## <a name="next-steps"></a>后续步骤

要将数据库迁移到 SQL 数据仓库，请参阅 [Migration overview](sql-data-warehouse-overview-migrate.md)（迁移概述）。

若要详细了解 Azure 数据工厂及其数据移动功能，请参阅以下文章：

- [Azure 数据工厂简介](../data-factory/introduction.md)
- [使用复制活动移动数据](../data-factory/copy-activity-overview.md)
- [使用 Azure 数据工厂将数据移出或移入 Azure SQL 数据仓库](../data-factory/connector-azure-sql-data-warehouse.md)

若要浏览 SQL 数据仓库中的数据，请参阅以下文章：

- [使用 Visual Studio 和 SSDT 连接到 SQL 数据仓库](sql-data-warehouse-query-visual-studio.md)
- [使用 Power BI 可视化数据](sql-data-warehouse-get-started-visualize-with-power-bi.md)。

<!-- Azure references -->
[Azure 门户]: https://portal.azure.com

