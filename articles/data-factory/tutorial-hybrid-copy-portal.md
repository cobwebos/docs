---
title: 使用 Azure 数据工厂将数据从 SQL Server 复制到 BLob 存储 | Microsoft Docs
description: 了解如何在 Azure 数据工厂中使用自承载 Integration Runtime 将数据从本地数据存储复制到云。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/11/2018
ms.author: jingwang
ms.openlocfilehash: 27e7d6f22678bf33ffd81fb34472fe4add3f9a15
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37045454"
---
# <a name="copy-data-from-an-on-premises-sql-server-database-to-azure-blob-storage"></a>将数据从本地 SQL Server 数据库复制到 Azure Blob 存储
本教程使用 Azure 数据工厂用户界面 (UI) 创建一个数据工厂管道，用于将数据从本地 SQL Server 数据库复制到 Azure Blob 存储。 同时创建一个自承载 Integration Runtime，用其在本地数据存储和云数据存储之间移动数据。

> [!NOTE]
> 本文不提供数据工厂的详细介绍。 有关详细信息，请参阅[数据工厂简介](introduction.md)。 

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

若要查看自己在订阅中的权限，请转到 Azure 门户。 在右上角选择自己的用户名，然后选择“权限”。 如果可以访问多个订阅，请选择相应的订阅。 有关如何将用户添加到角色的示例说明，请参阅[添加角色](../billing/billing-add-change-azure-subscription-administrator.md)。

