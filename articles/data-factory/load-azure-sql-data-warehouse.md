---
title: "使用 Azure 数据工厂向 Azure SQL 数据仓库加载数据 | Microsoft Docs"
description: "使用 Azure 数据工厂将数据复制到 Azure SQL 数据仓库"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.topic: article
ms.date: 01/17/2018
ms.author: jingwang
ms.openlocfilehash: eec6eeb3419c5f5f4c8d22398051f7cf057ac980
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2018
---
# <a name="load-data-into-azure-sql-data-warehouse-by-using-azure-data-factory"></a>使用 Azure 数据工厂向 Azure SQL 数据仓库加载数据

[Azure SQL 数据仓库](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)是一种基于云的向外扩展数据库，可以处理大量数据（关系数据和非关系数据）。 SQL 数据仓库在大规模并行处理 (MPP) 体系结构的基础上构建，已针对企业数据仓库工作负荷进行优化。 它通过灵活地缩放存储以及独立计算提供云灵活性。

现在通过使用 Azure 数据工厂，Azure SQL 数据仓库入门变得前所未有地简单。 Azure 数据工厂是一个完全托管的基于云的数据集成服务。 该服务可用于使用现有系统中的数据填充 SQL 数据仓库，在生成分析解决方案时节省时间。

以下是使用 Azure 数据工厂将数据加载到 Azure SQL 数据仓库的优点：

