---
title: "使用 Azure 门户创建数据工厂管道 | Microsoft Docs"
description: "本教程分步说明了如何使用 Azure 门户创建带管道的数据工厂。 该管道通过复制活动将数据从 Azure Blob 存储复制到 Azure SQL 数据库。 "
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/09/2018
ms.author: jingwang
ms.openlocfilehash: 8b5211e9c932221c6b6134e7e0627f4d7f964123
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2018
---
# <a name="copy-data-from-azure-blob-to-azure-sql-database-using-azure-data-factory"></a>使用 Azure 数据工厂将数据从 Azure Blob 复制到 Azure SQL 数据库
在本教程中，请使用 Azure 数据工厂用户界面 (UI) 创建数据工厂。 此数据工厂中的管道将数据从 Azure Blob 存储复制到 Azure SQL 数据库。 本教程中的配置模式适用于从基于文件的数据存储复制到关系数据存储。 如需支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

> [!NOTE]
> - 如果你对 Azure 数据工厂不熟悉，请参阅 [Azure 数据工厂简介](introduction.md)。
>
> - 本文适用于目前处于预览状态的数据工厂版本 2。 如果使用数据工厂服务版本 1（即正式版 (GA)），请参阅[数据工厂版本 1 入门](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。

在本教程中执行以下步骤：

> [!div class="checklist"]
> * 创建数据工厂。
> * 创建包含“复制”活动的管道。
> * 测试性运行管道
> * 手动触发管道
> * 按计划触发管道
> * 监视管道和活动运行。

## <a name="prerequisites"></a>先决条件
* **Azure 订阅**。 如果你还没有 Azure 订阅，可以在开始前创建一个[免费](https://azure.microsoft.com/free/)帐户。
* **Azure 存储帐户**。 可将 Blob 存储用作**源**数据存储。 如果没有 Azure 存储帐户，请参阅[创建存储帐户](../storage/common/storage-create-storage-account.md#create-a-storage-account)一文获取创建步骤。
* **Azure SQL 数据库**。 将数据库用作**接收器**数据存储。 如果没有 Azure SQL 数据库，请参阅[创建 Azure SQL 数据库](../sql-database/sql-database-get-started-portal.md)一文获取创建步骤。

### <a name="create-a-blob-and-a-sql-table"></a>创建 blob 和 SQL 表

现在，执行以下步骤来准备本教程所需的 Azure Blob 和 Azure SQL 数据库：

#### <a name="create-a-source-blob"></a>创建源 blob

1. 启动记事本。 复制以下文本并将其在磁盘上另存为 **emp.txt** 文件。

    ```
    John,Doe
    Jane,Doe
    ```

2. 在 Azure Blob 存储中创建名为 **adftutorial** 的容器。 在该容器中创建名为 input 的文件夹。 然后，将 **emp.txt** 文件上传到 **input** 文件夹。 请使用 Azure 门户或工具（例如 [Azure 存储资源管理器](http://storageexplorer.com/)）执行这些任务。

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

2. 允许 Azure 服务访问 SQL Server。 确保 Azure SQL Server 的“允许访问 Azure 服务”设置处于“打开”状态，以便数据工厂服务可以将数据写入 Azure SQL Server。 若要验证并启用此设置，请执行以下步骤：

    1. 单击左侧的“更多服务”中心，并单击“SQL Server”。
    2. 选择服务器，并单击“设置”下的“防火墙”。
    3. 在“防火墙设置”页中，单击“允许访问 Azure 服务”对应的“打开”。

## <a name="create-a-data-factory"></a>创建数据工厂
在此步骤中，请先创建数据工厂，然后启动 Azure 数据工厂 UI，在该数据工厂中创建一个管道。 

1. 在左侧菜单中单击“新建”，并依次单击“数据 + 分析”、“数据工厂”。 
   
   ![新建 -> DataFactory](./media/tutorial-copy-data-portal/new-azure-data-factory-menu.png)
2. 在“新建数据工厂”页中，输入 **ADFTutorialDataFactory** 作为**名称**。 
      
     ![“新建数据工厂”页](./media/tutorial-copy-data-portal/new-azure-data-factory.png)
 
   Azure 数据工厂的名称必须 **全局唯一**。 如果看到名称字段的以下错误，请更改数据工厂的名称（例如，改为 yournameADFTutorialDataFactory）。 有关数据工厂项目命名规则，请参阅[数据工厂 - 命名规则](naming-rules.md)一文。
  
     ![“新建数据工厂”页](./media/tutorial-copy-data-portal/name-not-available-error.png)
3. 选择要在其中创建数据工厂的 Azure **订阅**。 
4. 对于**资源组**，请执行以下步骤之一：
     
      - 选择“使用现有资源组”，并从下拉列表选择现有的资源组。 
      - 选择“新建”，并输入资源组的名称。   
         
        若要了解有关资源组的详细信息，请参阅 [使用资源组管理 Azure 资源](../azure-resource-manager/resource-group-overview.md)。  
4. 选择“V2 (预览)”作为**版本**。
5. 选择数据工厂的**位置**。 下拉列表中仅显示支持的位置。 数据工厂使用的数据存储（Azure 存储、Azure SQL 数据库，等等）和计算资源（HDInsight 等）可以位于其他区域中。
6. 选择“固定到仪表板”。     
7. 单击“创建”。      
8. 在仪表板上，会看到状态为“正在部署数据工厂”的以下磁贴。 

    ![“正在部署数据工厂”磁贴](media/tutorial-copy-data-portal/deploying-data-factory.png)
9. 创建完成后，会显示图中所示的“数据工厂”页。
   
   ![数据工厂主页](./media/tutorial-copy-data-portal/data-factory-home-page.png)
10. 单击“创作和监视”磁贴，在单独的选项卡中启动 Azure 数据工厂用户界面 (UI)。

## <a name="create-a-pipeline"></a>创建管道
本步骤在数据工厂中创建包含“复制”活动的管道。 “复制”活动将数据从 Azure Blob 存储复制到 Azure SQL 数据库。 在[快速入门教程](quickstart-create-data-factory-portal.md)中，已通过以下步骤创建一个管道：

1. 创建链接服务。 
2. 创建输入和输出数据集。
3. 然后创建管道。

在本教程中，一开始创建管道，然后创建用来配置管道的链接服务和数据集。 

1. 在入门页中，单击“创建管道”磁贴。 

   ![“创建管道”磁贴](./media/tutorial-copy-data-portal/create-pipeline-tile.png)
3. 在管道的“属性”窗口中，将管道的**名称**设置为 **CopyPipeline**。

    ![管道名称](./media/tutorial-copy-data-portal/pipeline-name.png)
4. 在“活动”工具箱中，展开 **DataFlow** 类别，然后将**复制**活动从工具箱拖放到管道设计器图面。 

    ![拖放复制活动](./media/tutorial-copy-data-portal/drag-drop-copy-activity.png)
5. 在“属性”窗口的“常规”选项卡中，指定 **CopyFromBlobToSql** 作为活动的名称。

    ![活动名称](./media/tutorial-copy-data-portal/activity-name.png)
6. 切换到“源”选项卡。单击“+ 新建”创建源数据集。 

    ![“新建源数据集”菜单](./media/tutorial-copy-data-portal/new-source-dataset-button.png)
7. 在“新建数据集”窗口中，选择“Azure Blob 存储”，然后单击“完成”。 源数据位于 Azure Blob 存储中，因此选择“Azure Blob 存储”作为源数据集。 

    ![选择“Azure Blob 存储”](./media/tutorial-copy-data-portal/select-azure-storage.png)
8. 此时会看到新**选项卡**在应用程序中打开，其标题为 **AzureBlob1**。

    ![“Azure Blob1”选项卡 ](./media/tutorial-copy-data-portal/new-tab-azure-blob1.png)        
9. 在底部的“属性”窗口的“常规”选项卡中，指定 **SourceBlobDataset** 作为**名称**。

    ![数据集名称](./media/tutorial-copy-data-portal/dataset-name.png)
10. 切换到属性窗口中的“连接”选项卡。 单击“链接服务”文本框旁边的“+ 新建”。 链接服务可将数据存储或计算链接到数据工厂。 在本例中，我们将创建一个 Azure 存储链接服务，用于将 Azure 存储帐户链接到数据存储。 该链接服务包含的连接信息可供数据工厂服务用来在运行时连接到 Blob 存储。 数据集指定包含源数据的容器、文件夹和文件（可选）。 

    ![“新建链接服务”按钮](./media/tutorial-copy-data-portal/source-dataset-new-linked-service-button.png)
12. 在“新建链接服务”窗口中，执行以下步骤： 

    1. 对于“名称”字段，请指定 **AzureStorageLinkedService**。 
    2. 对于“存储帐户名称”字段，请选择 Azure 存储帐户。
    3. 单击“测试连接”，测试到 Azure 存储帐户的连接。  
    4. 单击“保存”保存链接服务。

        ![新建 Azure 存储链接服务](./media/tutorial-copy-data-portal/new-azure-storage-linked-service.png)
13. 对于“文件路径”字段，请单击“浏览”。  

    ![文件的“浏览”按钮](./media/tutorial-copy-data-portal/file-browse-button.png)
14. 导航到 **adftutorial/input** 文件夹，选择 **emp.txt** 文件，然后单击“完成”。 也可以双击 emp.txt。 

    ![选择输入文件](./media/tutorial-copy-data-portal/select-input-file.png)
15. 确认“文件格式”已设置为“文本格式”，“列分隔符”已设置为“逗号(`,`)”。 对于“文件格式”字段，如果源文件使用不同的行分隔符和列分隔符，则可单击“检测文本格式”。 复制数据工具会自动检测文件格式和分隔符。 你仍然可以重写这些值。 可以单击“预览数据”，在此页上预览数据。

    ![检测文本格式](./media/tutorial-copy-data-portal/detect-text-format.png)
17. 在属性窗口中切换到“架构”选项卡，然后单击“导入架构”。 请注意，应用程序在源文件中检测到两个列。 在此处导入架构是为了能够将列从源数据存储映射到接收器数据存储。 如果不需映射列，可以跳过此步骤。 在本教程中，请导入架构。

    ![检测源架构](./media/tutorial-copy-data-portal/detect-source-schema.png)  
19. 现在，请切换到**包含管道的选项卡**，或者单击左侧**树状视图**中的管道。  

    ![管道选项卡](./media/tutorial-copy-data-portal/pipeline-tab.png)
20. 对于属性窗口中的“源数据集”字段，确认已选中 **SourceBlobDataset**。 可以单击“预览数据”，在此页上预览数据。 
    
    ![源数据集](./media/tutorial-copy-data-portal/source-dataset-selected.png)
21. 切换到“接收器”选项卡，单击“新建”创建一个接收器数据集。 

    ![“新建接收器数据集”菜单](./media/tutorial-copy-data-portal/new-sink-dataset-button.png)
22. 在“新建数据集”窗口中，选择“Azure SQL 数据集”，然后单击“完成”。 在本教程中，是将数据复制到 Azure SQL 数据库。 

    ![选择 Azure SQL 数据库](./media/tutorial-copy-data-portal/select-azure-sql-database.png)
23. 在属性窗口的“常规”选项卡中，将名称设置为 **OutputSqlDataset**。 
    
    ![输出数据集名称](./media/tutorial-copy-data-portal/output-dataset-name.png)
24. 切换到“连接”选项卡，然后针对“链接服务”单击“新建”。 数据集必须与链接服务相关联。 该链接服务包含的连接字符串可供数据工厂服务用于在运行时连接到 Azure SQL 数据库。 数据集指定可将数据复制到其中的容器、文件夹和文件（可选）。 
    
    ![“新建链接服务”按钮](./media/tutorial-copy-data-portal/new-azure-sql-database-linked-service-button.png)       
25. 在“新建链接服务”窗口中，执行以下步骤： 

    1. 对于“名称”字段，请输入 **AzureSqlDatabaseLinkedService**。 
    2. 对于“服务器名称”字段，请选择 Azure SQL Server。
    4. 对于“数据库名称”字段，请选择 Azure SQL 数据库。 
    5. 对于“用户名”字段，请输入用户的名称。 
    6. 对于“密码”字段，请输入用户的密码。 
    7. 单击“测试连接”以测试连接。
    8. 单击“保存”保存链接服务。 
    
        ![新建 Azure SQL 数据库链接服务](./media/tutorial-copy-data-portal/new-azure-sql-linked-service-window.png)

26. 对于“表”，请选择 **[dbo].[emp]**。 

    ![选择 emp 表](./media/tutorial-copy-data-portal/select-emp-table.png)
27. 切换到“架构”选项卡，然后单击“导入架构”。 

    ![导入目标架构](./media/tutorial-copy-data-portal/import-destination-schema.png)
28. 选择“ID”列，然后单击“删除”。 ID 列是 SQL 数据库中的标识列，因此复制活动不需将数据插入到此列中。

    ![删除 ID 列](./media/tutorial-copy-data-portal/delete-id-column.png)
30. 切换到包含**管道**的选项卡。对于“接收器数据集”，请确认已选中 **OutputSqlDataset**。

    ![管道选项卡](./media/tutorial-copy-data-portal/pipeline-tab-2.png)        
31. 在底部的属性窗口中切换到“映射”选项卡，然后单击“导入架构”。 请注意，源文件中的第一和第二列映射到 SQL 数据库中的“FirstName”和“LastName”字段。

    ![映射架构](./media/tutorial-copy-data-portal/map-schemas.png)
33. 单击“验证”按钮，对管道进行验证。 单击**右箭头**，关闭验证窗口。

    ![管道验证输出](./media/tutorial-copy-data-portal/pipeline-validation-output.png)   
34. 单击右角的“代码”按钮。 此时会看到与管道相关联的 JSON 代码。 

    ![“代码”按钮](./media/tutorial-copy-data-portal/code-button.png)
35. 此时会看到类似于以下代码片段的 JSON 代码：  

    ```json
    {
        "name": "CopyPipeline",
        "properties": {
            "activities": [
                {
                    "name": "CopyFromBlobToSql",
                    "type": "Copy",
                    "dependsOn": [],
                    "policy": {
                        "timeout": "7.00:00:00",
                        "retry": 0,
                        "retryIntervalInSeconds": 20
                    },
                    "typeProperties": {
                        "source": {
                            "type": "BlobSource",
                            "recursive": true
                        },
                        "sink": {
                            "type": "SqlSink",
                            "writeBatchSize": 10000
                        },
                        "enableStaging": false,
                        "parallelCopies": 0,
                        "cloudDataMovementUnits": 0,
                        "translator": {
                            "type": "TabularTranslator",
                            "columnMappings": "Prop_0: FirstName, Prop_1: LastName"
                        }
                    },
                    "inputs": [
                        {
                            "referenceName": "SourceBlobDataset",
                            "type": "DatasetReference",
                            "parameters": {}
                        }
                    ],
                    "outputs": [
                        {
                            "referenceName": "OutputSqlDataset",
                            "type": "DatasetReference",
                            "parameters": {}
                        }
                    ]
                }
            ]
        }
    }
    ```

## <a name="test-run-the-pipeline"></a>测试性运行管道
可以先对管道进行测试性运行，然后再将项目（链接服务、数据集和管道）发布到数据工厂或自己的 VSTS GIT 存储库。 

1. 若要测试性运行管道，请单击工具栏中的“测试性运行”。 可以在底部窗口的“输出”选项卡中看到管道运行的状态。 

    ![“测试性运行”按钮](./media/tutorial-copy-data-portal/test-run-output.png)
2. 验证是否已将源文件中的数据插入到目标 SQL 数据库中。 

    ![验证 SQL 输出](./media/tutorial-copy-data-portal/verify-sql-output.png)
3. 单击左窗格中的“全部发布”。 此操作将所创建的实体（链接服务、数据集和管道）发布到 Azure 数据工厂。

    ![发布按钮](./media/tutorial-copy-data-portal/publish-button.png)
4. 等待“已成功发布”消息出现。 若要查看通知消息，请单击左侧边栏中的“显示通知”选项卡。 单击“X”关闭通知窗口。

    ![显示通知](./media/tutorial-copy-data-portal/show-notifications.png)

## <a name="configure-code-repository"></a>配置代码存储库
可以将与数据工厂项目相关联的代码发布到 Visual Studio Team System (VSTS) 代码存储库。 此步骤创建代码存储库。 

如果不希望使用 VSTS 代码存储库，则可跳过此步骤，继续像前一步骤所做的那样发布到数据工厂。 

1. 单击左角的“数据工厂”或其旁边的向下箭头，然后单击“配置代码存储库”。 

    ![“代码”按钮](./media/tutorial-copy-data-portal/configure-code-repository-button.png)
2. 在“存储库设置”页中执行以下步骤： 
    1. 对于“存储库类型”字段，选择“Visual Studio Team Services Git”。
    2. 对于“Visual Studio Team Services 帐户”字段，选择 VSTS 帐户。
    3. 对于“项目名称”字段，选择 VSTS 帐户中的一个项目。
    4. 输入 **Tutorial2** 作为要与数据工厂相关联的 **Git 存储库的名称**。 
    5. 确认选中“将现有的数据工厂资源导入存储库”选项。 
    6. 单击“保存”保存这些设置。 

        ![存储库设置](./media/tutorial-copy-data-portal/repository-settings.png)
3. 确认选择 **VSTS GIT** 作为存储库。

    ![选定的 VSTS GIT](./media/tutorial-copy-data-portal/vsts-git-selected.png)
4. 在 Web 浏览器的独立选项卡中，导航到 **Tutorial2** 存储库，此时会看到两个分库：**master** 和 **adf_publish**。

    ![Master 和 adf_publish 分库](./media/tutorial-copy-data-portal/initial-branches-vsts-git.png)
5. 验证数据工厂实体的 **JSON 文件**是否存在于 **master** 分库中。

    ![master 分库中的文件](./media/tutorial-copy-data-portal/master-branch-files.png)
6. 验证 **JSON 文件**是否尚不在 **adf_publish** 分库中。 

    ![adf_publish 分库中的文件](./media/tutorial-copy-data-portal/adf-publish-files.png)
7. 添加**管道**的**说明**，然后单击工具栏中的“保存”按钮。 

    ![添加管道的说明](./media/tutorial-copy-data-portal/pipeline-description.png)
8. 此时会看到一个**分库**，你的用户名在 **Tutorial2** 存储库中。 所做的更改在自己的分库中，不在 master 分库中。 只能从 master 分库发布实体。

    ![你的分库](./media/tutorial-copy-data-portal/your-branch.png)
9. 将鼠标移到“同步”按钮上方（此时请勿单击），选择“提交更改”选项，然后单击“同步”按钮，将所做的更改与 **master** 分库同步。 

    ![提交并同步所做的更改](./media/tutorial-copy-data-portal/commit-and-sync.png)
9. 在“同步更改”窗口中，执行以下操作： 

    1. 确认 **CopyPipeline** 显示在更新的管道列表中。
    2. 确认“在同步后发布更改”已选中。 如果取消选中此选项，则只将你分库中的更改与 master 分库同步，而不将其发布到数据工厂服务。 可以稍后使用“发布”按钮发布这些更改。 如果选中此选项，则会先将所做的更改同步到 master 分库，然后将其发布到数据工厂服务。
    3. 单击“同步”。 

    ![“同步更改”窗口](./media/tutorial-copy-data-portal/sync-your-changes.png)
10. 现在可以在 **Tutorial2** 存储库的 **adf_publish** 分库中看到文件。 也可在此分库中找到数据工厂解决方案的 Azure 资源管理器模板。  

    ![adf_publish 分库中的文件](./media/tutorial-copy-data-portal/adf-publish-files-after-publish.png)


## <a name="trigger-the-pipeline-manually"></a>手动触发管道
在此步骤中，请手动触发在前面的步骤中发布的管道。 

1. 单击工具栏中的“触发器”，然后单击“立即触发”。 在“管道运行”页上单击“完成”。  

    ![“立即触发”菜单](./media/tutorial-copy-data-portal/trigger-now-menu.png)
2. 在左侧切换到“监视”选项卡。 此时会看到由手动触发器触发的管道运行。 可以使用“操作”列中的链接来查看活动详细信息以及重新运行该管道。

    ![监视管道运行](./media/tutorial-copy-data-portal/monitor-pipeline.png)
3. 若要查看与管道运行相关联的活动运行，请单击“操作”列中的“查看活动运行”链接。 此示例中只有一个活动，因此列表中只看到一个条目。 有关复制操作的详细信息，请单击“操作”列中的“详细信息”链接（眼镜图标）。 若要切换回“管道运行”视图，可单击顶部的“管道”。 若要刷新视图，请单击“刷新”。

    ![查看活动运行](./media/tutorial-copy-data-portal/view-activity-runs.png)
4. 验证是否又向 Azure SQL 数据库的 **emp** 表添加了两个行。 

## <a name="trigger-the-pipeline-on-a-schedule"></a>按计划触发管道
在此计划中，请为管道创建计划程序触发器。 触发器按指定的计划（每小时、每天等）运行管道。 在此示例中，请将触发器设置为每分钟运行一次，直至指定的结束日期/时间。 

1. 在左侧切换到“编辑”选项卡。 

    ![“编辑”选项卡](./media/tutorial-copy-data-portal/edit-tab.png)
2. 单击“触发器”，然后选择“新建/编辑”。 如果管道不是处于活动状态，请切换到该管道。 

    ![触发器的“新建/编辑”菜单](./media/tutorial-copy-data-portal/trigger-new-edit-menu.png)
3. 在“添加触发器”窗口中单击“选择触发器...”，然后单击“+ 新建”。 

    ![添加触发器 - 新建触发器](./media/tutorial-copy-data-portal/add-trigger-new-button.png)
4. 在“新建触发器”窗口中，执行以下步骤： 

    1. 将“名称”设置为 **RunEveryMinute**。
    2. 对于“结束”字段，请选择“在特定日期”。 
    3. 单击“在特定日期”所对应的下拉列表。
    4. 选择“当天”。 默认情况下，结束日期设置为第二天。 
    5. 更新“分钟”部分，使之超过当前的日期/时间数分钟。 触发器只会在发布所做的更改后激活。 因此，如果将其设置为仅数分钟后激活，而到时又不进行发布，则看不到触发器运行。  
    6. 单击“应用” 。 

        ![设置触发器属性](./media/tutorial-copy-data-portal/set-trigger-properties.png)
    7. 勾选“已激活”选项。 可以在以后使用此复选框停用它和激活它。
    8. 单击“资源组名称” 的 Azure 数据工厂。

        ![激活的触发器 - 下一步](./media/tutorial-copy-data-portal/trigger-activiated-next.png)

    > [!IMPORTANT]
    > 每次管道运行都需要付出相关成本。 因此，请正确设置结束日期。 
6. 在“触发器运行参数”页中查看以下警告，然后单击“完成”。 此示例中的管道不采用任何参数。 

    ![管道参数](./media/tutorial-copy-data-portal/trigger-pipeline-parameters.png)
7. 单击“同步”，将你的分库中的更改与 master 分库同步。 默认选择“在同步后发布更改”。 因此，在选择“同步”时，它也将更新的条目从 master 分库发布到 Azure 数据工厂服务。 在发布成功之前，触发器不会实际激活。

    ![发布触发器](./media/tutorial-copy-data-portal/sync-your-changes-with-trigger.png) 
9. 切换到左侧的“监视”选项卡，查看触发的管道运行。 

    ![触发的管道运行](./media/tutorial-copy-data-portal/triggered-pipeline-runs.png)    
9. 若要从管道运行视图切换到触发器运行视图，请单击“管道运行”，然后选择“触发器运行”。
    
    ![触发器运行菜单](./media/tutorial-copy-data-portal/trigger-runs-menu.png)
10. 可以在列表中看到触发器运行。 

    ![触发器运行列表](./media/tutorial-copy-data-portal/trigger-runs-list.png)
11. 验证是否每分钟将两个行（对于每个管道运行）插入 **emp** 表中，直至指定的结束时间。 

## <a name="next-steps"></a>后续步骤
此示例中的管道将数据从 Azure Blob 存储中的一个位置复制到另一个位置。 你已了解如何： 

> [!div class="checklist"]
> * 创建数据工厂。
> * 创建包含“复制”活动的管道。
> * 测试性运行管道
> * 手动触发管道
> * 按计划触发管道
> * 监视管道和活动运行。


若要了解如何将数据从本地复制到云，请转到以下教程： 

> [!div class="nextstepaction"]
>[将数据从本地复制到云](tutorial-hybrid-copy-portal.md)
