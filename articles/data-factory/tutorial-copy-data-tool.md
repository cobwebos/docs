---
title: "使用 Azure 复制数据工具复制数据 | Microsoft Docs"
description: "创建一个 Azure 数据工厂，然后使用“复制数据”工具将数据从 Azure Blob 存储复制到 Azure SQL 数据库。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.topic: hero-article
ms.date: 01/09/2018
ms.author: jingwang
ms.openlocfilehash: a8c7035ebf462bb168147e9d8eb60742333ce8b8
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/18/2018
---
# <a name="copy-data-from-azure-blob-to-azure-sql-database-using-copy-data-tool"></a>使用“复制数据”工具将数据从 Azure Blob 复制到 Azure SQL 数据库
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [版本 1 - 正式版](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [版本 2 - 预览版](tutorial-copy-data-tool.md)

在本教程中，我们将使用 Azure 门户创建数据工厂。 然后，使用“复制数据”工具创建一个管道，用于将数据从 Azure Blob 存储复制到 Azure SQL 数据库。 

> [!NOTE]
> - 如果你对 Azure 数据工厂不熟悉，请参阅 [Azure 数据工厂简介](introduction.md)。
>
> - 本文适用于目前处于预览版的数据工厂版本 2。 如果使用数据工厂服务版本 1（即正式版 (GA)），请参阅[数据工厂版本 1 入门](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。


在本教程中执行以下步骤：

> [!div class="checklist"]
> * 创建数据工厂。
> * 使用“复制数据”工具创建管道
> * 监视管道和活动运行。

## <a name="prerequisites"></a>先决条件

* **Azure 订阅**。 如果你还没有 Azure 订阅，可以在开始前创建一个[免费](https://azure.microsoft.com/free/)帐户。
* **Azure 存储帐户**。 可将 Blob 存储用作**源**数据存储。 如果没有 Azure 存储帐户，请参阅[创建存储帐户](../storage/common/storage-create-storage-account.md#create-a-storage-account)一文获取创建步骤。
* **Azure SQL 数据库**。 将数据库用作**接收器**数据存储。 如果没有 Azure SQL 数据库，请参阅[创建 Azure SQL 数据库](../sql-database/sql-database-get-started-portal.md)一文获取创建步骤。

### <a name="create-a-blob-and-a-sql-table"></a>创建 blob 和 SQL 表

现在，执行以下步骤来准备本教程所需的 Azure Blob 和 Azure SQL 数据库：

#### <a name="create-a-source-blob"></a>创建源 blob

1. 启动记事本。 复制以下文本并将其在磁盘上另存为 **inputEmp.txt** 文件。

    ```
    John|Doe
    Jane|Doe
    ```

2. 使用 [Azure 存储资源管理器](http://storageexplorer.com/)等工具创建 **adfv2tutorial** 容器，并将 **inputEmp.txt** 文件上传到该容器。

#### <a name="create-a-sink-sql-table"></a>创建接收器 SQL 表

1. 使用以下 SQL 脚本在 Azure SQL 数据库中创建 **dbo.emp** 表。

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

2. 允许 Azure 服务访问 SQL Server。 确保 Azure SQL Server 的“允许访问 Azure 服务”设置已启用，以便数据工厂服务可以将数据写入 Azure SQL Server。 若要验证并启用此设置，请执行以下步骤：

    1. 单击左侧的“更多服务”中心，并单击“SQL Server”。
    2. 选择服务器，并单击“设置”下的“防火墙”。
    3. 在“防火墙设置”页中，单击“允许访问 Azure 服务”对应的“打开”。

## <a name="create-a-data-factory"></a>创建数据工厂

1. 在左侧菜单中单击“新建”，并依次单击“数据 + 分析”、“数据工厂”。 
   
   ![新建 -> DataFactory](./media/tutorial-copy-data-tool/new-azure-data-factory-menu.png)
2. 在“新建数据工厂”页中，输入 **ADFTutorialDataFactory** 作为**名称**。 
      
     ![“新建数据工厂”页](./media/tutorial-copy-data-tool/new-azure-data-factory.png)
 
   Azure 数据工厂的名称必须 **全局唯一**。 如果看到名称字段的以下错误，请更改数据工厂的名称（例如，改为 yournameADFTutorialDataFactory）。 有关数据工厂项目命名规则，请参阅[数据工厂 - 命名规则](naming-rules.md)一文。
  
     ![“新建数据工厂”页](./media/tutorial-copy-data-tool/name-not-available-error.png)
3. 选择要在其中创建数据工厂的 Azure **订阅**。 
4. 对于**资源组**，请执行以下步骤之一：
     
      - 选择“使用现有资源组”，并从下拉列表选择现有的资源组。 
      - 选择“新建”，并输入资源组的名称。   
         
      若要了解有关资源组的详细信息，请参阅 [使用资源组管理 Azure 资源](../azure-resource-manager/resource-group-overview.md)。  
4. 选择“V2 (预览)”作为**版本**。
5. 选择数据工厂的**位置**。 下拉列表中仅显示支持的位置。 数据工厂使用的数据存储（Azure 存储、Azure SQL 数据库等）和计算资源（HDInsight 等）可以位于其他位置/区域。
6. 选择“固定到仪表板”。     
7. 单击“创建”。
8. 在仪表板上，会看到状态为“正在部署数据工厂”的以下磁贴。 

    ![“正在部署数据工厂”磁贴](media/tutorial-copy-data-tool/deploying-data-factory.png)
9. 创建完成后，会显示图中所示的“数据工厂”页。
   
   ![数据工厂主页](./media/tutorial-copy-data-tool/data-factory-home-page.png)
10. 单击“创作和监视”磁贴，在单独的选项卡中启动 Azure 数据工厂用户界面 (UI)。 

## <a name="use-copy-data-tool-to-create-a-pipeline"></a>使用“复制数据”工具创建管道

1. 在入门页中，单击“复制数据”磁贴启动“复制数据”工具。 

   ![“复制数据”工具磁贴](./media/tutorial-copy-data-tool/copy-data-tool-tile.png)
2. 在“复制数据”工具的“属性”页中，为“任务名称”指定 **CopyFromBlobToSqlPipeline**，然后单击“下一步”。 数据工厂 UI 将使用指定的任务名称创建一个管道。 

    ![“复制数据”工具 -“属性”页](./media/tutorial-copy-data-tool/copy-data-tool-properties-page.png)
3. 在“源数据存储”页中选择“Azure Blob 存储”，然后单击“下一步”。 源数据位于 Azure Blob 存储中。 

    ![“源数据存储”页](./media/tutorial-copy-data-tool/source-data-store-page.png)
4. 在“指定 Azure Blob 存储帐户”页中执行以下步骤： 
    1. 输入 **AzureStorageLinkedService** 作为**连接名称**。
    2. 从下拉列表中选择自己的**存储帐户名称**。
    3. 单击“资源组名称” 的 Azure 数据工厂。 

        ![指定 Blob 存储帐户](./media/tutorial-copy-data-tool/specify-blob-storage-account.png)

        链接服务可将数据存储或计算链接到数据工厂。 在本例中，我们将创建一个 Azure 存储链接服务，用于将 Azure 存储帐户链接到数据存储。 该链接服务包含的连接信息可供数据工厂服务用来在运行时连接到 Blob 存储。 数据集指定包含源数据的容器、文件夹和文件（可选）。 
5. 在“选择输入文件或文件夹”页中执行以下步骤：
    
    1. 导航到“adfv2tutorial/input”文件夹。
    2. 选择“inputEmp.txt”
    3. 单击“选择”。 也可以双击“inputEmp.txt”。 
    4. 单击“资源组名称” 的 Azure 数据工厂。 

    ![选择输入文件或文件夹](./media/tutorial-copy-data-tool/choose-input-file-folder.png)
6. 在“文件格式设置”页中，注意该工具会自动检测列与行的分隔符，然后单击“下一步”。 还可以在此页中预览数据，以及查看输入数据的架构。 

    ![“文件格式设置”页](./media/tutorial-copy-data-tool/file-format-settings-page.png)
7. 在“目标数据存储”页中，选择“Azure SQL 数据库”，然后单击“下一步”。 

    ![“目标数据存储”页](./media/tutorial-copy-data-tool/destination-data-storage-page.png)
8. 在“指定 Azure SQL 数据库”页中执行以下步骤： 

    1. 对于“连接名称”字段，请指定 **AzureSqlDatabaseLinkedService**。 
    2. 对于“服务器名称”，请选择自己的 Azure SQL Server。
    3. 对于“数据库名称”，请选择自己的 Azure SQL 数据库。
    4. 在“用户名”中指定用户名。
    5. 在“密码”中指定该用户的密码。
    6. 单击“资源组名称” 的 Azure 数据工厂。 

        ![指定 Azure SQL 数据库](./media/tutorial-copy-data-tool/specify-azure-sql-database.png)

        数据集必须与链接服务相关联。 该链接服务包含的连接字符串可供数据工厂服务用于在运行时连接到 Azure SQL 数据库。 数据集指定可将数据复制到其中的容器、文件夹和文件（可选）。
9.  在“表映射”页中选择“[dbo].[emp]”，然后单击“下一步”。 

    ![“表映射”页](./media/tutorial-copy-data-tool/table-mapping-page.png)
10. 在“架构映射”页中，注意输入文件中的第一个和第二个列已映射到 **emp** 表的 **FirstName** 和 **LastName** 列。 

    ![“架构映射”页](./media/tutorial-copy-data-tool/schema-mapping-page.png)
11. 在“设置”页中，单击“下一步”。 

    ![“设置”页](./media/tutorial-copy-data-tool/settings-page.png)
12. 在“摘要”页中检查设置，然后单击“下一步”。

    ![“摘要”页](./media/tutorial-copy-data-tool/summary-page.png)
13. 在“部署”页中，单击“监视”可以监视管道（任务）。

    ![“部署”页](./media/tutorial-copy-data-tool/deployment-page.png)
14. 请注意，界面中已自动选择左侧的“监视”选项卡。 在“操作”列中，可以看到用于查看活动运行详细信息以及重新运行管道的链接。 单击“刷新”可刷新列表。 

    ![监视管道运行](./media/tutorial-copy-data-tool/monitor-pipeline-runs.png)
15. 若要查看与管道运行相关联的活动运行，请单击“操作”列中的“查看活动运行”链接。 该管道只包含一个活动（复制活动），因此只显示了一个条目。 有关复制操作的详细信息，请单击“操作”列中的“详细信息”链接（眼镜图标）。 若要切换回到管道运行视图，请单击顶部的“管道”链接。 若要刷新视图，请单击“刷新”。 

    ![监视活动运行](./media/tutorial-copy-data-tool/monitor-activity-runs.png)
16. 单击左侧的“编辑”选项卡可以切换到编辑器模式。 可以使用编辑器来更新该工具创建的链接服务、数据集和管道。 单击“代码”可以查看与编辑器中打开的实体相关联的 JSON 代码。 有关在数据工厂 UI 中编辑这些实体的详细信息，请参阅[本教程的 Azure 门户版本](tutorial-copy-data-portal.md)。

    ![编辑器选项卡](./media/tutorial-copy-data-tool/edit-tab.png)
17. 验证数据是否已插入到 Azure SQL 数据库中的 **emp** 表。 

    ![验证 SQL 输出](./media/tutorial-copy-data-tool/verify-sql-output.png)

## <a name="next-steps"></a>后续步骤
此示例中的管道将数据从 Azure Blob 存储复制到 Azure SQL 数据库。 你已了解如何： 

> [!div class="checklist"]
> * 创建数据工厂。
> * 使用“复制数据”工具创建管道
> * 监视管道和活动运行。

若要了解如何将数据从本地复制到云，请转到以下教程： 

> [!div class="nextstepaction"]
>[将数据从本地复制到云](tutorial-hybrid-copy-data-tool.md)