* **轻松设置**：无需脚本的 5 步直观的向导。
* **丰富的数据存储支持**：对一组丰富的本地和基于云的数据存储的内置支持。 有关详细列表，请参阅表[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。
* **安全且合规**：通过 HTTPS 或 ExpressRoute 传输数据。 存在全局服务可确保数据永远不会离开地理边界。
* **通过使用 PolyBase 提供无与伦比的性能**：使用 Polybase 是将数据移动到 Azure SQL 数据仓库的最高效的方法。 使用临时 blob 功能，可以实现所有类型的数据存储（包括 Azure Blob 存储和 Data Lake Store）的高加载速度。 （默认情况下，Polybase 支持 Azure Blob 存储和 Azure Data Lake Store。）有关详细信息，请参阅[复制活动性能](copy-activity-performance.md)。

本文介绍如何使用数据工厂复制数据工具将数据从 Azure SQL 数据库加载至 Azure SQL 数据仓库。 可以遵循类似步骤，从其他类型的数据存储中复制数据。

> [!NOTE]
> 有关详细信息，请参阅[使用 Azure 数据工厂向/从 Azure SQL 数据仓库复制数据](connector-azure-sql-data-warehouse.md)。
>
> 本文适用于目前处于预览状态的 Azure 数据工厂第 2 版。 如果使用正式版 (GA) 版本 1 的数据工厂服务，请参阅 [Azure 数据工厂版本 1 中的复制活动](v1/data-factory-data-movement-activities.md)。

## <a name="prerequisites"></a>先决条件

* Azure 订阅：如果没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/)。
* Azure SQL 数据仓库：数据仓库包含从 SQL 数据库复制的数据。 如果没有 Azure SQL 数据仓库，请参阅[创建 Azure SQL 数据仓库](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md)中的说明。
* Azure SQL 数据库：本教程使用 Adventure Works LT 示例数据从 Azure SQL 数据库复制数据。 可以遵照[创建 Azure SQL 数据库](../sql-database/sql-database-get-started-portal.md)中的说明创建 SQL 数据库。 
* Azure 存储帐户：Azure 存储用作批量复制操作中的过渡 Blob。 如果没有 Azure 存储帐户，请参阅[创建存储帐户](../storage/common/storage-create-storage-account.md#create-a-storage-account)中的说明。

## <a name="create-a-data-factory"></a>创建数据工厂

1. 在左侧菜单中，选择“新建” > “数据 + 分析” > “数据工厂”： 
   
   ![新建数据工厂](./media/load-azure-sql-data-warehouse/new-azure-data-factory-menu.png)
2. 在“新建数据工厂”页中，为下图中所示的字段提供值：
      
   ![“新建数据工厂”页](./media/load-azure-sql-data-warehouse/new-azure-data-factory.png)
 
    * **名称**：输入 Azure 数据工厂的全局唯一名称。 如果收到错误“数据工厂名称 \"LoadSQLDWDemo\" 不可用”，请输入不同的数据工厂名称。 例如，可以使用名称 _**yourname**_**ADFTutorialDataFactory**。 请重试创建数据工厂。 有关数据工厂项目的命名规则，请参阅[数据工厂 - 命名规则](naming-rules.md)。
    * **订阅**：选择要在其中创建数据工厂的 Azure 订阅。 
    * **资源组**：从下拉列表中选择现有资源组，或选择“新建”选项并输入资源组的名称。 若要了解有关资源组的详细信息，请参阅 [使用资源组管理 Azure 资源](../azure-resource-manager/resource-group-overview.md)。  
    * **版本**：选择“V2 (预览版)”。
    * **位置**：选择数据工厂所在的位置。 下拉列表中仅显示支持的位置。 数据工厂使用的数据存储可以在其他位置和区域中。 这些数据存储包括 Azure Data Lake Store、Azure 存储、Azure SQL 数据库，等等。

3. 选择“创建”。
4. 创建操作完成后，请转到数据工厂。 此时会看到“数据工厂”主页，如下图所示：
   
   ![数据工厂主页](./media/load-azure-sql-data-warehouse/data-factory-home-page.png)

   选择“创作和监视”磁贴，在单独的选项卡中启动数据集成应用程序。

## <a name="load-data-into-azure-sql-data-warehouse"></a>将数据加载到 Azure SQL 数据仓库

1. 在“入门”页中，单击“复制数据”磁贴启动“复制数据”工具：

   ![“复制数据”工具磁贴](./media/load-azure-sql-data-warehouse/copy-data-tool-tile.png)
2. 在“属性”页中，为“任务名称”字段指定“CopyFromSQLToSQLDW”，然后选择“下一步”：

    ![“属性”页](./media/load-azure-sql-data-warehouse/copy-data-tool-properties-page.png)
3. 在“源数据存储”页中，选择“Azure SQL 数据库”，然后选择“下一步”：

    ![“源数据存储”页](./media/load-azure-sql-data-warehouse/specify-source.png)
4. 在“指定 Azure SQL 数据库”页中执行以下步骤： 
   1. 对于“服务器名称”，请选择自己的 Azure SQL Server。
   2. 对于“数据库名称”，请选择自己的 Azure SQL 数据库。
   3. 在“用户名”中指定用户名。
   4. 在“密码”中指定该用户的密码。
   5. 选择“**下一步**”。
   
   ![指定 Azure SQL DB](./media/load-azure-sql-data-warehouse/specify-source-connection.png)
5. 在“选择要从中复制数据的表或使用自定义查询”页中，输入 **SalesLT** 以筛选表。 选中“(全选)”复选框以便对副本使用所有表，然后选择“下一步”： 

    ![选择源表](./media/load-azure-sql-data-warehouse/select-source-tables.png)

6. 在“目标数据存储”页中，选择“Azure SQL 数据仓库”，然后选择“下一步”：

    ![“目标数据存储”页](./media/load-azure-sql-data-warehouse/specify-sink.png)
7. 在“指定 Azure SQL 数据仓库”页中执行以下步骤： 

   1. 对于“服务器名称”，请选择自己的 Azure SQL Server。
   2. 对于“数据库名称”，请选择自己的 Azure SQL 数据仓库。
   3. 在“用户名”中指定用户名。
   4. 在“密码”中指定该用户的密码。
   5. 选择“**下一步**”。
   
   ![指定 Azure SQL 数据仓库](./media/load-azure-sql-data-warehouse/specify-sink-connection.png)
8. 在“表映射”页中查看内容并选择“下一步”。 此时会显示智能表映射。 源表已根据表名映射到目标表。 如果目标中不存在表，则默认情况下 Azure 数据工厂将创建一个具有相同名称的目标表。 还可以将源表映射到现有目标表。 

   > [!NOTE]
   > 当 SQL Server 或 Azure SQL 数据库为源时，将应用 SQL 数据仓库接收器的自动表创建。 若从其他源数据存储复制数据，则在执行数据复制前，需先在接收器 Azure SQL 数据仓库中预创建架构。

   ![“表映射”页](./media/load-azure-sql-data-warehouse/specify-table-mapping.png)

9. 在“架构映射”页中查看内容并选择“下一步”。 智能表映射基于列名。 如果自动创建的表的数据工厂，源和目标存储之间存在不兼容时，可能发生数据类型转换。 如果在源列与目标列之间进行不受支持的数据类型转换，会显示错误消息以及相应的表。

    ![“架构映射”页](./media/load-azure-sql-data-warehouse/specify-schema-mapping.png)

11. 从“设置”页的“存储帐户名称”下拉列表中选择 Azure 存储帐户。 该帐户用于在使用 PolyBase 将数据加载至 SQL 数据仓库前暂存数据。 复制完成后，会自动清除 Azure 存储中的临时数据。 在“高级设置”下，取消选中“使用类型默认设置”选项：

    ![“设置”页](./media/load-azure-sql-data-warehouse/copy-settings.png)
12. 在“摘要”页中检查设置，然后选择“下一步”：

    ![“摘要”页](./media/load-azure-sql-data-warehouse/summary-page.png)
13. 在“部署”页中，选择“监视”可以监视管道（任务）：

    ![“部署”页](./media/load-azure-sql-data-warehouse/deployment-page.png)
14. 请注意，界面中已自动选择左侧的“监视”选项卡。 “操作”列中包含用于查看活动运行详细信息以及用于重新运行管道的链接： 

    ![监视管道运行](./media/load-azure-sql-data-warehouse/monitor-pipeline-run.png)
15. 若要查看与管道运行关联的活动运行，请选择“操作”列中的“查看活动运行”链接。 管道中有 10 个复制活动，每个活动复制数据的一个表。 若要切换回到管道运行视图，请选择顶部的“管道”链接。 选择“刷新”可刷新列表。 

    ![监视活动运行](./media/load-azure-sql-data-warehouse/monitor-activity-run.png)

16. 若要监视每个复制活动的执行详细信息，请在活动监视视图中选择“操作”下的“详细信息”链接。 可以监视详细信息，如从源复制到接收器的数据量、吞吐量、执行步骤以及相应的持续时间和使用的配置：

    ![监视活动运行详细信息](./media/load-azure-sql-data-warehouse/monitor-activity-run-details.png)

## <a name="next-steps"></a>后续步骤

请转至下列文章，了解有关 Azure SQL 数据仓库支持的相关信息： 

> [!div class="nextstepaction"]
>[Azure SQL 数据仓库连接器](connector-azure-sql-data-warehouse.md)