---
title: 以增量方式复制新的和已更改文件通过复制数据工具基于 LastModifiedDate |Microsoft Docs
description: 创建一个 Azure 数据工厂，然后使用复制数据工具以增量方式加载基于 LastModifiedDate 的新文件。
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
ms.openlocfilehash: 3865bb10346c4a55adbf94a7df225eacf2c11252
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65519135"
---
# <a name="incrementally-copy-new-and-changed-files-based-on-lastmodifieddate-by-using-the-copy-data-tool"></a>以增量方式将通过复制数据工具基于 LastModifiedDate 的新功能和更改文件的复制

在本教程中，您将使用 Azure 门户创建数据工厂。 然后，将使用复制数据工具创建管道，用于以增量方式将新的和已更改文件，复制基于其**LastModifiedDate**从 Azure Blob 存储到 Azure Blob 存储。

通过此操作，ADF 将扫描的源存储中的所有文件、 应用文件筛选器通过其 LastModifiedDate，并将新的和更新文件上次之后仅复制到目标存储。  请注意，是否让 ADF 扫描数量庞大的文件，但仅将一些文件复制到目标，你仍然希望由于文件扫描而引起的长持续时间也较长时间。   

> [!NOTE]
> 如果对 Azure 数据工厂不熟悉，请参阅 [Azure 数据工厂简介](introduction.md)。

在本教程中，您将执行以下任务：

> [!div class="checklist"]
> * 创建数据工厂。
> * 使用“复制数据”工具创建管道。
> * 监视管道和活动运行。

## <a name="prerequisites"></a>必备组件

* **Azure 订阅**：如果还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/)。
* **Azure 存储帐户**：Blob 存储用作_源_并_接收器_数据存储区。 如果没有 Azure 存储帐户，请参阅[创建存储帐户](../storage/common/storage-quickstart-create-account.md)中的说明。

### <a name="create-two-containers-in-blob-storage"></a>在 Blob 存储中创建两个容器

通过执行以下步骤来准备本教程在 Blob 存储。

