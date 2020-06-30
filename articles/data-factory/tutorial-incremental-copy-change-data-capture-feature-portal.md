---
title: 使用变更数据捕获以增量方式复制数据
description: 在本教程中，你将创建一个 Azure 数据工厂管道，该管道以增量方式将增量数据从 Azure SQL 托管实例数据库中的表复制到 Azure 存储。
services: data-factory
ms.author: nihurt
author: hurtn
manager: ''
ms.reviewer: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: ''
ms.date: 05/04/2020
ms.openlocfilehash: ca6b0ff197c21dd41521d2aa6106aa3b0df2d177
ms.sourcegitcommit: bf99428d2562a70f42b5a04021dde6ef26c3ec3a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2020
ms.locfileid: "85249477"
---
# <a name="incrementally-load-data-from-azure-sql-managed-instance-to-azure-storage-using-change-data-capture-cdc"></a>使用变更数据捕获 (CDC)，以增量方式将 Azure SQL 托管实例中的数据加载到 Azure 存储

在本教程中，请创建一个带管道的 Azure 数据工厂，以便根据源 Azure SQL 托管实例数据库中的**变更数据捕获 (CDC)** 信息将增量数据加载到 Azure Blob 存储。  

在本教程中执行以下步骤：

> [!div class="checklist"]
> * 准备源数据存储
> * 创建数据工厂。
> * 创建链接服务。
> * 创建源和接收器数据集。
> * 创建、调试和运行管道以检查变更的数据
> * 修改源表中的数据
> * 完成、运行和监视完整的增量复制管道

## <a name="overview"></a>概述
可以使用 Azure SQL 托管实例 (MI) 和 SQL Server 等数据存储支持的变更数据捕获技术来确定变更的数据。  本教程介绍如何将 Azure 数据工厂与 SQL 变更数据捕获技术配合使用，通过增量方式将增量数据从 Azure SQL 托管实例加载到 Azure Blob 存储中。  有关 SQL 变更数据捕获技术的更具体信息，请参阅 [SQL Server 中的变更数据捕获](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-data-capture-sql-server)。

## <a name="end-to-end-workflow"></a>端到端工作流
下面是典型的端到端工作流步骤，用于通过变更数据捕获技术以增量方式加载数据。

> [!NOTE]
> Azure SQL MI 和 SQL Server 都支持变更数据捕获技术。 本教程使用 Azure SQL 托管实例作为源数据存储。 也可以使用本地 SQL Server。

## <a name="high-level-solution"></a>高级解决方案
在本教程中，请创建管道来执行下述操作：  

   1. 创建**查找活动**，来计算 SQL 数据库 CDC 表中变更的记录的数量，然后将其传递给“IF 条件”活动。
   2. 创建 **If 条件**，来检查是否有变更的记录，如果有，则调用“复制活动”。
   3. 创建**复制活动**，来将插入/更新/删除的数据从 CDC 表复制到 Azure Blob 存储。

如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。

## <a name="prerequisites"></a>先决条件
* **Azure SQL 数据库托管实例**。 将数据库用作**源**数据存储。 如果没有 Azure SQL 数据库托管实例，请参阅[创建 Azure SQL 数据库托管实例](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started)一文获取创建步骤。
* **Azure 存储帐户**。 将 Blob 存储用作**接收器**数据存储。 如果没有 Azure 存储帐户，请参阅[创建存储帐户](../storage/common/storage-account-create.md)一文获取创建步骤。 创建名为“raw”的容器。 

### <a name="create-a-data-source-table-in-azure-sql-database"></a>在 Azure SQL 数据库中创建数据源表

