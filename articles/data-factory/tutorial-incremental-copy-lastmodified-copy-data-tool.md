---
title: Data tool to copy new and updated files incrementally
description: Create an Azure data factory and then use the Copy Data tool to incrementally load new files based on LastModifiedDate.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 1/24/2019
ms.openlocfilehash: 5c20196bd243d025d58f7cc08e015e1e0038e178
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74217788"
---
# <a name="incrementally-copy-new-and-changed-files-based-on-lastmodifieddate-by-using-the-copy-data-tool"></a>Incrementally copy new and changed files based on LastModifiedDate by using the Copy Data tool

In this tutorial, you'll use the Azure portal to create a data factory. Then, you'll use the Copy Data tool to create a pipeline that incrementally copies new and changed files only, based on their **LastModifiedDate** from Azure Blob storage to Azure Blob storage.

By doing so, ADF will scan all the files from the source store, apply the file filter by their LastModifiedDate, and copy the new and updated file only since last time to the destination store.  Please note that if you let ADF scan huge amounts of files but only copy a few files to destination, you would still expect the long duration due to file scanning is time consuming as well.   

> [!NOTE]
> 如果对 Azure 数据工厂不熟悉，请参阅 [Azure 数据工厂简介](introduction.md)。

In this tutorial, you will perform the following tasks:

> [!div class="checklist"]
> * 创建数据工厂。
> * 使用“复制数据”工具创建管道。
> * 监视管道和活动运行。

## <a name="prerequisites"></a>必备组件

* **Azure 订阅**：如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/)。
* **Azure storage account**: Use Blob storage as the _source_ and _sink_ data store. 如果没有 Azure 存储帐户，请参阅[创建存储帐户](../storage/common/storage-quickstart-create-account.md)中的说明。

### <a name="create-two-containers-in-blob-storage"></a>Create two containers in Blob storage

Prepare your Blob storage for the tutorial by performing these steps.