1. 创建名为的容器**源**。 可以使用各种工具来执行此任务中，如[Azure 存储资源管理器](https://storageexplorer.com/)。

2. 创建名为的容器**目标**。 

## <a name="create-a-data-factory"></a>创建数据工厂

1. 在左侧菜单中，选择“创建资源”   > “数据 + 分析”   > “数据工厂”  ： 
   
   ![在“新建”窗格中选择“数据工厂”](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. 在“新建数据工厂”  页的“名称”下输入 **ADFTutorialDataFactory**  。 
      
     ![新建数据工厂](./media/tutorial-copy-data-tool/new-azure-data-factory.png)
 
   数据工厂的名称必须全局唯一。  可能会收到以下错误消息：
   
   ![新的数据工厂错误消息](./media/tutorial-copy-data-tool/name-not-available-error.png)

   如果收到有关名称值的错误消息，请为数据工厂输入另一名称。 例如，使用名称 _**yourname**_ **ADFTutorialDataFactory**。 有关数据工厂项目的命名规则，请参阅[数据工厂命名规则](naming-rules.md)。
3. 选择 Azure**订阅**在其中将创建新的数据工厂。 
4. 对于“资源组”，请执行以下步骤之一： 
     
    * 选择“使用现有资源组”，并从下拉列表选择现有的资源组。 

    * 选择“新建”，并输入资源组的名称。  
         
    若要了解资源组，请参阅[使用资源组管理 Azure 资源](../azure-resource-manager/resource-group-overview.md)。

5. 下**版本**，选择**V2**。
6. 在“位置”下选择数据工厂的位置。  下拉列表中仅显示支持的位置。 数据工厂使用的数据存储 （例如，Azure 存储和 SQL 数据库） 和计算资源 (例如，Azure HDInsight) 可以在其他位置和区域。
7. 选择“固定到仪表板”  。 
8. 选择“创建”  。
9. 在仪表板，请参阅**部署数据工厂**磁贴，查看进程状态。

    ![部署数据工厂的磁贴](media/tutorial-copy-data-tool/deploying-data-factory.png)
10. 创建完以后，会显示“数据工厂”  主页。
   
    ![数据工厂主页](./media/tutorial-copy-data-tool/data-factory-home-page.png)
11. 若要打开 Azure 数据工厂用户界面 (UI) 的单独选项卡上，选择**创作和监视**磁贴。 

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>使用“复制数据”工具创建管道

1. 上**让我们开始吧**页上，选择**复制数据**标题以打开复制数据工具。 

   ![“复制数据”工具磁贴](./media/tutorial-copy-data-tool/copy-data-tool-tile.png)
   
2. 上**属性**页上，执行以下步骤：

    a. 下**任务名称**，输入**DeltaCopyFromBlobPipeline**。

    b. 下**任务的频率**或**任务计划**，选择**按计划定期运行**。

    c. 下**触发器类型**，选择**翻转窗口**。
    
    d. 下**重复**，输入**15 分钟**。 
    
    e. 选择“**下一步**”。 
    
    数据工厂 UI 将使用指定的任务名称创建一个管道。 

    ![“属性”页](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/copy-data-tool-properties-page.png)
    
3. 在“源数据存储”  页上，完成以下步骤：

    a. 选择 **+ 创建新的连接**，若要添加的连接。
    
    ![“源数据存储”页](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page.png)

    b. 选择**Azure Blob 存储**从库中，并选择**继续**。
    
    ![“源数据存储”页](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-blob.png)

    c. 上**新建链接服务**页上，选择从你的存储帐户**存储帐户名称**列表，然后选择**完成**。
    
    ![“源数据存储”页](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-linkedservice.png)
    
    d. 选择新创建的链接的服务，然后选择**下一步**。 
    
   ![“源数据存储”页](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-linkedservice.png)

4. 在“选择输入文件或文件夹”页中完成以下步骤： 
    
    a. 浏览并选择**源**文件夹，，然后选择**选择**。
    
    ![选择输入文件或文件夹](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-input-file-folder.png)
    
    b. 下**文件加载行为**，选择**增量加载：LastModifiedDate**。
    
    ![选择输入文件或文件夹](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-loading-behavior.png)
    
    c. 检查**二进制副本**，然后选择**下一步**。
    
     ![选择输入文件或文件夹](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/check-binary-copy.png)
     
5. 上**目标数据存储区**页上，选择**AzureBlobStorage**。 这是与源数据存储相同的存储帐户。 然后，选择“下一步”  。

    ![“目标数据存储”页](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/destination-data-store-page-select-linkedservice.png)
    
6. 在“选择输出文件或文件夹”页中完成以下步骤： 
    
    a. 浏览并选择**目标**文件夹，，然后选择**选择**。
    
    ![选择输出文件或文件夹](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-output-file-folder.png)
    
    b. 选择“**下一步**”。
    
     ![选择输出文件或文件夹](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/click-next-after-output-folder.png)
    
7. 在“设置”页中，选择“下一步”。   

    ![“设置”页](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/settings-page.png)
    
8. 上**摘要**页上，查看设置，然后选择**下一步**。

    ![“摘要”页](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/summary-page.png)
    
9. 在“部署”页中，选择“监视”可以监视管道（任务）   。

    ![“部署”页](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/deployment-page.png)
    
10. 请注意，界面中已自动选择左侧的“监视”选项卡。  “操作”列中包含用于查看活动运行详细信息以及用于重新运行管道的链接  。 选择**刷新**以刷新列表，然后选择**查看活动运行**中的链接**操作**列。 

    ![刷新列表并选择查看活动运行](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs1.png)

11. 都只有一个活动 （复制活动） 在管道中，因此您看到只有一个条目。 有关复制操作的详细信息，请选择“操作”列中的“详细信息”链接（眼镜图标）。   

    ![复制活动是管道中](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs2.png)
    
    因为没有在文件**源**Blob 存储帐户中的容器，您将看到任何文件复制到**目标**Blob 存储帐户中的容器。
    
    ![源容器或目标容器中没有文件](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3.png)
    
12. 创建一个空文本文件并将其命名**file1.txt**。 上传到此文本文件**源**存储帐户中的容器。 可以使用各种工具（例如 [Azure 存储资源管理器](https://storageexplorer.com/)）来执行这些任务。   

    ![创建 file1.txt 和上传到源容器](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3-1.png)
    
13. 若要回到**管道运行**视图中，选择**所有管道运行**，并等待相同管道再次自动触发。  

    ![选择所有管道运行](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs4.png)

14. 选择**查看活动运行**进行第二个管道运行时你将看到它。 与你对第一次管道运行相同的方式，然后查看详细信息。  

    ![选择查看活动运行并查看详细信息](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs5.png)

    将其中一个，请参阅文件 (file1.txt) 是否已从复制**源**到容器**目标**Blob 存储帐户的容器。
    
    ![已 File1.txt 从从源容器复制到目标容器](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs6.png)
    
15. 创建另一个空的文本文件并将其命名**file2.txt**。 上传到此文本文件**源**Blob 存储帐户中的容器。   
    
16. 此第二个文本文件重复步骤 13 和 14。 你将看到新文件 (file2.txt) 是否已从复制**源**到容器**目标**的下一个管道运行中的存储帐户的容器。  
    
    ![已 File2.txt 从从源容器复制到目标容器](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs7.png)

    您还可以通过使用验证这[Azure 存储资源管理器](https://storageexplorer.com/)要扫描的文件。
    
    ![扫描文件使用 Azure 存储资源管理器](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs8.png)

    
## <a name="next-steps"></a>后续步骤
请转到以下教程，了解如何在 Azure 上使用 Apache Spark 群集转换数据的：

> [!div class="nextstepaction"]
>[使用 Apache Spark 群集转换云中的数据](tutorial-transform-data-spark-portal.md)