1. 启动 **SQL Server Management Studio**，然后连接到 Azure SQL 托管实例服务器。
2. 在“服务器资源管理器”中，右键单击你的**数据库**，然后选择“新建查询”。
3. 针对 Azure SQL 托管实例数据库运行以下 SQL 命令，以创建名为 `customers` 的表作为数据源存储。  

    ```sql
    create table customers 
    (
    customer_id int, 
    first_name varchar(50), 
    last_name varchar(50), 
    email varchar(100), 
    city varchar(50), CONSTRAINT "PK_Customers" PRIMARY KEY CLUSTERED ("customer_id") 
     );
    ```
4. 通过运行以下 SQL 查询，在数据库和源表 (customers) 上启用**变更数据捕获**机制：

    > [!NOTE]
    > - 将&lt;源架构名称&gt;替换为你的 Azure SQL MI 的架构，其中包含 customers 表。
    > - 变更数据捕获在更改要跟踪的表的事务中不做任何事情。 而是将插入、更新和删除操作写入事务日志中。 如果没有定期系统地清除数据，更改表中存储的数据将会变得非常大。 有关详细信息，请参阅[对数据库启用变更数据捕获](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-data-capture-sql-server?enable-change-data-capture-for-a-database=&view=sql-server-ver15)

    ```sql
    EXEC sys.sp_cdc_enable_db 
    
    EXEC sys.sp_cdc_enable_table
    @source_schema = 'dbo',
    @source_name = 'customers', 
    @role_name = 'null',
    @supports_net_changes = 1
    ```
5. 通过运行以下命令，将数据插入 customers 表中：

    ```sql
     insert into customers 
        (customer_id, first_name, last_name, email, city) 
     values 
        (1, 'Chevy', 'Leward', 'cleward0@mapy.cz', 'Reading'),
        (2, 'Sayre', 'Ateggart', 'sateggart1@nih.gov', 'Portsmouth'),
        (3, 'Nathalia', 'Seckom', 'nseckom2@blogger.com', 'Portsmouth');
    ```

    > [!NOTE]
    > 在启用“变更数据捕获”之前，不会捕获对表的历史更改。

## <a name="create-a-data-factory"></a>创建数据工厂

1. 启动 **Microsoft Edge** 或 **Google Chrome** Web 浏览器。 目前，仅 Microsoft Edge 和 Google Chrome Web 浏览器支持数据工厂 UI。
1. 在左侧菜单中，选择“创建资源” > “数据 + 分析” > “数据工厂”：

   ![在“新建”窗格中选择“数据工厂”](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-azure-data-factory-menu.png)

2. 在“新建数据工厂”页中，输入 **ADFTutorialDataFactory** 作为**名称**。

     ![“新建数据工厂”页](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-azure-data-factory.png)

   Azure 数据工厂的名称必须 **全局唯一**。 如果收到错误，请更改数据工厂的名称（例如改为 yournameADFTutorialDataFactory），并重新尝试创建。 有关数据工厂项目命名规则，请参阅[数据工厂 - 命名规则](naming-rules.md)一文。

       `Data factory name “ADFTutorialDataFactory” is not available`
3. 选择“V2”作为“版本”。
4. 选择要在其中创建数据工厂的 Azure **订阅**。
5. 对于**资源组**，请执行以下步骤之一：

   1. 选择“使用现有资源组”，并从下拉列表选择现有的资源组。
   2. 选择“新建”，并输入资源组的名称。   
         
    若要了解有关资源组的详细信息，请参阅 [使用资源组管理 Azure 资源](../azure-resource-manager/management/overview.md)。  
5. 选择数据工厂的**位置**。 下拉列表中仅显示支持的位置。 数据工厂使用的数据存储（Azure 存储、Azure SQL 数据库，等等）和计算资源（HDInsight 等）可以位于其他区域中。
6. 取消选中“启用 GIT”。     
7. 单击“创建”。
8. 部署完成后，单击“转到资源”

   ![数据工厂主页](./media/tutorial-incremental-copy-change-data-capture-feature-portal/data-factory-deploy-complete.png)
9. 创建完成后，可以看到图中所示的“数据工厂”页。

   ![数据工厂主页](./media/tutorial-incremental-copy-change-data-capture-feature-portal/data-factory-home-page.png)
