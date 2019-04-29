---
title: 仅使用 Azure 数据工厂以增量方式将新文件复制基于时间分区的文件名称 |Microsoft Docs
description: 创建一个 Azure 数据工厂，然后使用复制数据工具以增量方式加载仅基于时间分区的文件名称的新文件。
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
ms.date: 1/24/2019
ms.openlocfilehash: c89764d746f07e6100b1f250d4c107bb700fe014
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61098356"
---
# <a name="incrementally-copy-new-files-based-on-time-partitioned-file-name-by-using-the-copy-data-tool"></a>以增量方式复制新文件通过复制数据工具基于时间分区的文件名称

在本教程中，我们将使用 Azure 门户创建数据工厂。 然后，使用复制数据工具创建的管道，以增量方式将基于从 Azure Blob 存储到 Azure Blob 存储的时间分区的文件名称的新文件复制。 

> [!NOTE]
> 如果对 Azure 数据工厂不熟悉，请参阅 [Azure 数据工厂简介](introduction.md)。

将在本教程中执行以下步骤：

> [!div class="checklist"]
> * 创建数据工厂。
> * 使用“复制数据”工具创建管道。
> * 监视管道和活动运行。

## <a name="prerequisites"></a>必备组件

* **Azure 订阅**：如果还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/)。
* **Azure 存储帐户**：Blob 存储用作_源_并_接收器_数据存储区。 如果没有 Azure 存储帐户，请参阅[创建存储帐户](../storage/common/storage-quickstart-create-account.md)中的说明。

### <a name="create-two-containers-in-blob-storage"></a>在 Blob 存储中创建两个容器

通过执行以下步骤来准备本教程在 Blob 存储。

