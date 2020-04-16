---
title: 数据工具，以增量方式复制新文件并更新文件
description: 创建一个 Azure 数据工厂，然后使用复制数据工具根据 LastModifiedDate 以增量方式加载新文件。
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 3/18/2020
ms.openlocfilehash: 3098ca0d3d5e41c298d3058ffa84fcf129648281
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399488"
---
# <a name="incrementally-copy-new-and-changed-files-based-on-lastmodifieddate-by-using-the-copy-data-tool"></a>使用复制数据工具根据 LastModifiedDate 以增量方式复制新的和已更改的文件

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

在本教程中，你将使用 Azure 门户创建数据工厂。 然后，您将使用"复制数据"工具创建仅从 Azure Blob 存储到 Azure Blob 存储的增量复制新文件和更改文件的管道。 它用于`LastModifiedDate`确定要复制的文件。

完成此处的步骤后，Azure 数据工厂将扫描源存储中的所有文件，通过`LastModifiedDate`应用文件筛选器，并仅复制到目标存储中自上次更新的文件。 请注意，如果数据工厂扫描大量文件，您仍应期望持续时间长。 文件扫描非常耗时，即使复制的数据量减少了。

> [!NOTE]
> 如果对数据工厂不熟悉，请参阅 [Azure 数据工厂简介](introduction.md)。

在本教程中，你将完成以下任务：

> [!div class="checklist"]
> * 创建数据工厂。
> * 使用“复制数据”工具创建管道。
> * 监视管道和活动运行。

## <a name="prerequisites"></a>先决条件