10. 单击“创作和监视”磁贴，在单独的选项卡中启动 Azure 数据工厂用户界面 (UI)。
11. 在“入门”页的左侧面板中，切换到“编辑”选项卡，如下图所示： 

    ![“创建管道”按钮](./media/tutorial-incremental-copy-change-data-capture-feature-portal/get-started-page.png)

## <a name="create-linked-services"></a>创建链接服务
可在数据工厂中创建链接服务，将数据存储和计算服务链接到数据工厂。 在本部分中，请创建 Azure 存储帐户和 Azure SQL MI 的链接服务。

### <a name="create-azure-storage-linked-service"></a>创建 Azure 存储链接服务。
在此步骤中，请将 Azure 存储帐户链接到数据工厂。

1. 依次单击“连接”、“+ 新建”。 

   ![“新建连接”按钮](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-connection-button-storage.png)
2. 在“新建链接服务”窗口中，选择“Azure Blob 存储”，然后单击“继续”。  

   ![选择“Azure Blob 存储”](./media/tutorial-incremental-copy-change-data-capture-feature-portal/select-azure-storage.png)
3. 在“新建链接服务”窗口中执行以下步骤：

   1. 输入 **AzureStorageLinkedService** 作为**名称**。
   2. 对于“存储帐户名称”，请选择自己的 Azure 存储帐户。
   3. 单击“ **保存**”。

   ![Azure 存储帐户设置](./media/tutorial-incremental-copy-change-data-capture-feature-portal/azure-storage-linked-service-settings.png)


### <a name="create-azure-sql-mi-database-linked-service"></a>创建 Azure SQL MI 数据库链接服务。
在此步骤中，将 Azure SQL MI 数据库链接到数据工厂。