1. Create a container named **source**. You can use various tools to perform this task, such as [Azure Storage Explorer](https://storageexplorer.com/).

2. Create a container named **destination**. 

## <a name="create-a-data-factory"></a>创建数据工厂

1. 在左侧菜单中，选择“创建资源” > “数据 + 分析” > “数据工厂”： 
   
   ![在“新建”窗格中选择“数据工厂”](./media/doc-common-process/new-azure-data-factory-menu.png)

2. 在“新建数据工厂”页的“名称”下输入 **ADFTutorialDataFactory**。 
 
   数据工厂的名称必须全局唯一。 可能会收到以下错误消息：
   
   ![新的数据工厂错误消息](./media/doc-common-process/name-not-available-error.png)

   如果收到有关名称值的错误消息，请为数据工厂输入另一名称。 例如，使用名称 _**yourname**_ **ADFTutorialDataFactory**。 有关数据工厂项目的命名规则，请参阅[数据工厂命名规则](naming-rules.md)。
3. Select the Azure **subscription** in which you'll create the new data factory. 
4. 对于“资源组”，请执行以下步骤之一：
     
    * 选择“使用现有资源组”，并从下拉列表选择现有的资源组。

    * 选择“新建”，并输入资源组的名称。 
         
    若要了解资源组，请参阅[使用资源组管理 Azure 资源](../azure-resource-manager/resource-group-overview.md)。

5. Under **version**, select **V2**.
6. 在“位置”下选择数据工厂的位置。 下拉列表中仅显示支持的位置。 The data stores (for example, Azure Storage and SQL Database) and computes (for example, Azure HDInsight) that your data factory uses can be in other locations and regions.
7. 选择“固定到仪表板”。 
8. 选择**创建**。
9. On the dashboard, refer to the **Deploying Data Factory** tile to see the process status.

    ![Deploying Data Factory Tile](media/tutorial-copy-data-tool/deploying-data-factory.png)
10. 创建完以后，会显示“数据工厂”主页。
   
    ![数据工厂主页](./media/doc-common-process/data-factory-home-page.png)
11. To open the Azure Data Factory user interface (UI) on a separate tab, select the **Author & Monitor** tile. 

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>使用“复制数据”工具创建管道

1. On the **Let's get started** page, select the **Copy Data** title to open the Copy Data tool. 

   ![“复制数据”工具磁贴](./media/doc-common-process/get-started-page.png)
   
2. On the **Properties** page, take the following steps:

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 Under **Task name**, enter **DeltaCopyFromBlobPipeline**.

    b. Under **Task cadence** or **Task schedule**, select **Run regularly on schedule**.

    c. Under **Trigger Type**, select **Tumbling Window**.
    
    d.单击“下一步”。 Under **Recurrence**, enter **15 Minute(s)** . 
    
    e. 选择“**下一步**”。 
    
    数据工厂 UI 将使用指定的任务名称创建一个管道。 

    ![“属性”页](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/copy-data-tool-properties-page.png)
    
3. 在“源数据存储”页上，完成以下步骤：

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 Select  **+ Create new connection**, to add a connection.
    
    ![“源数据存储”页](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page.png)

    b. 从库中选择“Azure Blob 存储”，然后选择“继续”。
    
    ![“源数据存储”页](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-blob.png)

    c. On the **New Linked Service** page, select your storage account from the **Storage account name** list and then select **Finish**.
    
    ![“源数据存储”页](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-linkedservice.png)
    
    d.单击“下一步”。 Select the newly created linked service and then select **Next**. 
    
   ![“源数据存储”页](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-linkedservice.png)

4. 在“选择输入文件或文件夹”页中完成以下步骤：
    
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 Browse and select the **source** folder, and then select **Choose**.
    
    ![选择输入文件或文件夹](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-input-file-folder.png)
    
    b. Under **File loading behavior**, select **Incremental load: LastModifiedDate**.
    
    ![选择输入文件或文件夹](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-loading-behavior.png)
    
    c. Check **Binary copy** and select **Next**.
    
     ![选择输入文件或文件夹](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/check-binary-copy.png)
     
5. On the **Destination data store** page, select **AzureBlobStorage**. This is the same storage account as the source data store. 然后，选择“下一步”。

    ![“目标数据存储”页](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/destination-data-store-page-select-linkedservice.png)
    
6. 在“选择输出文件或文件夹”页中完成以下步骤：
    
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 Browse and select the **destination** folder, and then select **Choose**.
    
    ![选择输出文件或文件夹](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-output-file-folder.png)
    
    b. 选择“**下一步**”。
    
     ![选择输出文件或文件夹](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/click-next-after-output-folder.png)
    
7. 在“设置”页中，选择“下一步”。 

    ![“设置”页](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/settings-page.png)
    
8. On the **Summary** page, review the settings and then select **Next**.

    ![“摘要”页](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/summary-page.png)
    
9. 在“部署”页中，选择“监视”可以监视管道（任务）。

    ![“部署”页](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/deployment-page.png)
    
10. 请注意，界面中已自动选择左侧的“监视”选项卡。 “操作”列中包含用于查看活动运行详细信息以及用于重新运行管道的链接。 Select **Refresh** to refresh the list, and select the **View Activity Runs** link in the **Actions** column. 

    ![Refresh list and select View Activity Runs](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs1.png)

11. There's only one activity (the copy activity) in the pipeline, so you see only one entry. 有关复制操作的详细信息，请选择“操作”列中的“详细信息”链接（眼镜图标）。 

    ![Copy activity is in pipeline](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs2.png)
    
    Because there is no file in the **source** container in your Blob storage account, you will not see any file copied to the **destination** container in your Blob storage account.
    
    ![No file in source container or destination container](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3.png)
    
12. Create an empty text file and name it **file1.txt**. Upload this text file to the **source** container in your storage account. 可以使用各种工具（例如 [Azure 存储资源管理器](https://storageexplorer.com/)）来执行这些任务。   

    ![Create file1.txt and upload to source container](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3-1.png)
    
13. To go back to the **Pipeline Runs** view, select **All Pipeline Runs**, and wait for the same pipeline to be triggered again automatically.  

    ![Select All Pipeline Runs](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs4.png)

14. Select **View Activity Run** for the second pipeline run when you see it. Then review the details in the same way you did for the first pipeline run.  

    ![Select View Activity Run and review details](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs5.png)

    You will that see one file (file1.txt) has been copied from the **source** container to the **destination** container of your Blob storage account.
    
    ![File1.txt has been copied from source container to destination container](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs6.png)
    
15. Create another empty text file and name it **file2.txt**. Upload this text file to the **source** container in your Blob storage account.   
    
16. Repeat steps 13 and 14 for this second text file. You will see that only the new file (file2.txt) has been copied from the **source** container to the **destination** container of your storage account in the next pipeline run.  
    
    ![File2.txt has been copied from source container to destination container](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs7.png)

    You can also verify this by using [Azure Storage Explorer](https://storageexplorer.com/) to scan the files.
    
    ![Scan files using Azure Storage Explorer](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs8.png)

    
## <a name="next-steps"></a>后续步骤
Advance to the following tutorial to learn about transforming data by using an Apache Spark cluster on Azure:

> [!div class="nextstepaction"]
>[Transform data in the cloud by using an Apache Spark cluster](tutorial-transform-data-spark-portal.md)