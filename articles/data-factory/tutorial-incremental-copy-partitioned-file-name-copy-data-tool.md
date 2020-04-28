---
title: 基于时间分区文件名增量复制新文件
description: 创建一个 Azure 数据工厂，然后使用复制数据工具仅根据时间分区文件名以增量方式加载新文件。
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 3/17/2020
ms.openlocfilehash: 6cc089a1efc3f5960a8bca8a36063bb1019bbcc6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81409397"
---
# <a name="incrementally-copy-new-files-based-on-time-partitioned-file-name-by-using-the-copy-data-tool"></a>使用复制数据工具仅根据时间分区文件名以增量方式复制新文件

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

在本教程中，我们将使用 Azure 门户创建数据工厂。 然后，使用复制数据工具创建一个管道，该管道根据时间分区文件名以增量方式将新文件从 Azure Blob 存储复制到 Azure Blob 存储。

> [!NOTE]
> 如果对 Azure 数据工厂不熟悉，请参阅 [Azure 数据工厂简介](introduction.md)。

将在本教程中执行以下步骤：

> [!div class="checklist"]
> * 创建数据工厂。
> * 使用“复制数据”工具创建管道。
> * 监视管道和活动运行。

## <a name="prerequisites"></a>先决条件

* **Azure 订阅**：如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。
* **Azure 存储帐户**：将 Blob 存储用作源  和接收器  数据存储。 如果没有 Azure 存储帐户，请参阅[创建存储帐户](../storage/common/storage-account-create.md)中的说明。

### <a name="create-two-containers-in-blob-storage"></a>在 Blob 存储中创建两个容器

执行以下步骤，准备本教程所需的 Blob 存储。

