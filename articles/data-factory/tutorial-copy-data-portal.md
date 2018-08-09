---
title: 使用 Azure 门户创建数据工厂管道 | Microsoft Docs
description: 本教程分步说明了如何使用 Azure 门户创建带管道的数据工厂。 该管道通过复制活动将数据从 Azure Blob 存储复制到 SQL 数据库。
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
ms.date: 06/21/2018
ms.author: jingwang
ms.openlocfilehash: d8e2d6f18ea1c66404ceed475d1522bd631961fb
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2018
ms.locfileid: "39444549"
---
# <a name="copy-data-from-azure-blob-storage-to-a-sql-database-by-using-azure-data-factory"></a>使用 Azure 数据工厂，将数据从 Azure Blob 存储复制到 SQL 数据库
在本教程中，请使用 Azure 数据工厂用户界面 (UI) 创建数据工厂。 此数据工厂中的管道将数据从 Azure Blob 存储复制到 SQL 数据库。 本教程中的配置模式适用于从基于文件的数据存储复制到关系数据存储。 如需可以用作源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

> [!NOTE]
> - 如果对数据工厂不熟悉，请参阅 [Azure 数据工厂简介](introduction.md)。

将在本教程中执行以下步骤：

> [!div class="checklist"]
> * 创建数据工厂。
> * 创建包含复制活动的管道。
> * 测试性运行管道。
> * 手动触发管道。
> * 按计划触发管道。
> * 监视管道和活动运行。

