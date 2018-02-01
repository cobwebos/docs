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
ms.openlocfilehash: 36e24da50386d1abc441e2beb09f570a9612a346
ms.sourcegitcommit: 828cd4b47fbd7d7d620fbb93a592559256f9d234
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/18/2018
---
# <a name="load-data-into-azure-sql-data-warehouse-using-azure-data-factory"></a>使用 Azure 数据工厂向 Azure SQL 数据仓库加载数据

[Azure SQL 数据仓库](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)是一种基于云的向外扩展数据库，可以处理大量数据（关系数据和非关系数据）。  SQL 数据仓库在大规模并行处理 (MPP) 体系结构的基础上构建，已针对企业数据仓库工作负荷进行优化。  它通过灵活地缩放存储以及独立计算提供云灵活性。

现在通过使用 **Azure 数据工厂**，Azure SQL 数据仓库入门变得前所未有地简单。  Azure 数据工厂是一个完全托管的基于云的数据集成服务，它可用于使用现有系统中的数据填充 SQL 数据仓库，在节省宝贵时间的同时评估 SQL 数据仓库并生成分析解决方案。 以下是使用 Azure 数据工厂将数据加载到 Azure SQL 数据仓库的主要优点：

* **轻松设置**：无需脚本的 5 步直观的向导。
* **丰富的数据存储支持**：对一组丰富的本地和基于云的数据存储的内置支持，有关详细列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。
* **安全且合规**：通过 HTTPS 或 ExpressRoute 传输数据，并且全局服务可确保数据不会离开地理边界
* **通过使用 PolyBase 提供无与伦比的性能**：使用 Polybase 是将数据移动到 Azure SQL 数据仓库的最高效的方法。 使用临时 blob 功能，可以实现所有类型的数据存储（包括 Azure Blob 存储和 Data Lake Store）的高加载速度，默认情况下，Polybase 支持此功能。 从[复制活动性能](copy-activity-performance.md)中了解详细信息。

本文介绍如何使用数据工厂复制数据工具将数据从 Azure SQL 数据仓库加载至 Azure SQL 数据仓库。 可以遵循类似步骤，从其他类型的数据存储中复制数据。

> [!NOTE]
>  有关数据工厂将数据复制到 Azure SQL 数据仓库或从 Azure SQL 数据仓库复制数据的功能的一般信息，请参阅[使用 Azure 数据工厂将数据复制到 Azure SQL 数据仓库或从 Azure SQL 数据仓库复制数据](connector-azure-sql-data-warehouse.md)一文。
>
> 本文适用于目前处于预览版的数据工厂版本 2。 如果使用正式版 (GA) 1 版本的数据工厂服务，请参阅 [V1 中的复制活动](v1/data-factory-data-movement-activities.md)。

## <a name="prerequisites"></a>先决条件

