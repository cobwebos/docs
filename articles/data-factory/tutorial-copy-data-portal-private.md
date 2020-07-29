---
title: 使用专用终结点创建 Azure 数据工厂管道
description: 本教程分步说明了如何使用 Azure 门户创建带管道的数据工厂。 该管道通过复制活动将数据从 Azure Blob 存储复制到 Azure SQL 数据库。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 05/15/2020
ms.author: jingwang
ms.openlocfilehash: 9458c89922fab8450b7cb8e202491d4c47e250e8
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86540453"
---
# <a name="copy-data-securely-from-azure-blob-storage-to-a-sql-database-by-using-private-endpoints"></a>使用专用终结点将数据从 Azure Blob 存储安全复制到 SQL 数据库

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

在本教程中，请使用 Azure 数据工厂用户界面 (UI) 创建数据工厂。 此数据工厂中的管道使用 [Azure 数据工厂管理的虚拟网络](managed-virtual-network-private-endpoint.md)中的专用终结点将数据从 Azure Blob 存储安全地复制到 Azure SQL 数据库（均允许仅访问选定的网络）。 本教程中的配置模式适用于从基于文件的数据存储复制到关系数据存储。 如需可以用作源和接收器的数据存储的列表，请参阅[支持的数据存储](https://docs.microsoft.com/azure/data-factory/copy-activity-overview)表。

> [!NOTE]
>
> - 如果对数据工厂不熟悉，请参阅 [Azure 数据工厂简介](https://docs.microsoft.com/azure/data-factory/introduction)。

将在本教程中执行以下步骤：

> * 创建数据工厂
> * 创建包含复制活动的管道


## <a name="prerequisites"></a>先决条件
* **Azure 订阅**。 如果还没有 Azure 订阅，可以在开始前创建一个[免费 Azure 帐户](https://azure.microsoft.com/free/)。
* **Azure 存储帐户**。 可将 Blob 存储用作源数据存储。 如果没有存储帐户，请参阅[创建 Azure 存储帐户](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal)以获取创建步骤。 **请确保存储帐户仅允许来自“选定的网络”的访问。** 
* **Azure SQL 数据库**。 将数据库用作接收器数据存储。 如果没有 Azure SQL 数据库，请参阅[创建 SQL 数据库](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal)，了解创建该数据库的步骤。 **确保 Azure SQL 数据库帐户仅允许来自“选定的网络”的访问。** 

### <a name="create-a-blob-and-a-sql-table"></a>创建 blob 和 SQL 表

现在，请执行以下步骤来准备本教程所需的 Blob 存储和 SQL 数据库。

#### <a name="create-a-source-blob"></a>创建源 blob

1. 启动记事本。 复制以下文本并将其在磁盘上另存为 **emp.txt** 文件：

    ```
    FirstName,LastName
    John,Doe
    Jane,Doe
    ```

1. 在 Blob 存储中创建名为 **adftutorial** 的容器。 在该容器中创建名为 input 的文件夹。 然后，将 **emp.txt** 文件上传到 **input** 文件夹。 请使用 Azure 门户或工具（例如 [Azure 存储资源管理器](https://storageexplorer.com/)）执行这些任务。

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

1. 允许 Azure 服务访问 SQL Server。 确保 SQL Server 的“允许访问 Azure 服务”  处于“打开”  状态，以便数据工厂可以将数据写入 SQL Server。 若要验证并启用此设置，请转至 Azure SQL Server >“概述”>“设置服务器防火墙”>将“允许访问 Azure 服务”  选项设置为“打开”  。

## <a name="create-a-data-factory"></a>创建数据工厂
在此步骤中，请先创建数据工厂，然后启动数据工厂 UI，在该数据工厂中创建一个管道。

1. 打开 **Microsoft Edge** 或 **Google Chrome**。 目前，仅 Microsoft Edge 和 Google Chrome Web 浏览器支持数据工厂 UI。


2. 在左侧菜单中，选择“创建资源” > “Analytics” > “数据工厂”。

3. 在“新建数据工厂”页的“名称”下输入 **ADFTutorialDataFactory** 。

   Azure 数据工厂的名称必须 *全局唯一*。 如果收到有关名称值的错误消息，请为数据工厂输入另一名称。 （例如 yournameADFTutorialDataFactory）。 有关数据工厂项目的命名规则，请参阅[数据工厂命名规则](https://docs.microsoft.com/azure/data-factory/naming-rules)。

4. 选择要在其中创建数据工厂的 Azure **订阅**。

5. 对于“资源组”，请执行以下步骤之一：

    a. 选择“使用现有资源组”，并从下拉列表选择现有的资源组。

    b. 选择“新建”，并输入资源组的名称。 
         
    若要了解资源组，请参阅[使用资源组管理 Azure 资源](https://docs.microsoft.com/azure/azure-resource-manager/management/overview)。 

6. 在“版本”下选择“V2”。

7. 在“位置”下选择数据工厂所在的位置。 下拉列表中仅显示支持的位置。 数据工厂使用的数据存储（例如，Azure 存储和 SQL 数据库）和计算资源（例如，Azure HDInsight）可以位于其他区域。


8. 选择“创建” 。


9. 创建完成后，通知中心内会显示通知。 选择“转到资源”导航到“数据工厂”页。

10. 选择“创作和监视”，在单独的选项卡中启动数据工厂 UI。

## <a name="create-an-azure-integration-runtime-in-adf-managed-virtual-network"></a>在 ADF 托管的虚拟网络中创建 Azure Integration Runtime
在此步骤中，你将创建一个 Azure Integration Runtime 并启用托管虚拟网络。

1. 在 ADF 门户中，转到“管理中心”并单击“新建”以创建新的 Azure Integration Runtime。 
   ![新建 Azure Integration Runtime](./media/tutorial-copy-data-portal-private/create-new-azure-ir.png)
2. 选择创建 Azure * * Integration Runtime。
   ![新 Azure Integration Runtime](./media/tutorial-copy-data-portal-private/azure-ir.png)
3. 启用“虚拟网络”。
   ![新 Azure Integration Runtime](./media/tutorial-copy-data-portal-private/enable-managed-vnet.png)
4. 选择“创建”。

## <a name="create-a-pipeline"></a>创建管道
本步骤在数据工厂中创建包含复制活动的管道。 复制活动将数据从 Blob 存储复制到 SQL 数据库。 在[快速入门教程](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal)中，已通过以下步骤创建一个管道：

1. 创建链接服务。
1. 创建输入和输出数据集。
1. 创建管道。

在本教程中，请首先创建管道， 然后在配置管道时根据需要创建链接服务和数据集。

1. 在“开始使用”页中，选择“创建管道”。

   ![创建管道](./media/doc-common-process/get-started-page.png)
1. 在管道的“属性”窗格中，输入“CopyPipeline”作为管道的“名称” 。

1. 在“活动”工具箱中，展开“移动和转换”类别，然后将“复制数据”活动从工具箱拖放到管道设计器图面。 指定 **CopyFromBlobToSql** 作为**名称**。

    ![复制活动](./media/tutorial-copy-data-portal-private/drag-drop-copy-activity.png)

### <a name="configure-source"></a>配置源

>[!TIP]
>本教程使用“帐户密钥”作为源数据存储的身份验证类型，但你可以根据需要选择其他受支持的身份验证方法：“SAS URI”、“服务主体”和“托管标识”。 有关详细信息，请参阅[此文](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#linked-service-properties)中的相应部分。
>为了安全地存储数据存储的机密，我们还建议使用 Azure Key Vault。 有关详细说明，请参阅[此文](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)。

#### <a name="create-source-dataset-and-linked-service"></a>创建源数据集和链接服务

1. 转到“源”选项卡。选择“+ 新建”创建源数据集。

1. 在“新建数据集”对话框中选择“Azure Blob 存储”，然后选择“继续”。 源数据位于 Blob 存储中，因此选择“Azure Blob 存储”作为源数据集。

1. 在“选择格式”对话框中选择数据的格式类型，然后选择“继续”。

1. 在“设置属性”对话框中，输入 **SourceBlobDataset** 作为名称。 选中“第一行作为标题”复选框。 在“链接服务”文本框下，选择“+ 新建”。

1. 在“新建链接服务(Azure Blob 存储)”窗口中，输入 **AzureStorageLinkedService** 作为名称，从“存储帐户名称”列表中选择你的存储帐户。 

1. 请确保启用“交互式创作”。 启用可能需要大约 1 分钟的时间。

    ![交互式创作](./media/tutorial-copy-data-portal-private/interactive-authoring.png)

1. 选择“测试连接”，当存储帐户仅允许来自“选定网络”的访问时，连接应该会失败，并要求 Azure 数据工厂创建一个专用终结点，并在使用它之前需要先获得批准。 在错误消息中，应会看到一个用于创建专用终结点的链接，可以通过该链接创建托管的专用终结点。 一种替代方法是直接转到“管理”选项卡，按照[下一节](#create-a-managed-private-endpoint)中的说明创建托管专用终结点
> [!NOTE]
> 管理选项卡可能并非适用于所有数据工厂实例。 如果看不到专用终结点，可以通过以下方式访问：“创建者”选项卡-->“连接”-->“专用终结点”  
1. 让对话框保持打开状态，然后转到上面选定的存储帐户。

1. 按照[此部分](#approval-of-a-private-link-in-storage-account)中的说明批准专用链接。

1. 返回对话框。 再次测试连接，然后选择“创建”以部署该链接服务。 

1. 创建链接服务后，会导航回到“设置属性”页。 在“文件路径”旁边，选择“浏览”。

1. 导航到 adftutorial/input 文件夹，选择 emp.txt 文件，然后选择“确定”。

1. 选择“确定”。 将自动导航到管道页。 在“源”选项卡中，确认已选择“SourceBlobDataset”。 若要预览此页上的数据，请选择“预览数据”。

    ![源数据集](./media/tutorial-copy-data-portal-private/source-dataset-selected.png)

#### <a name="create-a-managed-private-endpoint"></a>创建托管专用终结点

如果在测试上述连接时未单击超链接，请依循以下路径。 现在，需要创建一个托管的专用终结点，并将该终结点连接到上面创建的链接服务。

1. 转到“管理”选项卡。
> [!NOTE]
> 管理选项卡可能并非适用于所有数据工厂实例。 如果看不到专用终结点，可以通过以下方式访问：“创建者”选项卡-->“连接”-->“专用终结点”  

1. 转到“托管专用终结点”部分。

1. 选择托管专用终结点下的“+ 新建”。

    ![新托管专用终结点](./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png) 

1. 从列表中选择 Azure Blob 存储磁贴，然后选择“继续”。

1. 输入前面创建的存储帐户的名称。

1. 选择“创建”。

1. 等待几秒钟后，会显示创建的专用链接需要审批。

1. 选择前面创建的专用终结点。 可以看到一个超链接，该超链接将引导你在存储帐户级别批准专用终结点。

    ![管理专用终结点](./media/tutorial-copy-data-portal-private/manage-private-endpoint.png) 

#### <a name="approval-of-a-private-link-in-storage-account"></a>在存储帐户中批准专用链接
1. 在存储帐户中，转到“设置”部分下的“专用终结点连接”。

1. 勾选前面创建的专用终结点，并选择“批准”。

    ![批准专用终结点](./media/tutorial-copy-data-portal-private/approve-private-endpoint.png)

1. 添加描述，并单击“是”
1. 返回 Azure 数据工厂中“管理”选项卡的“托管专用终结点”部分 。
1. 大约 1-2 分钟后，对专用终结点的批准才会反映在 Azure 数据工厂 UI 中。


### <a name="configure-sink"></a>配置接收器
>[!TIP]
>本教程使用“SQL 身份验证”作为接收器数据存储的身份验证类型，但你可以根据需要选择其他受支持的身份验证方法：“服务主体”和“托管标识”。 有关详细信息，请参阅[此文](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database#linked-service-properties)中的相应部分。
>为了安全地存储数据存储的机密，我们还建议使用 Azure Key Vault。 有关详细说明，请参阅[此文](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)。

#### <a name="create-sink-dataset-and-linked-service"></a>创建接收器数据集和链接服务
1. 转到“接收器”选项卡，选择“+ 新建”，创建一个接收器数据集。

1. 在“新建数据集”对话框中的搜索框内输入“SQL”来筛选连接器，选择“Azure SQL 数据库”，然后选择“继续”。 在本教程中，请将数据复制到 SQL 数据库。

1. 在“设置属性”对话框中，输入 **OutputSqlDataset** 作为名称。 从“链接服务”下拉列表中，选择“+ 新建”。 数据集必须与链接服务相关联。 该链接服务包含的连接字符串可供数据工厂用于在运行时连接到 SQL 数据库。 数据集指定可将数据复制到其中的容器、文件夹和文件（可选）。

1. 在“新建链接服务(Azure SQL 数据库)”对话框中执行以下步骤：

    1. 在“名称”下输入 **AzureSqlDatabaseLinkedService**。
    1. 在“服务器名称”下选择 SQL Server 实例。
    1. 请确保启用“交互式创作”。
    1. 在“数据库名称”下选择自己的 SQL 数据库。
    1. 在“用户名”下输入用户的名称。
    1. 在“密码”下输入用户的密码。
    1. 选择“测试连接”。 应会出现错误，因为存储帐户仅允许来自“选定网络”的访问，并要求 Azure 数据工厂创建一个专用终结点，在使用它之前需要获得批准。 在错误消息中，应会看到一个用于创建专用终结点的链接，可以通过该链接创建托管的专用终结点。 一种替代方法是直接转到“管理”选项卡，按照 下一节 中的说明创建托管专用终结点
    1. 让对话框保持打开状态，然后转到上面选定的 SQL Server。    
    1. 按照[此部分](#approval-of-a-private-link-in-sql-server)中的说明批准专用链接。
    1. 返回对话框。 再次测试连接，然后选择“创建”以部署该链接服务。 

1. 将自动导航到“设置属性”对话框。 在“表”中选择“[dbo].[emp]”。 然后选择“确定”。

1. 转到包含管道的选项卡。在“接收器数据集”中，确认已选中“OutputSqlDataset”。

    ![“管道”选项卡](./media/tutorial-copy-data-portal-private/pipeline-tab-2.png)       

可以选择按照[复制活动中的架构映射](https://docs.microsoft.com/azure/data-factory/copy-activity-schema-and-type-mapping)中所述将源架构映射到对应的目标架构。

#### <a name="create-a-managed-private-endpoint"></a>创建托管专用终结点

如果在测试上述连接时未单击超链接，请依循以下路径。 现在，需要创建一个托管的专用终结点，并将该终结点连接到上面创建的链接服务。

1. 转到“管理”选项卡。
1. 转到“托管专用终结点”部分。
1. 选择托管专用终结点下的“+ 新建”。

    ![新托管专用终结点](./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png) 

1. 从列表中选择 Azure SQL 数据库存储磁贴，然后选择“继续”。
1. 输入上面所选的 SQL Server 的名称。
1. 选择“创建”。
1. 等待几秒钟后，会显示创建的专用链接需要审批。
1. 选择前面创建的专用终结点。 可以看到一个超链接，该超链接将引导你在 SQL Server 级别批准专用终结点。


#### <a name="approval-of-a-private-link-in-sql-server"></a>在 SQL server 中批准专用链接
1. 在 SQL Server 中，转到“设置”部分下的“专用终结点连接”。
1. 勾选前面创建的专用终结点，并选择“批准”。
1. 添加描述，并单击“是”。
1. 返回 Azure 数据工厂中“管理”选项卡的“托管专用终结点”部分 。
1. 大约 1-2 分钟后，对专用终结点的批准才会反映出来。

#### <a name="debug-and-publish-the-pipeline"></a>调试和发布管道

可以先调试管道，然后再将项目（链接服务、数据集和管道）发布到数据工厂或自己的 Azure Repos GIT 存储库。

1. 若要调试管道，请在工具栏上选择“调试”。 可以在窗口底部的“输出”选项卡中看到管道运行的状态。
2. 在管道可以成功运行后，在顶部工具栏中选择“全部发布”。 此操作将所创建的实体（数据集和管道）发布到数据工厂。
3. 等待“已成功发布”消息出现。 若要查看通知消息，请单击右上角的“显示通知”（铃铛按钮）。


#### <a name="summary"></a>总结
此示例中的管道使用托管虚拟网络中的专用终结点将数据从 Blob 存储复制到 Azure SQL DB。 你已了解如何执行以下操作：

> * 创建数据工厂
> * 创建包含复制活动的管道