## <a name="prerequisites"></a>先决条件
* **Azure 订阅**。 如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。
* **Azure 存储帐户**。 可将 Blob 存储用作源数据存储。 如果没有存储帐户，请参阅[创建 Azure 存储帐户](../storage/common/storage-create-storage-account.md#create-a-storage-account)以获取创建步骤。
* **Azure SQL 数据库**。 将数据库用作接收器数据存储。 如果没有 SQL 数据库，请参阅[创建 SQL 数据库](../sql-database/sql-database-get-started-portal.md)，了解创建该数据库的步骤。

### <a name="create-a-blob-and-a-sql-table"></a>创建 blob 和 SQL 表

现在，请执行以下步骤来准备本教程所需的 Blob 存储和 SQL 数据库。

#### <a name="create-a-source-blob"></a>创建源 blob

1. 启动记事本。 复制以下文本并将其在磁盘上另存为 **emp.txt** 文件：

    ```
    John,Doe
    Jane,Doe
    ```

1. 在 Blob 存储中创建名为 **adftutorial** 的容器。 在该容器中创建名为 input 的文件夹。 然后，将 **emp.txt** 文件上传到 **input** 文件夹。 请使用 Azure 门户或工具（例如 [Azure 存储资源管理器](http://storageexplorer.com/)）执行这些任务。

#### <a name="create-a-sink-sql-table"></a>创建接收器 SQL 表

1. 使用以下 SQL 脚本在 SQL 数据库中创建 **dbo.emp** 表：

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

1. 允许 Azure 服务访问 SQL Server。 确保 SQL Server 的“允许访问 Azure 服务”处于“打开”状态，以便数据工厂可以将数据写入 SQL Server。 若要验证并启用此设置，请执行以下步骤：

    a. 在左侧选择“更多服务” > “SQL Server”。

    b. 选择服务器，然后选择“设置”下的“防火墙”。

    c. 在“防火墙设置”页中，选择“允许访问 Azure 服务”所对应的“打开”。

## <a name="create-a-data-factory"></a>创建数据工厂
在此步骤中，请先创建数据工厂，然后启动数据工厂 UI，在该数据工厂中创建一个管道。 

1. 打开 **Microsoft Edge** 或 **Google Chrome** Web 浏览器。 目前，仅 Microsoft Edge 和 Google Chrome Web 浏览器支持数据工厂 UI。
1. 在左侧菜单中，选择“新建” > “数据 + 分析” > “数据工厂”。 
  
   ![新建数据工厂](./media/tutorial-copy-data-portal/new-azure-data-factory-menu.png)
1. 在“新建数据工厂”页的“名称”下输入 **ADFTutorialDataFactory**。 
      
     ![新建数据工厂](./media/tutorial-copy-data-portal/new-azure-data-factory.png)
 
   Azure 数据工厂的名称必须 *全局唯一*。 如果看到名称字段的以下错误消息，请更改数据工厂的名称（例如，改为 yournameADFTutorialDataFactory）。 有关数据工厂项目的命名规则，请参阅[数据工厂命名规则](naming-rules.md)。
  
   ![错误消息](./media/tutorial-copy-data-portal/name-not-available-error.png)
1. 选择要在其中创建数据工厂的 Azure **订阅**。 
1. 对于“资源组”，请执行以下步骤之一：
     
    a. 选择“使用现有资源组”，并从下拉列表选择现有的资源组。

    b. 选择“新建”，并输入资源组的名称。 
         
    若要了解资源组，请参阅[使用资源组管理 Azure 资源](../azure-resource-manager/resource-group-overview.md)。 
1. 在“版本”下选择“V2”。
1. 在“位置”下选择数据工厂所在的位置。 下拉列表中仅显示支持的位置。 数据工厂使用的数据存储（例如，Azure 存储和 SQL 数据库）和计算资源（例如，Azure HDInsight）可以位于其他区域。
1. 选择“固定到仪表板”。 
1. 选择“创建”。 
1. 仪表板上会显示状态为“正在部署数据工厂”的以下磁贴： 

    ![“部署数据工厂”磁贴](media/tutorial-copy-data-portal/deploying-data-factory.png)
1. 创建完以后，会看到图中所示的“数据工厂”页：
   
    ![数据工厂主页](./media/tutorial-copy-data-portal/data-factory-home-page.png)
1. 选择“创作和监视”，在单独的选项卡中启动数据工厂 UI。

## <a name="create-a-pipeline"></a>创建管道
本步骤在数据工厂中创建包含复制活动的管道。 复制活动将数据从 Blob 存储复制到 SQL 数据库。 在[快速入门教程](quickstart-create-data-factory-portal.md)中，已通过以下步骤创建一个管道：

1. 创建链接服务。 
1. 创建输入和输出数据集。
1. 创建管道。

在本教程中，请首先创建管道， 然后在配置管道时根据需要创建链接服务和数据集。 

1. 在“开始使用”页中，选择“创建管道”。 

   ![创建管道](./media/tutorial-copy-data-portal/create-pipeline-tile.png)
1. 在管道的“常规”选项卡中，输入 **CopyPipeline** 作为管道的**名称**。

1. 在“活动”工具箱中，展开“数据流”类别，然后将“复制”活动从工具箱拖放到管道设计器图面。 指定 **CopyFromBlobToSql** 作为**名称**。

    ![复制活动](./media/tutorial-copy-data-portal/drag-drop-copy-activity.png)

### <a name="configure-source"></a>配置源

1. 转到“源”选项卡。选择“+ 新建”创建源数据集。 

1. 在“新建数据集”窗口中，选择“Azure Blob 存储”，然后选择“完成”。 源数据位于 Blob 存储中，因此选择“Azure Blob 存储”作为源数据集。 

    ![存储选择](./media/tutorial-copy-data-portal/select-azure-blob-dataset.png)

1. 此时可以看到为 blob 数据集打开了一个新选项卡。 在“属性”窗口底部的“常规”选项卡中，输入 **SourceBlobDataset** 作为**名称**。

    ![数据集名称](./media/tutorial-copy-data-portal/dataset-name.png)

1. 转到“属性”窗口的“连接”选项卡。 在“链接服务”文本框旁边，选择“+ 新建”。 

    ![“新建链接服务”按钮](./media/tutorial-copy-data-portal/source-dataset-new-linked-service-button.png)

1. 在“新建链接服务”窗口中，输入 **AzureStorageLinkedService** 作为名称，从“存储帐户名称”列表中选择你的存储帐户，然后选择“保存”来部署链接服务。

    ![新建链接服务](./media/tutorial-copy-data-portal/new-azure-storage-linked-service.png)

1. 在创建链接服务后，会返回到数据集设置。 在“文件路径”旁边，选择“浏览”。

    ![文件路径的“浏览”按钮](./media/tutorial-copy-data-portal/file-browse-button.png)

1. 导航到 **adftutorial/input** 文件夹，选择 **emp.txt** 文件，然后选择“完成”。 

    ![选择输入文件](./media/tutorial-copy-data-portal/select-input-file.png)

1. 确认“文件格式”已设置为“文本格式”，“列分隔符”已设置为“逗号(`,`)”。 对于“文件格式”，如果源文件使用不同的行分隔符和列分隔符，则可选择“检测文本格式”。 复制数据工具会自动检测文件格式和分隔符。 你仍然可以重写这些值。 若要预览此页上的数据，请选择“预览数据”。

    ![检测文本格式](./media/tutorial-copy-data-portal/detect-text-format.png)

1. 转到“属性”窗口的“架构”选项卡，选择“导入架构”。 请注意，应用程序在源文件中检测到两个列。 在此处导入架构是为了能够将列从源数据存储映射到接收器数据存储。 如果不需映射列，可以跳过此步骤。 在本教程中，请导入架构。

    ![检测源架构](./media/tutorial-copy-data-portal/detect-source-schema.png)  

1. 现在，返回到管道 ->“源”选项卡，确认已选择了 **SourceBlobDataset**。 若要预览此页上的数据，请选择“预览数据”。 
    
    ![源数据集](./media/tutorial-copy-data-portal/source-dataset-selected.png)

### <a name="configure-sink"></a>配置接收器

1. 转到“接收器”选项卡，选择“+ 新建”，创建一个接收器数据集。 

    ![接收器数据集](./media/tutorial-copy-data-portal/new-sink-dataset-button.png)
1. 在“新建数据集”窗口中，在搜索框中输入“SQL”来筛选连接器，选择“Azure SQL 数据库”，然后选择“完成”。 在本教程中，请将数据复制到 SQL 数据库。 

    ![SQL 数据库选择](./media/tutorial-copy-data-portal/select-azure-sql-dataset.png)
1. 在“属性”窗口的“常规”选项卡的“名称”中，输入 **OutputSqlDataset**。 
    
    ![输出数据集名称](./media/tutorial-copy-data-portal/output-dataset-name.png)
1. 转到“连接”选项卡，在“链接服务”旁边选择“+ 新建”。 数据集必须与链接服务相关联。 该链接服务包含的连接字符串可供数据工厂用于在运行时连接到 SQL 数据库。 数据集指定可将数据复制到其中的容器、文件夹和文件（可选）。 
    
    ![链接服务](./media/tutorial-copy-data-portal/new-azure-sql-database-linked-service-button.png)       
1. 在“新建链接服务”窗口中执行以下步骤： 

    a. 在“名称”下输入 **AzureSqlDatabaseLinkedService**。

    b. 在“服务器名称”下选择 SQL Server 实例。

    c. 在“数据库名称”下选择自己的 SQL 数据库。

    d. 在“用户名”下输入用户的名称。

    e. 在“密码”下输入用户的密码。

    f. 选择“测试连接”以测试连接。

    g. 选择“保存”保存链接服务。 
    
    ![保存新建链接服务](./media/tutorial-copy-data-portal/new-azure-sql-linked-service-window.png)

1. 在“表”中选择“[dbo].[emp]”。 

    ![表](./media/tutorial-copy-data-portal/select-emp-table.png)
1. 转到“架构”选项卡，选择“导入架构”。 

    ![选择“导入架构”](./media/tutorial-copy-data-portal/import-destination-schema.png)
1. 选择“ID”列，然后选择“删除”。 **ID** 列是 SQL 数据库中的标识列，因此复制活动不需将数据插入到此列中。

    ![删除 ID 列](./media/tutorial-copy-data-portal/delete-id-column.png)
1. 转到包含管道的选项卡。在“接收器数据集”中，确认已选中“OutputSqlDataset”。

    ![“管道”选项卡](./media/tutorial-copy-data-portal/pipeline-tab-2.png)        

### <a name="confugure-mapping"></a>配置映射

转到“属性”窗口底部的“映射”选项卡，选择“导入架构”。 请注意，源文件中的第一和第二列映射到 SQL 数据库中的“FirstName”和“LastName”。

![映射架构](./media/tutorial-copy-data-portal/map-schemas.png)

## <a name="validate-the-pipeline"></a>验证管道
若要验证管道，请从工具栏中选择“验证”。
 
可以通过单击右上角的“代码”来查看与管道关联的 JSON 代码。

## <a name="debug-and-publish-the-pipeline"></a>调试和发布管道
可以先调试管道，然后再将项目（链接服务、数据集和管道）发布到数据工厂或自己的 Visual Studio Team Services GIT 存储库。 

1. 若要调试管道，请在工具栏上选择“调试”。 可以在窗口底部的“输出”选项卡中看到管道运行的状态。 

1. 在管道可以成功运行后，在工具栏中选择“全部发布”。 此操作将所创建的实体（数据集和管道）发布到数据工厂。

    ![发布](./media/tutorial-copy-data-portal/publish-button.png)

1. 等待“已成功发布”消息出现。 若要查看通知消息，请单击右上角的“显示通知”（铃铛按钮）。 

## <a name="trigger-the-pipeline-manually"></a>手动触发管道
在此步骤中，请手动触发在前面的步骤中发布的管道。 

1. 选择工具栏中的“触发器”，然后选择“立即触发”。 在“管道运行”页上选择“完成”。  

1. 转到左侧的“监视”选项卡。 此时会看到由手动触发器触发的管道运行。 可以使用“操作”列中的链接来查看活动详细信息以及重新运行该管道。

    ![监视管道运行](./media/tutorial-copy-data-portal/monitor-pipeline.png)
1. 若要查看与管道运行关联的活动运行，请选择“操作”列中的“查看活动运行”链接。 此示例中只有一个活动，因此列表中只看到一个条目。 有关复制操作的详细信息，请选择“操作”列中的“详细信息”链接（眼镜图标）。 若要回到“管道运行”视图，请选择顶部的“管道”。 若要刷新视图，请选择“刷新”。

    ![监视活动运行](./media/tutorial-copy-data-portal/view-activity-runs.png)
1. 验证是否又向 SQL 数据库的 **emp** 表添加了两个行。 

## <a name="trigger-the-pipeline-on-a-schedule"></a>按计划触发管道
在此计划中，请为管道创建计划触发器。 触发器按指定的计划（例如，每小时或每天）运行管道。 在此示例中，请将触发器设置为每分钟运行一次，直至指定的结束日期/时间。 

1. 转到左侧位于“监视器”选项卡上方的“创作”选项卡。 

1. 转到你的管道，在工具栏上单击“触发器”，然后选择“新建/编辑”。 

1. 在“添加触发器”窗口中选择“选择触发器”，然后选择“+ 新建”。 

    ![新建按钮](./media/tutorial-copy-data-portal/add-trigger-new-button.png)
1. 在“新建触发器”窗口中，执行以下步骤： 

    a. 在“名称”下输入 **RunEveryMinute**。

    b. 在“结束”下选择“在特定日期”。

    c. 在“结束日期”下选择下拉列表。

    d. 选择“当天”选项。 默认情况下，结束日期设置为第二天。

    e. 更新“分钟”部分，使之超过当前的日期/时间数分钟。 触发器只会在发布所做的更改后激活。 如果将其设置为仅数分钟后激活，而到时又不进行发布，则看不到触发器运行。

    f. 选择“应用”。 

    ![触发器属性](./media/tutorial-copy-data-portal/set-trigger-properties.png)

    g. 选中“已激活”选项。 可以在以后使用此复选框停用它和激活它。

    h. 选择“**下一步**”。

    ![“已激活”按钮](./media/tutorial-copy-data-portal/trigger-activiated-next.png)

    > [!IMPORTANT]
    > 每个管道运行都有相关联的成本，因此请正确设置结束日期。 
1. 在“触发器运行参数”页中查看以下警告，然后选择“完成”。 此示例中的管道不采用任何参数。 

    ![触发器运行参数](./media/tutorial-copy-data-portal/trigger-pipeline-parameters.png)

1. 单击“全部发布”来发布更改。 

1. 转到左侧的“监视”选项卡，查看触发的管道运行。 

    ![触发的管道运行](./media/tutorial-copy-data-portal/triggered-pipeline-runs.png)    
1. 若要从“管道运行”视图切换到“触发器运行”视图，请选择“管道运行”，然后选择“触发器运行”。
    
    ![触发器运行](./media/tutorial-copy-data-portal/trigger-runs-menu.png)
1. 可以在列表中看到触发器运行。 

    ![触发器运行列表](./media/tutorial-copy-data-portal/trigger-runs-list.png)
1. 验证是否每分钟将两个行（对于每个管道运行）插入 **emp** 表中，直至指定的结束时间。 

## <a name="next-steps"></a>后续步骤
此示例中的管道将数据从 Blob 存储中的一个位置复制到另一个位置。 你已了解如何： 

> [!div class="checklist"]
> * 创建数据工厂。
> * 创建包含复制活动的管道。
> * 测试性运行管道。
> * 手动触发管道。
> * 按计划触发管道。
> * 监视管道和活动运行。


若要了解如何将数据从本地复制到云，请转到以下教程： 

> [!div class="nextstepaction"]
>[将数据从本地复制到云](tutorial-hybrid-copy-portal.md)