* **Azure 订阅**。 如果你还没有 Azure 订阅，可以在开始前创建一个[免费](https://azure.microsoft.com/free/)帐户。
* **Azure SQL 数据仓库**。 此数据仓库包含从 SQL 数据库复制的数据。 如果没有 Azure SQL 数据仓库，请参阅[创建 Azure SQL 数据仓库](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md)一文了解创建数据仓库的步骤。
* **Azure SQL 数据库**。 此教程使用 Adventure Works LT 示例数据从 Azure SQL 数据库复制数据，你也可以遵循[创建 Azure SQL 数据库](../sql-database/sql-database-get-started-portal.md)一文创建一个。 
* **Azure 存储帐户**。 Azure 存储用作大容量复制操作中的临时 blob。 如果没有 Azure 存储帐户，请参阅[创建存储帐户](../storage/common/storage-create-storage-account.md#create-a-storage-account)一文获取创建步骤。

## <a name="create-a-data-factory"></a>创建数据工厂

1. 在左侧菜单中单击“新建”，并依次单击“数据 + 分析”、“数据工厂”。 
   
   ![新建 -> DataFactory](./media/load-azure-sql-data-warehouse/new-azure-data-factory-menu.png)
2. 在“新建数据工厂”页中，提供以下屏幕截图中所示的值：
      
     ![“新建数据工厂”页](./media/load-azure-sql-data-warehouse/new-azure-data-factory.png)
 
   * **名称。** 输入数据工厂的全局唯一名称。 如果收到错误，请更改数据工厂的名称（例如改为 yournameADFTutorialDataFactory），并重新尝试创建。 有关数据工厂项目命名规则，请参阅[数据工厂 - 命名规则](naming-rules.md)一文。
  
            `Data factory name "LoadSQLDWDemo" is not available`

    * **订阅。** 选择要在其中创建数据工厂的 Azure **订阅**。 
    * **资源组。** 从下拉列表中选择现有资源组，或选择“新建”选项并输入资源组的名称。 若要了解有关资源组的详细信息，请参阅 [使用资源组管理 Azure 资源](../azure-resource-manager/resource-group-overview.md)。  
    * **版本。** 选择“V2 (预览版)”。
    * **位置。** 选择数据工厂的位置。 下拉列表中仅显示支持的位置。 数据工厂使用的数据存储（Azure 存储、Azure SQL 数据库等）可以位于其他位置/区域。 

3. 单击“创建”。
4. 创建完成后，请转至数据工厂，那里将会显示图中所示的“数据工厂”页。 单击“创作和监视”磁贴，在单独的选项卡中启动数据集成应用程序。
   
   ![数据工厂主页](./media/load-azure-sql-data-warehouse/data-factory-home-page.png)

## <a name="load-data-into-azure-sql-data-warehouse"></a>将数据加载到 Azure SQL 数据仓库

1. 在入门页中，单击“复制数据”磁贴启动“复制数据”工具。 

   ![“复制数据”工具磁贴](./media/load-azure-sql-data-warehouse/copy-data-tool-tile.png)
2. 在“复制数据”工具的“属性”页中，为“任务名称”指定“CopyFromSQLToSQLDW”，然后单击“下一步”。 

    ![“复制数据”工具 -“属性”页](./media/load-azure-sql-data-warehouse/copy-data-tool-properties-page.png)
3. 在“源数据存储”页中，选择“Azure SQL 数据库”，然后单击“下一步”。

    ![“源数据存储”页](./media/load-azure-sql-data-warehouse/specify-source.png)
4. 在“指定 Azure SQL 数据库”页中执行以下步骤： 
    1. 对于“服务器名称”，请选择自己的 Azure SQL Server。
    2. 对于“数据库名称”，请选择自己的 Azure SQL 数据库。
    3. 在“用户名”中指定用户名。
    4. 在“密码”中指定该用户的密码。
    5. 单击“资源组名称” 的 Azure 数据工厂。 

        ![指定 Azure SQL DB](./media/load-azure-sql-data-warehouse/specify-source-connection.png)
5. 在“选择要从其中复制数据或使用自定义查询的表”页中，通过在输入框中指定“SalesLT”来筛选表，然后选中“(全选)”复选框，选择所有表并单击“下一步”。 

    ![选择输入文件或文件夹](./media/load-azure-sql-data-warehouse/select-source-tables.png)

6. 在“目标数据存储”页中，选择“Azure SQL 数据仓库”，然后单击“下一步”。 

    ![“目标数据存储”页](./media/load-azure-sql-data-warehouse/specify-sink.png)
7. 在“指定 Azure SQL 数据仓库”页中执行以下步骤： 

    1. 对于“服务器名称”，请选择自己的 Azure SQL Server。
    2. 对于“数据库名称”，请选择自己的 Azure SQL 数据仓库。
    3. 在“用户名”中指定用户名。
    4. 在“密码”中指定该用户的密码。
    5. 单击“资源组名称” 的 Azure 数据工厂。 

        ![指定 Azure SQL 数据仓库](./media/load-azure-sql-data-warehouse/specify-sink-connection.png)
8. 在“表映射”页中查看并单击“下一步”。 此时会显示智能表映射，它可以根据表名将源映射到目标表。 如果目标中不存在表，则默认情况下 Azure 数据工厂将创建一个具有相同名称的表。 还可选择映射到现有表。 

    > [!NOTE]
    > 当 SQL Server 或 Azure SQL 数据库为源时，将应用 SQL 数据仓库接收器的自动表创建。 若从其他源数据存储复制数据，则在开始复制数据前，需先在接收器 Azure SQL 数据仓库中预创建架构。

    ![“表映射”页](./media/load-azure-sql-data-warehouse/specify-table-mapping.png)

9. 在“架构映射”页中查看并单击“下一步”。 智能映射基于列名。 如果选择让数据工厂自动创建表格，则当需要修复源和目标存储中的不兼容时，可能会出现适当的数据类型转换。 如果在源列与目标列之间进行不受支持的数据类型转换，会显示错误消息以及相应的表。

    ![“架构映射”页](./media/load-azure-sql-data-warehouse/specify-schema-mapping.png)

11. 从“设置”页的“存储帐户名称”下拉列表中选择 Azure 存储。 它用于在使用 PolyBase 将数据加载至 SQL 数据仓库前暂存数据。 复制完成后，会自动清除存储中的临时数据。 

    在“高级设置”下，取消选中“使用类型默认设置”选项。

    ![“设置”页](./media/load-azure-sql-data-warehouse/copy-settings.png)
12. 在“摘要”页中检查设置，然后单击“下一步”。

    ![“摘要”页](./media/load-azure-sql-data-warehouse/summary-page.png)
13. 在“部署”页中，单击“监视”可以监视管道（任务）。

    ![“部署”页](./media/load-azure-sql-data-warehouse/deployment-page.png)
14. 请注意，界面中已自动选择左侧的“监视”选项卡。 在“操作”列中，可以看到用于查看活动运行详细信息以及重新运行管道的链接。 

    ![监视管道运行](./media/load-azure-sql-data-warehouse/monitor-pipeline-run.png)
15. 若要查看与管道运行相关联的活动运行，请单击“操作”列中的“查看活动运行”链接。 管道中有 10 个复制活动，每个活动复制数据的一个表。 若要切换回到管道运行视图，请单击顶部的“管道”链接。 单击“刷新”可刷新列表。 

    ![监视活动运行](./media/load-azure-sql-data-warehouse/monitor-activity-run.png)

16. 可以进一步监视每个复制活动的执行详细信息，方法是在活动监视视图中单击“操作”下的“详细信息”链接。 它显示以下信息：从源复制到接收器的数据量、吞吐量、所执行的步骤以及相应的持续时间和使用的配置。

    ![监视活动运行详细信息](./media/load-azure-sql-data-warehouse/monitor-activity-run-details.png)

## <a name="next-steps"></a>后续步骤

请转至下列文章，了解有关 Azure SQL 数据仓库支持的相关信息： 

> [!div class="nextstepaction"]
>[Azure SQL 数据仓库连接器](connector-azure-sql-data-warehouse.md)