### <a name="sql-server-2014-2016-and-2017"></a>SQL Server 2014、2016 和 2017
在本教程中，请将本地 SQL Server 数据库用作源数据存储。 首先，在本教程中创建的数据工厂中的管道将数据从这个本地 SQL Server 数据库（源）复制到 Blob 存储（接收器）。 然后，你可以在 SQL Server 数据库中创建名为 **emp** 的表，并向表中插入几个示例条目。 

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
在本教程中，请使用常规用途的 Azure 存储帐户（具体说来就是 Blob 存储）作为目标/接收器数据存储。 如果没有常规用途的 Azure 存储帐户，请参阅[创建存储帐户](../storage/common/storage-create-storage-account.md#create-a-storage-account)。 在本教程中创建的数据工厂中的管道将数据从本地 SQL Server 数据库（源）复制到 Blob 存储（接收器）。 

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

1. 在“存储帐户”窗口中转到“概览”，然后选择“Blob”。 

    ![选择“Blob”选项](media/tutorial-hybrid-copy-powershell/select-blobs.png)

2. 在“Blob 服务”窗口中，选择“容器”。 

    ![“容器”按钮](media/tutorial-hybrid-copy-powershell/add-container-button.png)

3. 在“新建容器”窗口的“名称”下，输入 **adftutorial**。 然后选择“确定”。 

    ![“新建容器”窗口](media/tutorial-hybrid-copy-powershell/new-container-dialog.png)

4. 在容器列表中选择“adftutorial”。

    ![容器选择](media/tutorial-hybrid-copy-powershell/seelct-adftutorial-container.png)

5. 让 **adftutorial** 的“容器”窗口保持打开状态。 在教程结束时可以使用它来验证输出。 数据工厂自动在该容器中创建输出文件夹，因此不需要你来创建。

    ![“容器”窗口](media/tutorial-hybrid-copy-powershell/container-page.png)


## <a name="create-a-data-factory"></a>创建数据工厂
在此步骤中，请先创建数据工厂，然后启动数据工厂 UI，在该数据工厂中创建一个管道。 

1. 打开 **Microsoft Edge** 或 **Google Chrome** Web 浏览器。 目前，仅 Microsoft Edge 和 Google Chrome Web 浏览器支持数据工厂 UI。
2. 在左侧菜单中，选择“新建” > “数据 + 分析” > “数据工厂”。
   
   ![新建数据工厂](./media/tutorial-hybrid-copy-portal/new-azure-data-factory-menu.png)
3. 在“新建数据工厂”页的“名称”下输入 **ADFTutorialDataFactory**。 
   
     ![“新建数据工厂”页](./media/tutorial-hybrid-copy-portal/new-azure-data-factory.png)

数据工厂的名称必须全局唯一。 如果看到名称字段的以下错误消息，请更改数据工厂的名称（例如，改为 yournameADFTutorialDataFactory）。 有关数据工厂项目的命名规则，请参阅[数据工厂命名规则](naming-rules.md)。

   ![新建数据工厂名称](./media/tutorial-hybrid-copy-portal/name-not-available-error.png)
4. 选择要在其中创建数据工厂的 Azure **订阅**。
5. 对于“资源组”，请执行以下步骤之一：
   
      - 选择“使用现有资源组”，并从下拉列表选择现有的资源组。

      - 选择“新建”，并输入资源组的名称。
        
    若要了解资源组，请参阅[使用资源组管理 Azure 资源](../azure-resource-manager/resource-group-overview.md)。
6. 在“版本”下选择“V2”。
7. 在“位置”下选择数据工厂的位置。 下拉列表中仅显示支持的位置。 数据工厂使用的数据存储（例如，Azure 存储和 SQL 数据库）和计算资源（例如，Azure HDInsight）可以位于其他区域。
8. 选择“固定到仪表板”。 
9. 选择**创建**。
10. 仪表板上会显示状态为“正在部署数据工厂”的以下磁贴：

    ![“部署数据工厂”磁贴](media/tutorial-hybrid-copy-portal/deploying-data-factory.png)
11. 创建完以后，会看到图中所示的“数据工厂”页：
   
    ![数据工厂主页](./media/tutorial-hybrid-copy-portal/data-factory-home-page.png)
12. 选择“创作和监视”磁贴，在单独的选项卡中启动数据工厂 UI。 


## <a name="create-a-pipeline"></a>创建管道

1. 在“开始使用”页中，选择“创建管道”。 系统会自动创建一个管道。 可以在树状视图中看到该管道，并且其编辑器已打开。 

   ![“开始使用”页](./media/tutorial-hybrid-copy-portal/get-started-page.png)

2. 在“属性”窗口底部的“常规”选项卡的“名称”中，输入 **SQLServerToBlobPipeline**。

   ![管道名称](./media/tutorial-hybrid-copy-portal/pipeline-name.png)

3. 在“活动”工具箱中，展开“DataFlow”。 将“复制”活动拖放到管道设计图面。 将活动的名称设置为 **CopySqlServerToAzureBlobActivity**。

   ![活动名称](./media/tutorial-hybrid-copy-portal/copy-activity-name.png)

4. 在“属性”窗口中转到“源”选项卡，然后选择“+ 新建”。

   ![“源”选项卡](./media/tutorial-hybrid-copy-portal/source-dataset-new-button.png)

5. 在“新建数据集”窗口中搜索“SQL Server”。 选择“SQL Server”，然后选择“完成”。 此时会显示标题为“SqlServerTable1”的新选项卡。 左侧的树状视图中也显示了“SqlServerTable1”数据集。 

   ![SQL Server 选择](./media/tutorial-hybrid-copy-portal/select-sql-server.png)

6. 在“属性”窗口底部的“常规”选项卡的“名称”中，输入 **SqlServerDataset**。

   ![源数据集名称](./media/tutorial-hybrid-copy-portal/source-dataset-name.png)

7. 转到“连接”选项卡，然后选择“+ 新建”。 执行此步骤来与源数据存储（SQL Server 数据库）建立连接。 

   ![连接到源数据集](./media/tutorial-hybrid-copy-portal/source-connection-new-button.png)

8. 在“新建链接服务”窗口中，添加 **SqlServerLinkedService** 作为**名称**。 在“通过集成运行时连接”下选择“新建”。 在本部分，请创建一个自承载 Integration Runtime，然后将其与安装了 SQL Server 数据库的本地计算机相关联。 自承载 Integration Runtime 是一个组件，用于将数据从计算机上的 SQL Server 数据库复制到 Blob 存储。 

   ![新建集成运行时](./media/tutorial-hybrid-copy-portal/new-integration-runtime-button.png)

9. 在“集成运行时安装”窗口中选择“专用网络”，然后选择“下一步”。 

   ![专用网络选择](./media/tutorial-hybrid-copy-portal/select-private-network.png)

10. 输入集成运行时的名称，然后选择“下一步”。

    ![集成运行时名称](./media/tutorial-hybrid-copy-portal/integration-runtime-name.png)

11. 在“选项 1: 快速安装”下选择“单击此处对此计算机启动快速安装”。 

    ![“快速安装”链接](./media/tutorial-hybrid-copy-portal/click-exress-setup.png)

12. 在“Integration Runtime (自承载)快速安装”窗口中，选择“关闭”。 

    ![Integration Runtime(自承载)快速安装](./media/tutorial-hybrid-copy-portal/integration-runtime-setup-successful.png)

13. 在“新建链接服务”窗口中，确保在“通过集成运行时连接”下选择了上面创建的**集成运行时**。 

    ![](./media/tutorial-hybrid-copy-portal/select-integration-runtime.png)

14. 在“新建链接服务”窗口中执行以下步骤：

    a. 在“名称”下输入 **SqlServerLinkedService**。

    b. 在“通过集成运行时进行连接”下，确认已显示前面创建的自承载 Integration Runtime。

    c. 在“服务器名称”下，输入 SQL Server 实例的名称。 

    d. 在“数据库名称”下，输入包含 **emp** 表的数据库的名称。

    e. 在“身份验证类型”下，选择数据工厂在连接到 SQL Server 数据库时会使用的相应身份验证类型。

    f. 在“用户名”和“密码”下，输入用户名和密码。 如需在用户帐户或服务器名称中使用反斜杠 (\\)，请在其前面加上转义字符 (\\)。 例如，使用 *mydomain\\\\myuser*。

    g. 选择“测试连接”。 执行此步骤是为了确认数据工厂是否可以使用已创建的自承载 Integration Runtime 连接到 SQL Server 数据库。

    h. 若要保存链接服务，请选择“完成”。

       

15. 此时应已返回到打开了源数据集的窗口。 在“属性”窗口的“连接”选项卡中执行以下步骤： 

    a. 在“链接服务”中，确认显示了“SqlServerLinkedService”。

    b. 在“表”中选择“[dbo].[emp]”。

    ![源数据集连接信息](./media/tutorial-hybrid-copy-portal/source-dataset-connection.png)

16. 转到包含 SQLServerToBlobPipeline 的选项卡，或在树状视图中选择“SQLServerToBlobPipeline”。 

    ![“管道”选项卡](./media/tutorial-hybrid-copy-portal/pipeliene-tab.png)

17. 转到“属性”窗口底部的“接收器”选项卡，选择“+ 新建”。 

    ![“接收器”选项卡](./media/tutorial-hybrid-copy-portal/sink-dataset-new-button.png)

18. 在“新建数据集”窗口中，选择“Azure Blob 存储”， 然后选择“完成”。 此时可以看到为数据集打开了一个新选项卡。 也可以在树状视图中看到此数据集。 

    ![Blob 存储选择](./media/tutorial-hybrid-copy-portal/select-azure-blob-storage.png)

19. 在“名称”中输入 **AzureBlobDataset**。

    ![接收器数据集名称](./media/tutorial-hybrid-copy-portal/sink-dataset-name.png)

20. 转到“属性”窗口底部的“连接”选项卡。 在“链接服务”旁边选择“+ 新建”。 

    ![“新建链接服务”按钮](./media/tutorial-hybrid-copy-portal/new-storage-linked-service-button.png)

21. 在“新建链接服务”窗口中执行以下步骤：

    a. 在“名称”下输入 **AzureStorageLinkedService**。

    b. 在“存储帐户名称”下选择自己的 Azure 存储帐户。

    c. 若要测试到存储帐户的连接，请选择“测试连接”。

    d. 选择“保存”。

    ![存储链接服务设置](./media/tutorial-hybrid-copy-portal/azure-storage-linked-service-settings.png) 

22. 此时应已返回到打开了接收器数据集的窗口。 在“连接”选项卡中执行以下步骤： 

       a. 在“链接服务”中，确认选择了“AzureStorageLinkedService”。

       b. 对于**文件路径**的**文件夹**/ **目录**部分，输入 **adftutorial/fromonprem**。 如果 adftutorial 容器中不包含 output 文件夹，数据工厂会自动创建 output 文件夹。

       c. 对于**文件路径**的**文件名**部分，选择“添加动态内容”。   

    ![动态文件名值](./media/tutorial-hybrid-copy-portal/file-name.png)

       d. 添加 `@CONCAT(pipeline().RunId, '.txt')`，选择“完成”。 这会将文件重命名为 PipelineRunID.txt。 

    ![用于解析文件名的动态表达式](./media/tutorial-hybrid-copy-portal/add-dynamic-file-name.png)

    ![连接到接收器数据集](./media/tutorial-hybrid-copy-portal/sink-dataset-connection.png)

23. 转到已打开该管道的选项卡，或者在树状视图中选择该管道。 在“接收器数据集”中，确认选择了“AzureBlobDataset”。 

    ![选择的接收器数据集](./media/tutorial-hybrid-copy-portal/sink-dataset-selected.png)

24. 若要验证管道设置，请在工具栏中选择该管道对应的“验证”。 若要关闭“管道验证报表”，请选择“关闭”。 

    ![验证管道](./media/tutorial-hybrid-copy-portal/validate-pipeline.png)

25. 若要将创建的实体发布到数据工厂，请选择“全部发布”。

    ![发布按钮](./media/tutorial-hybrid-copy-portal/publish-button.png)

26. 等到“发布成功”弹出消息出现。 若要查看发布状态，请选择左侧的“显示通知”链接。 若要关闭通知窗口，请选择“关闭”。 

    ![发布成功](./media/tutorial-hybrid-copy-portal/publishing-succeeded.png)


## <a name="trigger-a-pipeline-run"></a>触发管道运行
选择工具栏中对应于管道的“触发器”，然后选择“立即触发”。

![触发管道运行](./media/tutorial-hybrid-copy-portal/trigger-now.png)

## <a name="monitor-the-pipeline-run"></a>监视管道运行

1. 转到“监视”选项卡。此时会看到在上一步手动触发的管道。 

    ![监视管道运行](./media/tutorial-hybrid-copy-portal/pipeline-runs.png)
2. 若要查看与管道运行关联的活动运行，请选择“操作”列中的“查看活动运行”链接。 只能看到一个活动运行，因为该管道中只包含一个活动。 若要查看复制操作的详细信息，请选择“操作”列中的“详细信息”链接（眼镜图标）。 若要回到“管道运行”视图，请选择顶部的“管道”。

    ![监视活动运行](./media/tutorial-hybrid-copy-portal/activity-runs.png)

## <a name="verify-the-output"></a>验证输出
该管道自动在 `adftutorial` Blob 容器中创建名为 *fromonprem* 的输出文件夹。 确认在输出文件夹中看到了 *[pipeline().RunId].txt* 文件。 

![确认输出文件名](./media/tutorial-hybrid-copy-portal/sink-output.png)


## <a name="next-steps"></a>后续步骤
此示例中的管道将数据从 Blob 存储中的一个位置复制到另一个位置。 你已了解如何：

> [!div class="checklist"]
> * 创建数据工厂。
> * 创建自我托管的集成运行时。
> * 创建 SQL Server 和存储链接服务。 
> * 创建 SQL Server 和 Blob 存储数据集。
> * 创建包含复制活动的管道，用于移动数据。
> * 启动管道运行。
> * 监视管道运行。

有关数据工厂支持的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。

若要了解如何将数据从源批量复制到目标，请继续学习以下教程：

> [!div class="nextstepaction"]
>[批量复制数据](tutorial-bulk-copy-portal.md)