> [!NOTE]
> 对于使用 SQL MI 的用户，请参阅[此处](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database-managed-instance#prerequisites)以获取通过公共和专用终结点进行访问的相关信息。 如果使用专用终结点，则需要使用自承载集成运行时运行此管道。 这同样适用于在 VM 或 VNet 方案中本地运行 SQL Server 的管道。

1. 依次单击“连接”、“+ 新建”。 
2. 在“新建链接服务”窗口中，选择“Azure SQL 数据库托管实例”，然后单击“继续”。
3. 在“新建链接服务”窗口中执行以下步骤：

   1. 对于“名称”字段，请输入“AzureSqlMI1”。
   2. 对于“服务器名称”字段，请选择你的 SQL 服务器。
   4. 对于“数据库名称”字段，请选择你的 SQL 数据库。
   5. 对于“用户名”字段，请输入用户的名称。
   6. 对于“密码”字段，请输入用户的密码。
   7. 单击“测试连接”以测试连接。
   8. 单击“保存”保存链接服务。

   ![Azure SQL MI 数据库链接服务设置](./media/tutorial-incremental-copy-change-data-capture-feature-portal/azure-sql-managed-instance-database-linked-service-settings.png)

## <a name="create-datasets"></a>创建数据集
在此步骤中，请创建用于表示数据源和数据目标的数据集。

### <a name="create-a-dataset-to-represent-source-data"></a>创建用于表示源数据的数据集
在此步骤中，请创建一个代表源数据的数据集。

1. 在树状视图中，依次单击“+”（加号）、“数据集”。 

   ![“新建数据集”菜单](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-dataset-menu.png)
2. 选择“Azure SQL 数据库托管实例”，然后单击“继续”。

   ![源数据集类型 - Azure SQL 数据库](./media/tutorial-incremental-copy-change-data-capture-feature-portal/select-azure-sql-database.png)
   
3. 在“设置属性”选项卡中，设置数据集名称和连接信息：
 
   1. 对于“链接服务”，请选择“AzureSqlMI1”。
   2. 对于“表名称”，请选择“[dbo].[dbo_customers_CT]”。  注意：对 customers 表启用 CDC 时，将自动创建此表。 更改的数据永远不会直接从该表查询获得，而是通过 [CDC 函数](https://docs.microsoft.com/sql/relational-databases/system-functions/change-data-capture-functions-transact-sql?view=sql-server-ver15)提取获得。

   ![源连接](./media/tutorial-incremental-copy-change-data-capture-feature-portal/source-dataset-configuration.png)

### <a name="create-a-dataset-to-represent-data-copied-to-sink-data-store"></a>创建一个数据集，用于表示复制到接收器数据存储的数据。
在此步骤中，请创建一个数据集，代表从源数据存储复制的数据。 在执行先决条件中的步骤时，你已在 Azure Blob 存储中创建了 Data Lake 容器。 创建容器（如果不存在），或者将容器设置为现有容器的名称。 在本教程中，输出文件名是使用触发器时间动态生成的，稍后将对其进行配置。

1. 在树状视图中，依次单击“+”（加号）、“数据集”。 

   ![“新建数据集”菜单](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-dataset-menu.png)
2. 选择“Azure Blob 存储”，然后单击“继续”。

   ![接收器数据集类型 - Azure Blob 存储](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-type.png)
3. 选择“DelimitedText”，然后单击“继续”。

   ![接收器数据集格式 - DelimitedText](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-format.png)
4. 在“设置属性”选项卡中，设置数据集名称和连接信息：

   1. 为“链接服务”选择“AzureStorageLinkedService”。 
   2. 输入“raw”作为“文件路径”的“容器”部分。
   3. 启用“第一行作为标题”
   4. 单击“确定”

   ![接收器数据集 - 连接](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-configuration.png)

## <a name="create-a-pipeline-to-copy-the-changed-data"></a>创建复制已更改数据的管道
在此步骤中，将创建管道，该管道首先使用**查找活动**检查 change 表中存在的已更改记录的数量。 “IF 条件”活动检查已更改记录的数量是否大于零，然后运行**复制活动**以将插入/更新/删除的数据从 Azure SQL 数据库复制到 Azure Blob 存储。 最后，配置翻转窗口触发器，并且开始和结束时间将传递给活动作为开始和结束窗口参数。 

1. 在数据工厂 UI 中，切换到“编辑”选项卡。依次单击左窗格中的“+”（加号）、“管道”。 

    ![“新建管道”菜单](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-pipeline-menu.png)
2. 此时会显示用于配置管道的新选项卡。 树状视图中也会显示管道。 在“属性”窗口中，将管道的名称更改为 **IncrementalCopyPipeline**。

    ![管道名称](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-name.png)
3. 在“活动”工具箱中展开“常规”， 将**查找**活动拖放到管道设计器图面。 将活动名称设置为“GetChangeCount”。 此活动获取给定时间窗口内 change 表中的记录数。

    ![查找活动 - 名称](./media/tutorial-incremental-copy-change-data-capture-feature-portal/first-lookup-activity-name.png)
4. 切换到“属性”窗口中的“设置”：
   1. 对于**源数据集**字段，请指定 SQL MI 数据集名称。
   2. 选择“查询”选项，并在查询框中输入以下内容：
    ```sql
    DECLARE  @from_lsn binary(10), @to_lsn binary(10);  
    SET @from_lsn =sys.fn_cdc_get_min_lsn('dbo_customers');  
    SET @to_lsn = sys.fn_cdc_map_time_to_lsn('largest less than or equal',  GETDATE());
    SELECT count(1) changecount FROM cdc.fn_cdc_get_all_changes_dbo_customers(@from_lsn, @to_lsn, 'all')
    ```
   3. 启用“仅第一行”

    ![查找活动 - 设置](./media/tutorial-incremental-copy-change-data-capture-feature-portal/first-lookup-activity-settings.png)
5. 单击“预览数据”按钮，以确保“查找”活动获取有效输出

    ![查找活动 - 预览](./media/tutorial-incremental-copy-change-data-capture-feature-portal/first-lookup-activity-preview.png)
6. 在“活动”工具箱中展开“迭代和条件”，然后将“If 条件”活动拖放到管道设计器图面。 将活动名称设置为“HasChangedRows”。 

    ![If Condition 活动 - 名称](./media/tutorial-incremental-copy-change-data-capture-feature-portal/if-condition-activity-name.png)
7. 切换到“属性”窗口中的“活动”：

   1. 输入以下**表达式**
   
    ```adf
    @greater(int(activity('GetChangeCount').output.firstRow.changecount),0)
    ```

   2. 单击铅笔图标编辑 True 条件。

   ![If Condition 活动 - 设置](./media/tutorial-incremental-copy-change-data-capture-feature-portal/if-condition-activity-setting.png)

   3. 在“活动”工具箱中展开“常规”，然后将“等待”活动拖放到管道设计器图面。 这是为了调试 If 条件的临时活动，将在本教程的后面部分进行更改。 

   ![If 条件 True - 等待](./media/tutorial-incremental-copy-change-data-capture-feature-portal/if-condition-activity-wait.png)

   4. 单击 IncrementalCopyPipeline 痕迹，以返回到主管道。

8. 在“调试”模式下运行管道，以验证管道是否成功执行。 

   ![管道 - 调试](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-debug.png)
9. 接下来，返回到 True 条件步骤，并删除“等待”活动。 在“活动”工具箱中，展开“移动和转换”，然后将“复制”活动拖放到管道设计器图面。 将活动的名称设置为 **IncrementalCopyActivity**。 

   ![复制活动 - 名称](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-source-name.png)
10. 在“属性”窗口中切换到“源”选项卡，然后执行以下步骤： 

   1. 对于**源数据集**字段，请指定 SQL MI 数据集名称。 
   2. 为“使用查询”选择“查询”。 
   3. 为“查询”输入以下内容。

    ```sql
    DECLARE @from_lsn binary(10), @to_lsn binary(10); 
    SET @from_lsn =sys.fn_cdc_get_min_lsn('dbo_customers'); 
    SET @to_lsn = sys.fn_cdc_map_time_to_lsn('largest less than or equal', GETDATE());
    SELECT * FROM cdc.fn_cdc_get_all_changes_dbo_customers(@from_lsn, @to_lsn, 'all')
    ```

   ![复制活动 - 源设置](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-source-settings.png)

11. 单击“预览”，以验证查询是否正确返回更改的行。

    ![复制活动 - 接收器设置](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-source-preview.png)
12. 切换到“接收器”选项卡，然后为“接收器数据集”字段指定 Azure 存储数据集。

    ![复制活动 - 接收器设置](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-sink-settings.png)
13. 单击一下返回到主管道画布，然后将“查找”活动逐个连接到“If 条件”活动。 将附加到“查找”活动的**绿色**按钮拖放到“If 条件”活动。

    ![连接“查找”和“复制”活动](./media/tutorial-incremental-copy-change-data-capture-feature-portal/connect-lookup-if.png)
14. 在工具栏中单击“验证”。 确认没有任何验证错误。 单击 **>>** 关闭“管道验证报告”窗口。

    ![“验证”按钮](./media/tutorial-incremental-copy-change-data-capture-feature-portal/validate-button.png)
15. 单击“调试”以测试管道，并验证是否在存储位置生成了一个文件。

    ![增量管道调试-2](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-debug-2.png)
16. 单击“全部发布”按钮，将实体（链接服务、数据集和管道）发布到数据工厂服务。 等到“发布成功”消息出现。

    ![发布按钮](./media/tutorial-incremental-copy-change-data-capture-feature-portal/publish-button-2.png)    

### <a name="configure-the-tumbling-window-trigger-and-cdc-window-parameters"></a>配置翻转窗口触发器和 CDC 窗口参数 
在此步骤中，将创建翻转窗口触发器以便按计划定期运行作业。 将使用翻转窗口触发器的 WindowStart 和 WindowEnd 系统变量，并将它们作为参数传递给要在 CDC 查询中使用的管道。

1. 导航到“IncrementalCopyPipeline”管道的“参数”选项卡，然后使用“+ 新建”按钮将两个参数（**triggerStartTime** 和 **triggerEndTime**）添加到管道，这将表示翻转窗口的开始和结束时间。 出于调试目的，请以格式 **YYYY-MM-DD HH24:MI:SS.FFF** 添加默认值，但确保 triggerStartTime 不早于要对该表启用的 CDC，否则这将导致错误。

    ![“立即触发”菜单](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-parameters.png)
2. 单击“查找”活动的“设置”选项卡，并将查询配置为使用 start 和 end 参数。 将以下内容复制到查询：
    ```sql
    @concat('DECLARE @begin_time datetime, @end_time datetime, @from_lsn binary(10), @to_lsn binary(10); 
    SET @begin_time = ''',pipeline().parameters.triggerStartTime,''';
    SET @end_time = ''',pipeline().parameters.triggerEndTime,''';
    SET @from_lsn = sys.fn_cdc_map_time_to_lsn(''smallest greater than or equal'', @begin_time);
    SET @to_lsn = sys.fn_cdc_map_time_to_lsn(''largest less than or equal'', @end_time);
    SELECT count(1) changecount FROM cdc.fn_cdc_get_all_changes_dbo_customers(@from_lsn, @to_lsn, ''all'')')
    ```

3. 在“If 条件”活动为 True 的情况下，导航到“复制”活动，然后单击“源”选项卡。将以下内容复制到查询：
    ```sql
    @concat('DECLARE @begin_time datetime, @end_time datetime, @from_lsn binary(10), @to_lsn binary(10); 
    SET @begin_time = ''',pipeline().parameters.triggerStartTime,''';
    SET @end_time = ''',pipeline().parameters.triggerEndTime,''';
    SET @from_lsn = sys.fn_cdc_map_time_to_lsn(''smallest greater than or equal'', @begin_time);
    SET @to_lsn = sys.fn_cdc_map_time_to_lsn(''largest less than or equal'', @end_time);
    SELECT * FROM cdc.fn_cdc_get_all_changes_dbo_customers(@from_lsn, @to_lsn, ''all'')')
    ```
4. 单击“复制”活动的“接收器”选项卡，然后单击“打开”以编辑数据集属性。 单击“参数”选项卡，然后添加名为“triggerStart”的新参数    

    ![接收器数据集配置-3](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-configuration-2.png)
5. 接下来，配置数据集属性以将数据存储在具有基于日期的分区的“客户/增量”子目录中。
   1. 单击数据集属性的“连接”选项卡，然后为“目录”和“文件”部分添加动态内容。 
   2. 单击文本框下方的动态内容链接，在“目录”部分中输入以下表达式：
    
    ```sql
    @concat('customers/incremental/',formatDateTime(dataset().triggerStart,'yyyy/MM/dd'))
    ```
   3. 在“文件”部分中输入以下表达式。 这将根据触发器的开始日期和时间创建文件名，其后缀为 csv 扩展名：
    
    ```sql
    @concat(formatDateTime(dataset().triggerStart,'yyyyMMddHHmmssfff'),'.csv')
    ```
    ![接收器数据集配置-3](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-configuration-3.png)

   4. 单击“IncrementalCopyPipeline”选项卡，导航回到“复制”活动中的“接收器”设置。 
   5. 展开数据集属性，然后使用以下表达式在 triggerStart 参数值中输入动态内容：
     ```sql
     @pipeline().parameters.triggerStartTime
     ```
    ![接收器数据集配置-4](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-configuration-4.png)

6. 单击“调试”以测试管道，并确保文件夹结构和输出文件按预期生成。 下载并打开该文件以验证内容。 

    ![增量复制调试-3](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-debug-3.png)
7. 通过查看管道运行的输入参数，确保参数将注入到查询中。

    ![增量复制调试-4](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-debug-4.png)
8. 单击“全部发布”按钮，将实体（链接服务、数据集和管道）发布到数据工厂服务。 等到“发布成功”消息出现。
9. 最后，配置翻转窗口触发器以便按固定间隔运行管道，并设置开始时间和结束时间参数。 
   1. 单击“添加触发器”按钮，然后选择“新建/编辑”

   ![添加新触发器](./media/tutorial-incremental-copy-change-data-capture-feature-portal/add-trigger.png)

   2. 输入触发器名称并指定开始时间，该时间等于上述调试窗口的结束时间。

   ![翻转窗口触发器](./media/tutorial-incremental-copy-change-data-capture-feature-portal/tumbling-window-trigger.png)

   3. 在下一个屏幕上，分别为 start 和 end 参数指定以下值。
    ```sql
    @formatDateTime(trigger().outputs.windowStartTime,'yyyy-MM-dd HH:mm:ss.fff')
    @formatDateTime(trigger().outputs.windowEndTime,'yyyy-MM-dd HH:mm:ss.fff')
    ```

   ![翻转窗口触发器-2](./media/tutorial-incremental-copy-change-data-capture-feature-portal/tumbling-window-trigger-2.png)

> [!NOTE]
> 请注意，触发器仅在发布后才运行。 此外，翻转窗口的预期行为是运行从开始日期到现在的所有历史间隔。 有关翻转窗口触发器的详细信息，请参阅[此处](https://docs.microsoft.com/azure/data-factory/how-to-create-tumbling-window-trigger)。 
  
10. 使用 **SQL Server Management Studio**，通过运行以下 SQL 对 customer 表进行一些其他更改：
    ```sql
    insert into customers (customer_id, first_name, last_name, email, city) values (4, 'Farlie', 'Hadigate', 'fhadigate3@zdnet.com', 'Reading');
    insert into customers (customer_id, first_name, last_name, email, city) values (5, 'Anet', 'MacColm', 'amaccolm4@yellowbook.com', 'Portsmouth');
    insert into customers (customer_id, first_name, last_name, email, city) values (6, 'Elonore', 'Bearham', 'ebearham5@ebay.co.uk', 'Portsmouth');
    update customers set first_name='Elon' where customer_id=6;
    delete from customers where customer_id=5;
    ```
11. 单击“全部发布”按钮。 等到“发布成功”消息出现。  
12. 几分钟后，管道将触发并且新文件将加载到 Azure 存储中


### <a name="monitor-the-incremental-copy-pipeline"></a>监视增量复制管道
1. 单击左侧的“监视”选项卡。 可以在列表中查看管道运行及其状态。 若要刷新列表，请单击“刷新”。 将鼠标光标悬停在管道名称附近，以访问“重新运行”操作和“消耗”报告。

    ![管道运行](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-pipeline-runs.png)
2. 若要查看与管道运行关联的活动运行，请单击管道名称。 如果检测到更改的数据，将有三个活动（包括复制活动），否则列表中将只有两个条目。 若要切换回到管道运行视图，请单击顶部的“所有管道”链接。

    ![活动运行](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-activity-runs.png)


### <a name="review-the-results"></a>查看结果
可以在 `raw` 容器的 `customers/incremental/YYYY/MM/DD` 文件夹中看到第二个文件。

![来自增量复制的输出文件](media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-run.png)
 

## <a name="next-steps"></a>后续步骤
继续查看以下教程，了解如何仅基于 LastModifiedDate 来复制新的和更改的文件：

> [!div class="nextstepaction"]
>[按 lastmodifieddate 复制新文件](tutorial-incremental-copy-lastmodified-copy-data-tool.md)