---
title: "使用 Azure 复制数据工具复制本地数据 | Microsoft Docs"
description: "创建一个 Azure 数据工厂，然后使用“复制数据”工具将数据从本地 SQL Server 数据库复制到 Azure Blob 存储。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.topic: hero-article
ms.date: 01/04/2018
ms.author: jingwang
ms.openlocfilehash: 77090d9a61945c9edc42cde7d647c75e91f54dd6
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2018
---
# <a name="copy-data-from-an-on-premises-sql-server-database-to-azure-blob-storage-by-using-the-copy-data-tool"></a>使用“复制数据”工具将数据从本地 SQL Server 数据库复制到 Azure Blob 存储
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [版本 1 - 正式版](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [版本 2 - 预览版](tutorial-hybrid-copy-data-tool.md)

在本教程中，我们将使用 Azure 门户创建数据工厂。 然后，使用“复制数据”工具创建一个管道，用于将数据从本地 SQL Server 数据库复制到 Azure Blob 存储。

> [!NOTE]
> - 如果对 Azure 数据工厂不熟悉，请参阅[数据工厂简介](introduction.md)。
>
> - 本文适用于目前处于预览状态的数据工厂版本 2。 如果使用公开发布的第 1 版数据工厂，请参阅[数据工厂第 1 版入门](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。

将在本教程中执行以下步骤：

> [!div class="checklist"]
> * 创建数据工厂。
> * 使用“复制数据”工具创建管道。
> * 监视管道和活动运行。

## <a name="prerequisites"></a>先决条件
### <a name="azure-subscription"></a>Azure 订阅
如果还没有 Azure 订阅，请在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

### <a name="azure-roles"></a>Azure 角色
若要创建数据工厂实例，用于登录到 Azure 的用户帐户必须具有参与者或所有者角色，或者是 Azure 订阅的管理员。 

若要查看自己在订阅中的权限，请转到 Azure 门户。 在右上角选择自己的用户名，然后选择“权限”。 如果可以访问多个订阅，请选择相应的订阅。 有关如何将用户添加到角色的示例说明，请参阅[添加角色](../billing/billing-add-change-azure-subscription-administrator.md)。

### <a name="sql-server-2014-2016-and-2017"></a>SQL Server 2014、2016 和 2017
在本教程中，请将本地 SQL Server 数据库用作源数据存储。 首先，在本教程中创建的数据工厂中的管道将数据从这个本地 SQL Server 数据库（源）复制到 Blob 存储（接收器）。 然后，你可以在 SQL Server 数据库中创建名为 **emp** 的表，并向表中插入几个示例条目。 

1. 启动 SQL Server Management Studio。 如果此软件尚未安装在计算机上，请访问[下载 SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)。 

2. 使用凭据连接到 SQL Server 实例。 

3. 创建示例数据库。 在树状视图中右键单击“数据库”，然后选择“新建数据库”。 
 
4. 在“新建数据库”窗口中输入数据库的名称，然后选择“确定”。 

5. 若要创建 **emp** 表并将一些示例数据插入到其中，请对数据库运行以下查询脚本。 在树状视图中右键单击所创建的数据库，然后选择“新建查询”。

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO
    
    INSERT INTO emp (FirstName, LastName) VALUES ('John', 'Doe')
    INSERT INTO emp (FirstName, LastName) VALUES ('Jane', 'Doe')
    GO
    ```

### <a name="azure-storage-account"></a>Azure 存储帐户
在本教程中，请使用常规用途的 Azure 存储帐户（具体说来就是 Blob 存储）作为目标/接收器数据存储。 如果没有常规用途的存储帐户，请参阅[创建存储帐户](../storage/common/storage-create-storage-account.md#create-a-storage-account)中的创建说明。 在本教程中创建的数据工厂中的管道将数据从本地 SQL Server 数据库（源）复制到此 Blob 存储（接收器）。 

#### <a name="get-the-storage-account-name-and-account-key"></a>获取存储帐户名称和帐户密钥
在本教程中，请使用存储帐户的名称和密钥。 若要获取存储帐户的名称和密钥，请执行以下步骤： 

1. 使用 Azure 用户名和密码登录到 [Azure 门户](https://portal.azure.com)。 

2. 在左窗格中，选择“**更多服务**”。 使用“存储”关键字进行筛选，然后选择“存储帐户”。

    ![存储帐户搜索](media/tutorial-hybrid-copy-powershell/search-storage-account.png)

3. 在存储帐户列表中，根据需要通过筛选找出你的存储帐户， 然后选择存储帐户。 

4. 在“存储帐户”窗口中选择“访问密钥”。

    ![访问密钥](media/tutorial-hybrid-copy-powershell/storage-account-name-key.png)

5. 复制“存储帐户名称”和“key1”框中的值，然后将其粘贴到记事本或其他编辑器中，在教程的后面部分使用。 

#### <a name="create-the-adftutorial-container"></a>创建 adftutorial 容器 
此部分在 Blob 存储中创建名为 **adftutorial** 的 Blob 容器。 

1. 在“存储帐户”窗口中切换到“概览”，然后选择“Blob”。 

    ![选择“Blob”选项](media/tutorial-hybrid-copy-powershell/select-blobs.png)

2. 在“Blob 服务”窗口中，选择“容器”。 

    ![“容器”按钮](media/tutorial-hybrid-copy-powershell/add-container-button.png)

3. 在“新建容器”窗口的“名称”框中输入“adftutorial”，然后选择“确定”。 

    ![新建容器](media/tutorial-hybrid-copy-powershell/new-container-dialog.png)

4. 在容器列表中选择“adftutorial”。

    ![容器选择](media/tutorial-hybrid-copy-powershell/seelct-adftutorial-container.png)

5. 让 **adftutorial** 的“容器”窗口保持打开状态。 在教程结束时可以使用它来验证输出。 数据工厂自动在该容器中创建输出文件夹，因此不需要你来创建。

    ![“容器”窗口](media/tutorial-hybrid-copy-powershell/container-page.png)


## <a name="create-a-data-factory"></a>创建数据工厂

1. 在左侧菜单中，选择“新建” > “数据 + 分析” > “数据工厂”。 
   
   ![新建数据工厂](./media/tutorial-hybrid-copy-data-tool/new-azure-data-factory-menu.png)
2. 在“新建数据工厂”页的“名称”下输入 **ADFTutorialDataFactory**。 
      
     ![新建数据工厂](./media/tutorial-hybrid-copy-data-tool/new-azure-data-factory.png)
 
   数据工厂的名称必须全局唯一。 如果看到名称字段的以下错误消息，请更改数据工厂的名称（例如，改为 yournameADFTutorialDataFactory）。 有关数据工厂项目的命名规则，请参阅[数据工厂命名规则](naming-rules.md)。
  
   ![新建数据工厂名称](./media/tutorial-hybrid-copy-data-tool/name-not-available-error.png)
3. 选择要在其中创建数据工厂的 Azure **订阅**。 
4. 对于“资源组”，请执行以下步骤之一：
     
      - 选择“使用现有资源组”，并从下拉列表选择现有的资源组。

      - 选择“新建”，并输入资源组的名称。 
         
      若要了解资源组，请参阅[使用资源组管理 Azure 资源](../azure-resource-manager/resource-group-overview.md)。
5. 在“版本”下选择“V2 (预览)”。
6. 在“位置”下选择数据工厂的位置。 下拉列表中仅显示支持的位置。 数据工厂使用的数据存储（例如，Azure 存储和 SQL 数据库）和计算资源（例如，Azure HDInsight）可以位于其他位置/区域。
7. 选择“固定到仪表板”。 
8. 选择“创建”。
9. 仪表板上会显示状态为“正在部署数据工厂”的以下磁贴：

    ![“部署数据工厂”磁贴](media/tutorial-hybrid-copy-data-tool/deploying-data-factory.png)
10. 创建完以后，会看到图中所示的“数据工厂”页：
   
    ![数据工厂主页](./media/tutorial-hybrid-copy-data-tool/data-factory-home-page.png)
11. 选择“创作和监视”，在单独的选项卡中启动数据工厂用户界面。 

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>使用“复制数据”工具创建管道

1. 在“开始使用”页中选择“复制数据”，启动“复制数据”工具。 

   ![“复制数据”工具磁贴](./media/tutorial-hybrid-copy-data-tool/copy-data-tool-tile.png)
2. 在“复制数据”工具的“属性”页的“任务名称”下，输入 **CopyFromOnPremSqlToAzureBlobPipeline**。 然后，选择“下一步”。 “复制数据”工具将使用在此字段中指定的名称创建一个管道。 
    
   ![任务名称](./media/tutorial-hybrid-copy-data-tool/properties-page.png)
3. 在“源数据存储”页中选择“SQL Server”，然后选择“下一步”。 可能需要向下滚动才能看到列表中的“SQL Server”。 

   ![SQL Server 选择](./media/tutorial-hybrid-copy-data-tool/select-source-data-store.png)
4. 在“连接名称”下输入 **SqlServerLinkedService**。 选择“创建集成运行时”链接。 必须创建自承载的 Integration Runtime，将其下载到计算机，然后将其注册到数据工厂。 自承载的 Integration Runtime 时可在本地环境与云之间复制数据。

   ![创建自承载的 Integration Runtime](./media/tutorial-hybrid-copy-data-tool/create-integration-runtime-link.png)
5. 在“创建集成运行时”对话框的“名称”下，输入 **TutorialIntegration Runtime**。 然后选择“创建”。 

   ![集成运行时名称](./media/tutorial-hybrid-copy-data-tool/create-integration-runtime-dialog.png)
6. 选择“在此计算机上启动快速安装”。 此操作在计算机上安装集成运行时，并将其注册到数据工厂。 或者，可以使用手动安装选项来下载安装文件、运行该文件，并使用密钥来注册集成运行时。 

    ![“在此计算机上启动快速安装”链接](./media/tutorial-hybrid-copy-data-tool/launch-express-setup-link.png)
7. 运行下载的应用程序。 窗口中会显示快速安装的状态。 

    ![快速安装状态](./media/tutorial-hybrid-copy-data-tool/express-setup-status.png)
8. 确认为“集成运行时”字段选择了“TutorialIntegrationRuntime”。

    ![选择的集成运行时](./media/tutorial-hybrid-copy-data-tool/integration-runtime-selected.png)
9. 在“指定本地 SQL Server 数据库”中执行以下步骤： 

    a. 在“连接名称”下输入 **OnPremSqlLinkedService**。

    b. 在“服务器名称”下，输入本地 SQL Server 实例的名称。

    c. 在“数据库名称”下输入本地数据库的名称。

    d.单击“下一步”。 在“身份验证类型”下选择适当的身份验证。

    e. 在“用户名”下输入有权访问本地 SQL Server 的用户名。

    f. 输入该用户的**密码**。 
10. 在“选择要从中复制数据的表或使用自定义查询”页中，从列表中选择“[dbo].[emp]”表，然后选择“下一步”。 

    ![emp 表选择](./media/tutorial-hybrid-copy-data-tool/select-emp-table.png)
11. 在“目标数据存储”页上，选择“Azure Blob 存储”，然后选择“下一步”。

    ![Blob 存储选择](./media/tutorial-hybrid-copy-data-tool/select-destination-data-store.png)
12. 在“指定 Azure Blob 存储帐户”页中执行以下步骤： 

    a. 在“连接名称”下输入 **AzureStorageLinkedService**。

    b. 在“存储帐户名称”下的下拉列表中选择自己的存储帐户。 

    c. 选择“下一步”。

    ![指定存储帐户](./media/tutorial-hybrid-copy-data-tool/specify-azure-blob-storage-account.png)
13. 在“选择输出文件或文件夹”页的“文件夹路径”下，输入 **adftutorial/fromonprem**。 在执行先决条件中的步骤时，你已创建了 **adftutorial** 容器。 如果 output 文件夹不存在，数据工厂会自动创建它。 也可以使用“浏览”按钮来浏览 Blob 存储及其容器/文件夹。 请注意，输出文件的名称默认设置为 **dbo.emp**。
        
    ![选择输出文件或文件夹](./media/tutorial-hybrid-copy-data-tool/choose-output-file-folder.png)
14. 在“文件格式设置”页中，选择“下一步”。 

    ![“文件格式设置”页](./media/tutorial-hybrid-copy-data-tool/file-format-settings-page.png)
15. 在“设置”页中，选择“下一步”。 

    ![“设置”页](./media/tutorial-hybrid-copy-data-tool/settings-page.png)
16. 在“摘要”页中，检查所有设置的值，然后选择“下一步”。 

    ![“摘要”页](./media/tutorial-hybrid-copy-data-tool/summary-page.png)
17. 在“部署”页中，选择“监视”可以监视创建的管道或任务。

    ![“部署”页](./media/tutorial-hybrid-copy-data-tool/deployment-page.png)
18. 在“监视”选项卡中，可以查看创建的管道的状态。 可以使用“操作”列中的链接查看与管道运行关联的活动运行，以及重新运行管道。 

    ![监视管道运行](./media/tutorial-hybrid-copy-data-tool/monitor-pipeline-runs.png)
19. 选择“操作”列中的“查看活动运行”链接可以查看与管道运行关联的活动运行。 若要查看复制操作的详细信息，请选择“操作”列中的“详细信息”链接（眼镜图标）。 若要切换回“管道运行”视图，请选择顶部的“管道”。

    ![监视活动运行](./media/tutorial-hybrid-copy-data-tool/monitor-activity-runs.png)
20. 确认可以在 **adftutorial** 容器的 **fromonprem** 文件夹中看到输出文件。 
 
    ![输出 Blob](./media/tutorial-hybrid-copy-data-tool/output-blob.png)
21. 选择左侧的“编辑”选项卡可以切换到编辑器模式。 可以使用编辑器来更新该工具创建的链接服务、数据集和管道。 选择“代码”可以查看与编辑器中打开的实体相关联的 JSON 代码。 有关如何在数据工厂 UI 中编辑这些实体的详细信息，请参阅[此教程的 Azure 门户版本](tutorial-copy-data-portal.md)。

    ![“编辑”选项卡](./media/tutorial-hybrid-copy-data-tool/edit-tab.png)


## <a name="next-steps"></a>后续步骤
此示例中的管道可将数据从本地 SQL Server 数据库复制到 Blob 存储。 你已了解如何： 

> [!div class="checklist"]
> * 创建数据工厂。
> * 使用“复制数据”工具创建管道。
> * 监视管道和活动运行。

有关数据工厂支持的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。

若要了解如何将数据从源批量复制到目标，请继续学习以下教程：

> [!div class="nextstepaction"]
>[批量复制数据](tutorial-bulk-copy-portal.md)