* **Azure 订阅**：如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。
* **Azure 存储帐户**：对源和接收器数据存储使用 Blob 存储。 如果没有 Azure 存储帐户，请按照["创建存储帐户](../storage/common/storage-account-create.md)"中的说明操作。

## <a name="create-two-containers-in-blob-storage"></a>在 Blob 存储中创建两个容器

通过完成以下步骤为本教程准备 Blob 存储：

1. 创建名为 **source** 的容器。 可以使用各种工具执行此任务，如 Azure[存储资源管理器](https://storageexplorer.com/)。

2. 创建名为 **destination** 的容器。

## <a name="create-a-data-factory"></a>创建数据工厂

1. 在左窗格中，选择“创建资源”****。 选择**分析** > **数据工厂**：

   ![选择数据工厂](./media/doc-common-process/new-azure-data-factory-menu.png)

2. 在“新建数据工厂”**** 页的“名称”下输入 **ADFTutorialDataFactory******。

   数据工厂的名称必须全局唯一。 您可能会收到以下错误消息：

   ![名称 不可用错误消息](./media/doc-common-process/name-not-available-error.png)

   如果收到有关名称值的错误消息，请为数据工厂输入另一名称。 例如，使用名称 _**yourname**_**ADFTutorialDataFactory**。 有关数据工厂项目的命名规则，请参阅[数据工厂命名规则](naming-rules.md)。
3. 在 **"订阅"** 下，选择将在其中创建新数据工厂的 Azure 订阅。
4. 在**资源组**下，执行以下步骤之一：

    * 选择 **"使用现有**资源"，然后在列表中选择现有资源组。

    * 选择 **"创建新**"，然后输入资源组的名称。
         
    若要了解资源组，请参阅[使用资源组管理 Azure 资源](../azure-resource-manager/management/overview.md)。

5. 在“版本”下选择“V2”。********
6. 在 **"位置**"下，选择数据工厂的位置。 列表中仅显示受支持的位置。 数据存储（例如，Azure 存储和 Azure SQL 数据库）和计算数据工厂使用的数据可以在其他位置和区域进行计算。
8. 选择“创建”  。
9. 创建数据工厂后，将显示数据工厂主页。
10. 要在单独的选项卡上打开 Azure 数据工厂用户界面 （UI），请选择 **"作者&监视器"** 磁贴：

    ![数据工厂主页](./media/doc-common-process/data-factory-home-page.png)

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>使用“复制数据”工具创建管道

1. 在"**让我们开始"** 页上，选择 **"复制数据**"磁贴以打开"复制数据"工具：

   ![复制数据磁贴](./media/doc-common-process/get-started-page.png)

2. 在“属性”页上执行以下步骤：****

    a. 在“任务名称”下**** 输入 **DeltaCopyFromBlobPipeline**。

    b. 在“任务频率或任务计划”下 **，选择“按计划定期运行”******。

    c. 在 **"触发类型"** 下，选择 **"翻转"窗口**。

    d. 在“重复周期”下输入“15 分钟”。********

    e. 选择“**下一页**”。

    数据工厂创建具有指定任务名称的管道。

    ![复制数据属性页](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/copy-data-tool-properties-page.png)

3. 在**源数据存储**页上，完成以下步骤：

    a. 选择 **"创建新连接**"以添加连接。

    b. 从库中选择**Azure Blob 存储**，然后选择 **"继续**"

    ![选择 Azure 博客存储](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-blob.png)

    c. 在 **"新建链接服务（Azure Blob 存储）"** 页上，从**存储帐户名称**列表中选择存储帐户。 测试连接，然后选择 **"创建**"。

    d. 选择新的链接服务，然后选择 **"下一步**" ：

   ![选择新的链接服务](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-linkedservice.png)

4. 在“选择输入文件或文件夹”页中完成以下步骤：****

    a. 浏览并选择**源**文件夹，然后**选择**。

    ![选择输入文件或文件夹](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-input-file-folder.png)

    b. 在**文件加载行为**下，选择**增量加载：上次修改日期**。

    c. 选择**二进制副本**，然后选择 **"下一步**" ：

     ![选择输入文件或文件夹页](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/check-binary-copy.png)

5. 在 **"目标数据存储"** 页上，选择您创建的**AzureBlob 存储**服务。 这是与源数据存储相同的存储帐户。 然后选择 **"下一步**"。

6. 在“选择输出文件或文件夹”页中完成以下步骤：****

    a. 浏览并选择**目标**文件夹，然后**选择**：

    ![选择输出文件或文件夹页](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-output-file-folder.png)

    b. 选择“**下一页**”。

7. 在“设置”页中，选择“下一步”。********

8. 在 **"摘要"** 页上，查看设置，然后选择 **"下一步**"。

    ![“摘要”页](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/summary-page.png)

9. 在“部署”页中，选择“监视”可以监视管道（任务）********。

    ![“部署”页](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/deployment-page.png)

10. 请注意，界面中已自动选择左侧的“监视”选项卡。**** 应用程序切换到 **"监视器"** 选项卡。您将看到管道的状态。 选择“刷新”可刷新列表。**** 选择 **"管道名称**"下的链接以查看活动运行详细信息或再次运行管道。

    ![刷新列表并查看活动运行详细信息](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs1.png)

11. 该管道只包含一个活动（复制活动），因此只显示了一个条目。 有关复制操作的详细信息，请选择**活动名称**列中**的详细信息**链接（眼镜图标）。 有关属性的详细信息，请参阅[复制活动概述](copy-activity-overview.md)。

    ![复制管道中的活动](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs2.png)

    由于 Blob 存储帐户中的源容器中没有文件，因此您不会看到任何文件复制到帐户中的目标容器：

    ![源容器或目标容器中没有文件](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3.png)

12. 创建空的文本文件，并将其命名为 **file1.txt**。 将此文本文件上传到存储帐户中的 source 容器。 可以使用各种工具来执行这些任务，如[Azure 存储资源管理器](https://storageexplorer.com/)。

    ![创建 file1.txt 并将其上载到源容器](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3-1.png)

13. 要返回**管道运行**视图，请选择 **"所有管道运行**"，然后等待同一管道再次自动触发。  

    ![选择"所有管道运行"](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs4.png)

14. 当第二个管道运行完成时，按照前面提到的相同的步骤来查看活动运行详细信息。  

    您将看到一个文件（file1.txt）已从源容器复制到 Blob 存储帐户的目标容器：

    ![file1.txt 已从源容器复制到目标容器](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs6.png)

15. 创建另一个空的文本文件，并将其命名为 **file2.txt**。 将此文本文件上传到 Blob 存储帐户中的 source 容器。

16. 对第二个文本文件重复步骤 13 和 14。 您将看到，在此管道运行期间，只有新文件 （file2.txt） 从源容器复制到存储帐户的目标容器。  

    还可以验证是否仅复制了一个文件，使用[Azure 存储资源管理器](https://storageexplorer.com/)扫描文件：

    ![使用 Azure 存储资源管理器扫描文件](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs8.png)


## <a name="next-steps"></a>后续步骤
转到以下教程，了解如何在 Azure 上使用 Apache Spark 群集转换数据：

> [!div class="nextstepaction"]
>[使用 Apache Spark 群集转换云中的数据](tutorial-transform-data-spark-portal.md)
