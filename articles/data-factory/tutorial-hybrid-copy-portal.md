---
title: "使用 Azure 数据工厂将数据从 SQL Server 复制到 BLob 存储 | Microsoft Docs"
description: "了解如何在 Azure 数据工厂中使用自承载 Integration Runtime 将数据从本地数据存储复制到 Azure 云。"
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
ms.date: 01/11/2018
ms.author: jingwang
ms.openlocfilehash: 64cd758e2f40ff2b18abbff1194a7e57389d8a54
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2018
---
# <a name="tutorial-copy-data-from-an-on-premises-sql-server-database-to-azure-blob-storage"></a>教程：将数据从本地 SQL Server 数据库复制到 Azure Blob 存储
本教程使用 Azure 数据工厂用户界面 (UI) 创建一个数据工厂管道，用于将数据从本地 SQL Server 数据库复制到 Azure Blob 存储。 同时创建一个自承载 Integration Runtime，用其在本地数据存储和云数据存储之间移动数据。 

> [!NOTE]
> 本文适用于目前处于预览状态的 Azure 数据工厂第 2 版。 如果使用数据工厂服务版本 1（即正式版 (GA)），请参阅[数据工厂版本 1 文档](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。
> 
> 本文不提供数据工厂服务的详细介绍。 有关详细信息，请参阅 [Azure 数据工厂简介](introduction.md)。 

将在本教程中执行以下步骤：

> [!div class="checklist"]
> * 创建数据工厂。
> * 创建自我托管的集成运行时。
> * 创建 SQL Server 和 Azure 存储链接服务。 
> * 创建 SQL Server 和 Azure Blob 数据集。
> * 创建包含复制活动的管道，用于移动数据。
> * 启动管道运行。
> * 监视管道运行。

## <a name="prerequisites"></a>先决条件
### <a name="azure-subscription"></a>Azure 订阅
如果还没有 Azure 订阅，请在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

### <a name="azure-roles"></a>Azure 角色
若要创建数据工厂实例，用于登录到 Azure 的用户帐户必须具有参与者或所有者角色，或者是 Azure 订阅的管理员。 

若要查看你在订阅中拥有的权限，请转到 Azure 门户，选择右上角的用户名，然后选择“权限”。 如果可以访问多个订阅，请选择相应的订阅。 有关将用户添加到角色的示例说明，请参阅[添加角色](../billing/billing-add-change-azure-subscription-administrator.md)一文。

### <a name="sql-server-2014-2016-and-2017"></a>SQL Server 2014、2016 和 2017
在本教程中，请将本地 SQL Server 数据库用作源数据存储。 首先，在本教程中创建的数据工厂中的管道将数据从这个本地 SQL Server 数据库（源）复制到 Azure Blob 存储（接收器）。 然后，你可以在 SQL Server 数据库中创建名为 **emp** 的表，并向表中插入几个示例条目。 

1. 启动 SQL Server Management Studio。 如果此软件尚未安装在计算机上，请访问[下载 SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)。 

2. 使用凭据连接到 SQL Server 实例。 

3. 创建示例数据库。 在树状视图中右键单击“数据库”，然后选择“新建数据库”。 
 
4. 在“新建数据库”窗口中输入数据库的名称，然后选择“确定”。 

5. 若要创建 **emp** 表并将一些示例数据插入到其中，请对数据库运行以下查询脚本：

   ```
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

6. 在树状视图中右键单击所创建的数据库，然后选择“新建查询”。

### <a name="azure-storage-account"></a>Azure 存储帐户
在本教程中，请使用常规用途的 Azure 存储帐户（具体说来就是 Azure Blob 存储）作为目标/接收器数据存储。 如果没有常规用途的 Azure 存储帐户，请参阅[创建存储帐户](../storage/common/storage-create-storage-account.md#create-a-storage-account)。 在本教程中创建的数据工厂中的管道将数据从本地 SQL Server 数据库（源）复制到这个 Azure Blob 存储（接收器）。 

#### <a name="get-storage-account-name-and-account-key"></a>获取存储帐户名称和帐户密钥
在本教程中，请使用 Azure 存储帐户的名称和密钥。 通过以下步骤获取存储帐户的名称和密钥： 

1. 使用 Azure 用户名和密码登录到 [Azure 门户](https://portal.azure.com)。 

2. 在左窗格中选择“更多服务”，使用“存储”关键字进行筛选，然后选择“存储帐户”。

    ![搜索存储帐户](media/tutorial-hybrid-copy-powershell/search-storage-account.png)

3. 在存储帐户列表中，通过筛选找出你的存储帐户（如果需要），然后选择你的存储帐户。 

4. 在“存储帐户”窗口中选择“访问密钥”。

    ![获取存储帐户名称和密钥](media/tutorial-hybrid-copy-powershell/storage-account-name-key.png)

5. 复制“存储帐户名称”和“key1”框中的值，然后将其粘贴到记事本或其他编辑器中，在教程的后面部分使用。 

#### <a name="create-the-adftutorial-container"></a>创建 adftutorial 容器 
此部分在 Azure Blob 存储中创建名为 **adftutorial** 的 Blob 容器。 

1. 在“存储帐户”窗口中切换到“概览”，然后选择“Blob”。 

    ![选择“Blob”选项](media/tutorial-hybrid-copy-powershell/select-blobs.png)

2. 在“Blob 服务”窗口中，选择“容器”。 

    ![“添加容器”按钮](media/tutorial-hybrid-copy-powershell/add-container-button.png)

3. 在“新建容器”窗口的“名称”框中输入“adftutorial”，然后选择“确定”。 

    ![输入容器名称](media/tutorial-hybrid-copy-powershell/new-container-dialog.png)

4. 在容器列表中选择“adftutorial”。  

    ![选择容器](media/tutorial-hybrid-copy-powershell/seelct-adftutorial-container.png)

5. 让 **adftutorial** 的“容器”窗口保持打开状态。 在教程结束时可以使用它来验证输出。 数据工厂自动在该容器中创建输出文件夹，因此不需要你来创建。

    ![“容器”窗口](media/tutorial-hybrid-copy-powershell/container-page.png)


## <a name="create-a-data-factory"></a>创建数据工厂
在此步骤中，请先创建数据工厂，然后启动 Azure 数据工厂 UI，在该数据工厂中创建一个管道。 

1. 在左侧菜单中单击“新建”，并依次单击“数据 + 分析”、“数据工厂”。 
   
   ![新建 -> DataFactory](./media/tutorial-hybrid-copy-portal/new-azure-data-factory-menu.png)
2. 在“新建数据工厂”页中，输入 **ADFTutorialDataFactory** 作为**名称**。 
      
     ![“新建数据工厂”页](./media/tutorial-hybrid-copy-portal/new-azure-data-factory.png)
 
   Azure 数据工厂的名称必须 **全局唯一**。 如果看到名称字段的以下错误，请更改数据工厂的名称（例如，改为 yournameADFTutorialDataFactory）。 有关数据工厂项目命名规则，请参阅[数据工厂 - 命名规则](naming-rules.md)一文。
  
     ![“新建数据工厂”页](./media/tutorial-hybrid-copy-portal/name-not-available-error.png)
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

    ![“正在部署数据工厂”磁贴](media/tutorial-hybrid-copy-portal/deploying-data-factory.png)
9. 创建完成后，会显示图中所示的“数据工厂”页。
   
   ![数据工厂主页](./media/tutorial-hybrid-copy-portal/data-factory-home-page.png)
10. 单击“创作和监视”磁贴，在单独的选项卡中启动 Azure 数据工厂 UI。 


## <a name="create-a-pipeline"></a>创建管道

1. 在“入门”页中，单击“创建管道”。 系统会自动创建一个管道。 可以在树状视图中看到该管道，并且其编辑器已打开。 

   ![“入门”页](./media/tutorial-hybrid-copy-portal/get-started-page.png)
2. 在底部“属性”窗口的“常规”选项卡中，输入 **SQLServerToBlobPipeline** 作为**名称**。

   ![管道名称](./media/tutorial-hybrid-copy-portal/pipeline-name.png)
2. 在“活动”工具箱中展开“数据流”，将“复制”活动拖放到管道设计图面。 将活动的名称设置为 **CopySqlServerToAzureBlobActivity**。

   ![活动名称](./media/tutorial-hybrid-copy-portal/copy-activity-name.png)
3. 在“属性”窗口中切换到“源”选项卡，然后单击“+ 新建”。

   ![新建源数据集 - 按钮](./media/tutorial-hybrid-copy-portal/source-dataset-new-button.png)
4. 在“新建数据集”窗口中搜索 **SQL Server**，选择“SQL Server”，然后单击“完成”。 此时会显示标题为“SqlServerTable1”的新选项卡。 左侧的树状视图中也显示了“SqlServerTable1”。 

   ![选择 SQL Server](./media/tutorial-hybrid-copy-portal/select-sql-server.png)
5. 在“属性”窗口的“常规”选项卡中，输入 **SqlServerDataset** 作为**名称**。
    
   ![源数据集 - 名称](./media/tutorial-hybrid-copy-portal/source-dataset-name.png)
6. 切换到“连接”选项卡，然后单击“+ 新建”。 执行此步骤来与源数据存储（SQL Server 数据库）建立连接。 

   ![源数据集 - 新建连接按钮](./media/tutorial-hybrid-copy-portal/source-connection-new-button.png)
7. 在“新建链接服务”窗口中，单击“新建集成运行时”。 在本部分，请创建一个自承载 Integration Runtime，然后将其与安装了 SQL Server 数据库的本地计算机相关联。 自承载 Integration Runtime 是一个组件，用于将数据从计算机上的 SQL Server 数据库复制到 Azure Blob 存储。 

   ![新建集成运行时](./media/tutorial-hybrid-copy-portal/new-integration-runtime-button.png)
8. 在“集成运行时安装”窗口中选择“专用网络”，然后单击“下一步”。 

   ![选择专用网络](./media/tutorial-hybrid-copy-portal/select-private-network.png)
9. 输入集成运行时的名称，然后单击“下一步”。  
    
   ![集成运行时 - 名称](./media/tutorial-hybrid-copy-portal/integration-runtime-name.png)
10. 在“选项 1: 快速安装”部分单击“单击此处对此计算机启动快速安装”。 

   ![单击“快速安装”链接](./media/tutorial-hybrid-copy-portal/click-exress-setup.png)
11. 在“集成运行时(自承载)快速安装”窗口中，单击“关闭”。 

   ![集成运行时安装 - 成功](./media/tutorial-hybrid-copy-portal/integration-runtime-setup-successful.png)
12. 在 Web 浏览器中的“集成运行时安装”窗口中，单击“完成”。 

   ![集成运行时安装 -完成](./media/tutorial-hybrid-copy-portal/click-finish-integration-runtime-setup.png)
13. 在“新建链接服务”窗口中执行以下步骤：

    1. 输入 **SqlServerLinkedService** 作为**名称**。
    2. 确认“通过集成运行时连接”中已显示前面创建的自承载集成运行时。
    3. 在“服务器名称”中指定 SQL 服务器的名称。 
    4. 在“数据库名称”字段中指定包含 **emp** 表的数据库的名称。 
    5. 选择数据工厂在连接到 SQL Server 数据库时要使用的相应**身份验证类型**。 
    6. 输入**用户名**和**密码**。 如需在用户帐户或服务器名称中使用反斜杠 (\\)，请在其前面加上转义字符 (\\)。 例如，使用 *mydomain\\\\myuser*。 
    7. 单击“测试连接”。 执行此步骤以确认数据工厂服务是否可以使用创建的自承载集成运行时连接到 SQL Server 数据库。 
    8. 若要保存链接服务，请单击“保存”。

       ![SQL Server 链接服务 - 设置](./media/tutorial-hybrid-copy-portal/sql-server-linked-service-settings.png)
14. 此时应已返回到打开了源数据集的窗口。 在“属性”窗口的“连接”中执行以下步骤： 

    1. 确认“链接服务”中显示了“SqlServerLinkedService”。 
    2. 对于“表”，请选择“[dbo].[emp]”。

        ![源数据集 - 连接信息](./media/tutorial-hybrid-copy-portal/source-dataset-connection.png)
15. 切换到包含 SQLServerToBlobPipeline 的选项卡，或在树状视图中单击“SQLServerToBlobPipeline”。 

    ![“管道”选项卡](./media/tutorial-hybrid-copy-portal/pipeliene-tab.png)
16. 在“属性”窗口中切换到“接收器”选项卡，然后单击“+ 新建”。 

    ![接收器数据集 -“新建”按钮](./media/tutorial-hybrid-copy-portal/sink-dataset-new-button.png)
17. 在“新建数据集”窗口中，选择“Azure Blob 存储”，然后单击“完成”。 此时可以看到为数据集打开了一个新选项卡。 也可以在树状视图中看到此数据集。 

    ![选择“Azure Blob 存储”](./media/tutorial-hybrid-copy-portal/select-azure-blob-storage.png)
18. 输入 **AzureBlobDataset** 作为**名称**。

    ![接收器数据集 - 名称](./media/tutorial-hybrid-copy-portal/sink-dataset-name.png)
19. 在“属性”窗口中切换到“连接”选项卡，然后针对“链接服务”单击“+ 新建”。 

    ![新建链接服务 - 按钮](./media/tutorial-hybrid-copy-portal/new-storage-linked-service-button.png)
20. 在“新建链接服务”窗口中执行以下步骤：

    1. 输入 **AzureStorageLinkedService** 作为**名称**。
    2. 对于“存储帐户名称”，请选择自己的 Azure 存储帐户。 
    3. 单击“测试连接”，测试与 Azure 存储帐户的连接。
    4. 单击“ **保存**”。

        ![Azure 存储链接服务 - 设置](./media/tutorial-hybrid-copy-portal/azure-storage-linked-service-settings.png) 
21.  此时应已返回到打开了接收器数据集的窗口。 在“连接”选项卡中执行以下步骤： 

        1. 确认为“链接服务”选择了“AzureStorageLinkedService”。
        2. 输入 **adftutorial/fromonprem** 作为“文件路径”的**文件夹**部分。 如果 adftutorial 容器中不包含 output 文件夹，数据工厂服务会自动创建 output 文件夹。
        3. 输入 `@CONCAT(pipeline().RunId, '.txt')` 作为“文件路径”的**文件名**部分。

            ![接收器数据集 - 连接](./media/tutorial-hybrid-copy-portal/sink-dataset-connection.png)
22. 切换到已打开该管道的选项卡，或者在**树状视图**中单击该**管道**。 确认为“接收器数据集”选择了“AzureBlobDataset”。 

    ![选择的接收器数据集 ](./media/tutorial-hybrid-copy-portal/sink-dataset-selected.png)
23. 若要验证管道设置，请在工具栏中单击该管道对应的“验证”。 单击右上角的“X”关闭“管道验证报告”。 

    ![验证管道](./media/tutorial-hybrid-copy-portal/validate-pipeline.png)
1. 单击“发布”，将创建的实体发布到 Azure 数据工厂服务。

    ![发布按钮](./media/tutorial-hybrid-copy-portal/publish-button.png)
24. 等到“发布成功”弹出消息出现。 也可以通过单击左侧的“显示通知”链接来检查发布状态。 单击“X”关闭通知窗口。 

    ![发布成功](./media/tutorial-hybrid-copy-portal/publishing-succeeded.png)
    

## <a name="trigger-a-pipeline-run"></a>触发管道运行
在工具栏中单击管道对应的“触发器”，然后单击“立即触发”。

![立即触发](./media/tutorial-hybrid-copy-portal/trigger-now.png)

## <a name="monitor-the-pipeline-run"></a>监视管道运行

1. 切换到“监视”选项卡。此时会看到在上一步骤中手动触发的管道。 

    ![管道运行](./media/tutorial-hybrid-copy-portal/pipeline-runs.png)
2. 若要查看与管道运行相关联的活动运行，请单击“操作”列中的“查看活动运行”链接。 只能看到一个活动运行，因为该管道中只包含一个活动。 若要查看有关复制操作的详细信息，请单击“操作”列中的“详细信息”链接（眼镜图标）。 单击顶部的“管道”可以切换回到管道运行视图。

    ![活动运行](./media/tutorial-hybrid-copy-portal/activity-runs.png)

## <a name="verify-the-output"></a>验证输出
该管道自动在 `adftutorial` Blob 容器中创建名为 *fromonprem* 的输出文件夹。 确认在输出文件夹中看到 *dbo.emp.txt* 文件。 

1. 在 Azure 门户的“adftutorial”容器窗口中选择“刷新”，查看输出文件夹。

    ![创建的输出文件夹](media/tutorial-hybrid-copy-portal/fromonprem-folder.png)
2. 选择文件夹列表中的 `fromonprem`。 
3. 确认可以看到名为 `dbo.emp.txt` 的文件。

    ![输出文件](media/tutorial-hybrid-copy-portal/fromonprem-file.png)


## <a name="next-steps"></a>后续步骤
此示例中的管道将数据从 Azure Blob 存储中的一个位置复制到另一个位置。 你已了解如何：

> [!div class="checklist"]
> * 创建数据工厂。
> * 创建自我托管的集成运行时。
> * 创建 SQL Server 和 Azure 存储链接服务。 
> * 创建 SQL Server 和 Azure Blob 数据集。
> * 创建包含复制活动的管道，用于移动数据。
> * 启动管道运行。
> * 监视管道运行。

有关数据工厂支持的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。

若要了解如何将数据从源批量复制到目标，请继续学习以下教程：

> [!div class="nextstepaction"]
>[批量复制数据](tutorial-bulk-copy-portal.md)