1. 创建名为的容器**源**。  创建文件夹路径用作**2019年/02/26/14**在容器中。 创建一个空的文本文件，并将其作为命名**file1.txt**。 上载到文件夹路径 file1.txt**源/2019年/02/26/14**存储帐户中。  可以使用各种工具（例如 [Azure 存储资源管理器](https://storageexplorer.com/)）来执行这些任务。
    
    ![上传文件](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/upload-file.png)
    
    > [!NOTE]
    > 请调整与 UTC 时间的文件夹名称。  例如，如果当前的 UTC 时间是下午 2:03 于 2019 年 2 月 26 日，可以创建文件夹路径用作**源/2019年/02/26/14/** 的规则**源 / {Year} / {Month} / {Day} / {Hour} /**。

2. 创建名为的容器**目标**。 可以使用各种工具（例如 [Azure 存储资源管理器](https://storageexplorer.com/)）来执行这些任务。

## <a name="create-a-data-factory"></a>创建数据工厂

1. 在左侧菜单中，选择“创建资源” > “数据 + 分析” > “数据工厂”： 
   
   ![在“新建”窗格中选择“数据工厂”](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

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

5. 在“版本”下选择“V2”作为版本。
6. 在“位置”下选择数据工厂的位置。 下拉列表中仅显示支持的位置。 数据工厂使用的数据存储（例如，Azure 存储和 SQL 数据库）和计算资源（例如，Azure HDInsight）可以位于其他位置和区域。
7. 选择“固定到仪表板”。 
8. 选择“创建”。
9. 在仪表板中，“部署数据工厂”磁贴显示进程状态。

    ![“部署数据工厂”磁贴](media/tutorial-copy-data-tool/deploying-data-factory.png)
10. 创建完以后，会显示“数据工厂”主页。
   
    ![数据工厂主页](./media/tutorial-copy-data-tool/data-factory-home-page.png)
11. 若要在单独的选项卡中启动 Azure 数据工厂用户界面 (UI)，请选择“创作和监视”磁贴。 

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>使用“复制数据”工具创建管道

1. 上**让我们开始吧**页上，选择**复制数据**标题以启动复制数据工具。 

   ![“复制数据”工具磁贴](./media/tutorial-copy-data-tool/copy-data-tool-tile.png)
   
2. 上**属性**页上，执行以下步骤：

    a. 下**任务名称**，输入**DeltaCopyFromBlobPipeline**。

    b. 下**任务的频率或任务计划**，选择**按计划定期运行**。

    c. 下**触发类型**，选择**翻转窗口**。
    
    d. 下**重复**，输入**1 小时内**。 
    
    e. 选择“**下一步**”。 
    
    数据工厂 UI 将使用指定的任务名称创建一个管道。 

    ![“属性”页](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/copy-data-tool-properties-page.png)
3. 在“源数据存储”页上，完成以下步骤：

    a. 单击 **+ 创建新的连接**，若要添加的连接。

    ![“源数据存储”页](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page.png)
    
    b. 选择**Azure Blob 存储**从库中，然后单击**继续**。

    ![“源数据存储”页](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-select-blob.png)
    
    c. 上**新建链接服务**页上，选择从你的存储帐户**存储帐户名称**列表，，然后单击**完成**。
    
    ![“源数据存储”页](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-linkedservice.png)
    
    d. 选择新创建的链接的服务，然后单击**下一步**。 
    
   ![“源数据存储”页](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-select-linkedservice.png)
4. 在“选择输入文件或文件夹”页中执行以下步骤：
    
    a. 浏览并选择**源**容器，然后选择**选择**。
    
    ![选择输入文件或文件夹](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-input-file-folder.png)
    
    b. 下**文件加载行为**，选择**增量加载： 时间分区文件夹/文件名称**。
    
    ![选择输入文件或文件夹](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-loading-behavior.png)
    
    c. 编写动态文件夹路径作为**源 / {year} / {month} / {day} / {hour} /**，并更改格式，按如下操作：
    
    ![选择输入文件或文件夹](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/input-file-name.png)
    
    d. 检查**二进制副本**然后单击**下一步**。
    
    ![选择输入文件或文件夹](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/check-binary-copy.png)     
5. 上**目标数据存储区**页上，选择**AzureBlobStorage**，该服务是相同的存储帐户作为数据源存储，然后依次**下一步**。

    ![“目标数据存储”页](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/destination-data-store-page-select-linkedservice.png) 
6. 上**选择输出文件或文件夹**页上，执行以下步骤：
    
    a. 浏览并选择**目标**文件夹，然后单击**选择**。
    
    ![选择输出文件或文件夹](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-output-file-folder.png)   
    
    b. 编写动态文件夹路径作为**源 / {year} / {month} / {day} / {hour} /**，并更改格式，按如下操作：
    
    ![选择输出文件或文件夹](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/input-file-name2.png)    
    
    c. 单击“下一步”。
    
    ![选择输出文件或文件夹](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/click-next-after-output-folder.png)  
7. 在“设置”页中，选择“下一步”。 

    ![“设置”页](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/settings-page.png)  
8. 在“摘要”页中检查设置，然后选择“下一步”。

    ![“摘要”页](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/summary-page.png)
    
9. 在“部署”页中，选择“监视”可以监视管道（任务）。
    ![部署页](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/deployment-page.png)
    
10. 请注意，界面中已自动选择左侧的“监视”选项卡。  你需要等待运行时自动触发管道 (有关在一小时后)。  在运行时**操作**列包含用于查看活动运行详细信息，以及重新运行管道的链接。 选择**刷新**以刷新列表，然后选择**查看活动运行**中的链接**操作**列。 

    ![监视管道运行](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs1.png)
11. 该管道只包含一个活动（复制活动），因此只显示了一个条目。 您可以看到源文件 (file1.txt) 是否已从复制**源/2019年/02/26/14/** 到**目标/2019年/02/26/14/** 具有相同的文件名称。  

    ![监视管道运行](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs2.png)
    
    你还可以通过使用 Azure 存储资源管理器验证相同 (https://storageexplorer.com/)要扫描的文件。
    
    ![监视管道运行](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs3.png)
12. 使用与新名称创建另一个空的文本文件**file2.txt**。 File2.txt 文件上传到文件夹路径**源/2019年/02/26/15**存储帐户中。   可以使用各种工具（例如 [Azure 存储资源管理器](https://storageexplorer.com/)）来执行这些任务。   
    
    ![监视管道运行](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs4.png)
    
    > [!NOTE]
    > 您可能会注意需创建新的文件夹路径。 请调整与 UTC 时间的文件夹名称。  例如，如果当前的 UTC 时间为下午 3:20 于 2019 年 2 月 26 日，您可以创建文件夹路径作为**源/2019年/02/26/15/** 的规则 **{Year} / {Month} / {Day} / {Hour} /**。
    
13. 若要回到**管道运行**视图中，选择**所有管道运行**，并等待相同管道再次触发另一一小时后自动。  

    ![监视管道运行](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs5.png)

14. 选择**查看活动运行**对于第二个管道运行时，它出现，并执行相同操作以查看详细信息。  

    ![监视管道运行](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs6.png)
    
    您可以看到源文件 (file2.txt) 是否已从复制**源/2019年/02/26/15/** 到**目标/2019年/02/26/15/** 具有相同的文件名称。
    
    ![监视管道运行](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs7.png) 
    
    你还可以通过使用 Azure 存储资源管理器验证相同 (https://storageexplorer.com/)中的文件进行扫描**目标**容器
    
    ![监视管道运行](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs8.png)

    
## <a name="next-steps"></a>后续步骤
请转到下一篇教程，了解如何在 Azure 上使用 Spark 群集转换数据：

> [!div class="nextstepaction"]
>[在云中使用 Spark 群集转换数据](tutorial-transform-data-spark-portal.md)