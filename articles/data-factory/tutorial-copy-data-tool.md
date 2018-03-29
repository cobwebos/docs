---
title: 使用 Azure 复制数据工具复制数据 | Microsoft Docs
description: 创建一个 Azure 数据工厂，然后使用“复制数据”工具将数据从 Azure Blob 存储复制到 SQL 数据库。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: hero-article
ms.date: 01/09/2018
ms.author: jingwang
ms.openlocfilehash: d2f1d089c6a08a1dc90f82fd9d1c3cb2b6f6dc0a
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="copy-data-from-azure-blob-storage-to-a-sql-database-by-using-the-copy-data-tool"></a>使用“复制数据”工具，将数据从 Azure Blob 存储复制到 SQL 数据库
> [!div class="op_single_selector" title1="Select the version of the Data Factory service that you're using:"]
> * [版本 1 - 正式版](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [版本 2 - 预览版](tutorial-copy-data-tool.md)

在本教程中，我们将使用 Azure 门户创建数据工厂。 然后，使用“复制数据”工具创建一个管道，以便将数据从 Azure Blob 存储复制到 SQL 数据库。 

> [!NOTE]
> 如果对 Azure 数据工厂不熟悉，请参阅 [Azure 数据工厂简介](introduction.md)。
>
> 本文适用于目前处于预览状态的数据工厂版本 2。 如果使用公开发布的第 1 版数据工厂，请参阅[数据工厂第 1 版入门](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。


将在本教程中执行以下步骤：

> [!div class="checklist"]
> * 创建数据工厂。
> * 使用“复制数据”工具创建管道。
> * 监视管道和活动运行。

## <a name="prerequisites"></a>先决条件

* **Azure 订阅**：如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/)。
* **Azure 存储帐户**：使用 Blob 存储作为源数据存储。 如果没有 Azure 存储帐户，请参阅[创建存储帐户](../storage/common/storage-create-storage-account.md#create-a-storage-account)中的说明。
* **Azure SQL 数据库**：使用 SQL 数据库作为接收器数据存储。 如果没有 SQL 数据库，请参阅[创建 SQL 数据库](../sql-database/sql-database-get-started-portal.md)中的说明。

### <a name="create-a-blob-and-a-sql-table"></a>创建 blob 和 SQL 表

执行以下步骤，准备本教程所需的 Blob 存储和 SQL 数据库。

#### <a name="create-a-source-blob"></a>创建源 blob

1. 启动**记事本**。 复制以下文本，并在磁盘上将其保存在名为 **inputEmp.txt** 的文件中：

    ```
    John|Doe
    Jane|Doe
    ```

2. 创建名为 **adfv2tutorial** 的容器，然后将 inputEmp.txt 文件上传到该容器中。 可以使用各种工具（例如 [Azure 存储资源管理器](http://storageexplorer.com/)）来执行这些任务。

#### <a name="create-a-sink-sql-table"></a>创建接收器 SQL 表

1. 使用以下 SQL 脚本在 SQL 数据库中创建名为 **dbo.emp** 的表：

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

2. 允许 Azure 服务访问 SQL Server。 验证是否为运行 SQL Server 的服务器启用“允许访问 Azure 服务”设置。 此设置允许数据工厂将数据写入到 SQL Server 实例。 若要验证并启用此设置，请执行以下步骤：

    a. 在左侧选择“更多服务”，然后选择“SQL Server”。

    b. 选择服务器，然后选择“设置” > “防火墙”。

    c. 在“防火墙设置”页中，将“允许访问 Azure 服务”选项设置为“打开”。

## <a name="create-a-data-factory"></a>创建数据工厂

1. 在左侧菜单中，选择“+ 新建” > “数据 + 分析” > “数据工厂”： 
   
   ![新建数据工厂](./media/tutorial-copy-data-tool/new-azure-data-factory-menu.png)
2. 在“新建数据工厂”页的“名称”下输入 **ADFTutorialDataFactory**。 
      
     ![新建数据工厂](./media/tutorial-copy-data-tool/new-azure-data-factory.png)
 
   数据工厂的名称必须全局唯一。 可能会收到以下错误消息：
   
   ![新的数据工厂错误消息](./media/tutorial-copy-data-tool/name-not-available-error.png)

   如果收到有关名称值的错误消息，请为数据工厂输入另一名称。 例如，使用名称 _**yourname**_**ADFTutorialDataFactory**。 有关数据工厂项目的命名规则，请参阅[数据工厂命名规则](naming-rules.md)。
3. 选择要在其中创建新数据工厂的 Azure **订阅**。 
4. 对于“资源组”，请执行以下步骤之一：
     
    a. 选择“使用现有资源组”，并从下拉列表选择现有的资源组。

    b. 选择“新建”，并输入资源组的名称。 
         
    若要了解资源组，请参阅[使用资源组管理 Azure 资源](../azure-resource-manager/resource-group-overview.md)。

5. 在“版本”下选择“V2 (预览)”作为版本。
6. 在“位置”下选择数据工厂的位置。 下拉列表中仅显示支持的位置。 数据工厂使用的数据存储（例如，Azure 存储和 SQL 数据库）和计算资源（例如，Azure HDInsight）可以位于其他位置和区域。
7. 选择“固定到仪表板”。 
8. 选择**创建**。
9. 在仪表板中，“部署数据工厂”磁贴显示进程状态。

    ![“部署数据工厂”磁贴](media/tutorial-copy-data-tool/deploying-data-factory.png)
10. 创建完以后，会显示“数据工厂”主页。
   
    ![数据工厂主页](./media/tutorial-copy-data-tool/data-factory-home-page.png)
11. 若要在单独的选项卡中启动 Azure 数据工厂用户界面 (UI)，请选择“创作和监视”磁贴。 

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>使用“复制数据”工具创建管道

1. 在“开始使用”页中选择“复制数据”磁贴，启动“复制数据”工具。 

   ![“复制数据”工具磁贴](./media/tutorial-copy-data-tool/copy-data-tool-tile.png)
2. 在“属性”页的“任务名称”下，输入 **CopyFromBlobToSqlPipeline**。 然后，选择“下一步”。 数据工厂 UI 将使用指定的任务名称创建一个管道。 

    ![“属性”页](./media/tutorial-copy-data-tool/copy-data-tool-properties-page.png)
3. 在“源数据存储”页中选择“Azure Blob 存储”，然后选择“下一步”。 源数据位于 Blob 存储中。 

    ![“源数据存储”页](./media/tutorial-copy-data-tool/source-data-store-page.png)
4. 在“指定 Azure Blob 存储帐户”页中执行以下步骤：

    a. 在“连接名称”下输入 **AzureStorageLinkedService**。

    b. 从“存储帐户名称”下拉列表中选择自己的存储帐户名称。

    c. 选择“**下一步**”。 

    ![指定存储帐户](./media/tutorial-copy-data-tool/specify-blob-storage-account.png)

    链接服务可将数据存储或计算链接到数据工厂。 本示例创建一个存储链接服务，用于将存储帐户链接到数据存储。 该链接服务包含的连接信息可供数据工厂用来在运行时连接到 Blob 存储。 数据集指定包含源数据的容器、文件夹和文件（可选）。 

5. 在“选择输入文件或文件夹”页中执行以下步骤：
    
    a. 浏览到“adfv2tutorial/input”文件夹。

    b. 选择“inputEmp.txt”文件。

    c. 选择“选择”。 也可以双击“inputEmp.txt”文件。

    d.单击“下一步”。 选择“**下一步**”。 

    ![选择输入文件或文件夹](./media/tutorial-copy-data-tool/choose-input-file-folder.png)

6. 在“文件格式设置”页中，注意该工具会自动检测列与行的分隔符。 选择“**下一步**”。 还可以在此页中预览数据，以及查看输入数据的架构。 

    ![文件格式设置](./media/tutorial-copy-data-tool/file-format-settings-page.png)
7. 在“目标数据存储”页上，选择“Azure SQL 数据库”，然后选择“下一步”。

    ![目标数据存储](./media/tutorial-copy-data-tool/destination-data-storage-page.png)
8. 在“指定 Azure SQL 数据库”页中执行以下步骤： 

    a. 在“连接名称”下输入 **AzureSqlDatabaseLinkedService**。

    b. 在“服务器名称”下选择 SQL Server 实例。

    c. 在“数据库名称”下选择自己的 SQL 数据库。

    d.单击“下一步”。 在“用户名”下输入用户的名称。

    e. 在“密码”下输入用户的密码。

    f. 选择“**下一步**”。 

    ![指定 SQL 数据库](./media/tutorial-copy-data-tool/specify-azure-sql-database.png)

    数据集必须与链接服务相关联。 该链接服务包含的连接字符串可供数据工厂用于在运行时连接到 SQL 数据库。 数据集指定可将数据复制到其中的容器、文件夹和文件（可选）。

9. 在“表映射”页中，选择 **[dbo].[emp]** 表，然后选择“下一步”。 

    ![表映射](./media/tutorial-copy-data-tool/table-mapping-page.png)
10. 在“架构映射”页中，注意输入文件中的第一个和第二个列已映射到 **emp** 表的 **FirstName** 和 **LastName** 列。

    ![“架构映射”页](./media/tutorial-copy-data-tool/schema-mapping-page.png)
11. 在“设置”页中，选择“下一步”。 

    ![“设置”页](./media/tutorial-copy-data-tool/settings-page.png)
12. 在“摘要”页中检查设置，然后选择“下一步”。

    ![“摘要”页](./media/tutorial-copy-data-tool/summary-page.png)
13. 在“部署”页中，选择“监视”可以监视管道（任务）。

    ![“部署”页](./media/tutorial-copy-data-tool/deployment-page.png)
14. 请注意，界面中已自动选择左侧的“监视”选项卡。 “操作”列中包含用于查看活动运行详细信息以及用于重新运行管道的链接。 选择“刷新”可刷新列表。 

    ![监视管道运行](./media/tutorial-copy-data-tool/monitor-pipeline-runs.png)
15. 若要查看与管道运行关联的活动运行，请选择“操作”列中的“查看活动运行”链接。 该管道只包含一个活动（复制活动），因此只显示了一个条目。 有关复制操作的详细信息，请选择“操作”列中的“详细信息”链接（眼镜图标）。 若要回到“管道运行”视图，请选择顶部的“管道”链接。 若要刷新视图，请选择“刷新”。 

    ![监视活动运行](./media/tutorial-copy-data-tool/monitor-activity-runs.png)
16. 选择左侧的“编辑”选项卡可以切换到编辑器模式。 可以使用编辑器来更新通过该工具创建的链接服务、数据集和管道。 选择“代码”可以查看当前在编辑器中打开的实体的 JSON 代码。 有关在数据工厂 UI 中编辑这些实体的详细信息，请参阅[本教程的 Azure 门户版本](tutorial-copy-data-portal.md)。

    ![编辑器选项卡](./media/tutorial-copy-data-tool/edit-tab.png)
17. 验证数据是否已插入到 SQL 数据库中的 **emp** 表。

    ![验证 SQL 输出](./media/tutorial-copy-data-tool/verify-sql-output.png)

## <a name="next-steps"></a>后续步骤
此示例中的管道将数据从 Blob 存储复制到 SQL 数据库。 你已了解如何： 

> [!div class="checklist"]
> * 创建数据工厂。
> * 使用“复制数据”工具创建管道。
> * 监视管道和活动运行。

若要了解如何将数据从本地复制到云，请转到以下教程： 

> [!div class="nextstepaction"]
>[将数据从本地复制到云](tutorial-hybrid-copy-data-tool.md)