1. 创建名为 **source** 的容器。  在容器中创建一个文件夹路径为**2020/03/17/03** 。 创建空的文本文件，并将其命名为 **file1.txt**。 将 file1 上传到存储帐户中的文件夹路径**源/2020/03/17/03** 。  可以使用各种工具（例如 [Azure 存储资源管理器](https://storageexplorer.com/)）来执行这些任务。

    ![上传文件](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/upload-file.png)

    > [!NOTE]
    > 请根据 UTC 时间调整文件夹名称。  例如，如果当前 UTC 时间为3:38 年3月 17 2020 日上午，则可以按照**source/{Year}/{Month}/{Day}/{Hour}**/规则，创建**源/2020/03/17/03** /文件夹路径。

2. 创建名为 **destination** 的容器。 可以使用各种工具（例如 [Azure 存储资源管理器](https://storageexplorer.com/)）来执行这些任务。

## <a name="create-a-data-factory"></a>创建数据工厂

1. 在左侧菜单中，选择“创建资源”   > “数据 + 分析”   > “数据工厂”  ：

   ![在“新建”窗格中选择“数据工厂”](./media/doc-common-process/new-azure-data-factory-menu.png)

2. 在“新建数据工厂”  页的“名称”下输入 **ADFTutorialDataFactory**  。

    数据工厂的名称必须全局唯一。  可能会收到以下错误消息：

   ![新的数据工厂错误消息](./media/doc-common-process/name-not-available-error.png)

   如果收到有关名称值的错误消息，请为数据工厂输入另一名称。 例如，使用名称 _**yourname**_ **ADFTutorialDataFactory**。 有关数据工厂项目的命名规则，请参阅[数据工厂命名规则](naming-rules.md)。
3. 选择要在其中创建新数据工厂的 Azure **订阅**。
4. 对于“资源组”，请执行以下步骤之一： 

    a. 选择“使用现有资源组”，并从下拉列表选择现有的资源组。 

    b. 选择“新建”，并输入资源组的名称。  
         
    若要了解资源组，请参阅[使用资源组管理 Azure 资源](../azure-resource-manager/management/overview.md)。

5. 在“版本”下选择“V2”作为版本。  
6. 在“位置”下选择数据工厂的位置。  下拉列表中仅显示支持的位置。 数据工厂使用的数据存储（例如，Azure 存储和 SQL 数据库）和计算资源（例如，Azure HDInsight）可以位于其他位置和区域。
7. 选择“创建”  。
8. 创建完以后，会显示“数据工厂”**** 主页。
9. 若要在单独的选项卡中启动 Azure 数据工厂用户界面 (UI)，请选择“创作和监视”磁贴。****

    ![数据工厂主页](./media/doc-common-process/data-factory-home-page.png)


## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>使用“复制数据”工具创建管道

1. 在“开始使用”页中选择“复制数据”标题，启动“复制数据”工具。********

   ![“复制数据”工具磁贴](./media/doc-common-process/get-started-page.png)

2. 在“属性”页上执行以下步骤：****

    a. 在“任务名称”下**** 输入 **DeltaCopyFromBlobPipeline**。

    b. 在“任务频率或任务计划”下 **，选择“按计划定期运行”******。

    c. 在 "**触发器类型**" 下，选择 "**翻转窗口**"。

    d. 在“重复周期”下输入“1 小时”。********

    e. 选择“**下一页**”。

    数据工厂 UI 将使用指定的任务名称创建一个管道。

    ![“属性”页](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/copy-data-tool-properties-page.png)
3. 在“源数据存储”**** 页上，完成以下步骤：

    a. 单击 " **+ 新建连接**" 以添加连接
    
    b. 从库中选择“Azure Blob 存储”，然后选择“继续”。
    
    c. 在 "**新建链接服务（Azure Blob 存储）** " 页上，输入链接服务的名称。 选择 Azure 订阅，并从 "**存储帐户名称**" 列表中选择存储帐户。 测试连接，然后选择 "**创建**"。

    ![“源数据存储”页](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-linkedservice.png)

    d. 在 "**源数据存储**" 页上，选择新创建的链接服务，然后单击 "**下一步**"。

4. 在“选择输入文件或文件夹”页中执行以下步骤：****

    a. 浏览并选择 **source** 容器，然后选择“选择”。****

    ![选择输入文件或文件夹](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-input-file-folder.png)

    b. 在“文件加载行为”下选择“增量加载: 时间分区文件夹/文件名”。********

    c. 将动态文件夹路径编写为**source/{year}/{month}/{day}/{小时}**/，并更改格式，如以下屏幕截图所示。 勾选“二进制副本”，然后单击“下一步”。********

    ![选择输入文件或文件夹](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/check-binary-copy.png)     

5. 在“目标数据存储”**** 页上选择“AzureBlobStorage”****（与数据源存储相同的存储帐户），然后单击“下一步”****。

    ![“目标数据存储”页](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/destination-data-store-page-select-linkedservice.png)
6. 在“选择输出文件或文件夹”页上执行以下步骤：****

    a. 浏览并选择 **destination** 文件夹，然后单击“选择”。****

    b. 将动态文件夹路径编写为 **destination/{年}/{月}/{日}/{小时}/**，并将格式更改如下：

    ![选择输出文件或文件夹](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/output-file-name.png)

    c. 单击“下一步”。 

    ![选择输出文件或文件夹](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/click-next-after-output-folder.png)
7. 在“设置”页中，选择“下一步”。********

8. 在“摘要”**** 页中检查设置，然后选择“下一步”****。

    ![“摘要”页](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/summary-page.png)

9. 在“部署”页中，选择“监视”可以监视管道（任务）********。
    ![“部署”页](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/deployment-page.png)

10. 请注意，界面中已自动选择左侧的“监视”选项卡。****  在自动触发管道后，需等待管道运行（约等待一小时）。 运行时，单击管道名称链接**DeltaCopyFromBlobPipeline**查看活动运行详细信息或重新运行管道。 选择“刷新”可刷新列表。****

    ![监视管道运行](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs1.png)
11. 该管道只包含一个活动（复制活动），因此只显示了一个条目。 调整**源**列和**目标**列的列宽（如有必要）若要显示更多详细信息，您可以看到源文件（file1）已从*source/2020/03/17/03/* to *destination/2020/03/17/03* /具有相同文件名的文件中复制。 

    ![监视管道运行](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs2.png)

    也可使用 Azure 存储资源管理器进行相同的验证 (https://storageexplorer.com/) 以扫描这些文件。

    ![监视管道运行](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs3.png)

12. 将另一个使用新名称的空文本文件创建为 **file2.txt**。 将 file2 文件上传到存储帐户中的文件夹路径**源/2020/03/17/04** 。 可以使用各种工具（例如 [Azure 存储资源管理器](https://storageexplorer.com/)）来执行这些任务。

    > [!NOTE]
    > 你可能知道，必须创建新的文件夹路径。 请根据 UTC 时间调整文件夹名称。  例如，如果当前 UTC 时间为4:20 年3月 17 2020 日上午，则可以按照 **{Year}/{Month}/{Day}/{Hour}/** 的规则创建**源/2020/03/17/04** /的文件夹路径。

13. 若要返回到 "**管道运行**" 视图，请选择 "**所有管道运行**"，并等待在另一个小时后再次自动触发同一管道。  

    ![监视管道运行](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs5.png)

14. 选择第二个管道运行时的新 " **DeltaCopyFromBlobPipeline** " 链接，并执行相同的操作以查看详细信息。 你将看到源文件（file2）已从源 **/2020/03/17/04/** **目标/2020/03/17/17/17** /版本复制，具有相同的文件名。 你还可以使用 Azure 存储资源管理器（https://storageexplorer.com/)扫描**目标**容器中的文件）来验证是否相同。


## <a name="next-steps"></a>后续步骤
请转到下一篇教程，了解如何在 Azure 上使用 Spark 群集转换数据：

> [!div class="nextstepaction"]
>[在云中使用 Spark 群集转换数据](tutorial-transform-data-spark-portal.md)
