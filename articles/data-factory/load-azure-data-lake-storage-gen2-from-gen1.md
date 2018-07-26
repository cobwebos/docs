---
title: 使用 Azure 数据工厂将数据从 Azure Data Lake Storage Gen1 复制到 Gen2（预览版）
description: 使用 Azure 数据工厂将数据从 Azure Data Lake Storage Gen1 复制到 Gen2（预览版）
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: jingwang
ms.openlocfilehash: a160c47e12db3c4ef9cefc5cd70293468ddf8234
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2018
ms.locfileid: "39011355"
---
# <a name="copy-data-from-azure-data-lake-storage-gen1-to-gen2-preview-with-azure-data-factory"></a>使用 Azure 数据工厂将数据从 Azure Data Lake Storage Gen1 复制到 Gen2（预览版）

[Azure Data Lake Storage Gen2（预览版）](../storage/data-lake-storage/introduction.md)向 Azure Blob 存储添加了新的协议，其中带有分层文件系统命名空间和安全功能，使得分析框架能更加轻松地连接到持久存储层。 在 Data Lake Storage Gen2（预览版）中，在添加文件系统接口优点的同时，还保留了对象存储的所有功能。

如果当前正在使用 Azure Data Lake Storage Gen1，则可以使用 Azure 数据工厂将数据从 Data Lake Storage Gen1 复制到 Gen2，以便评估 Gen2 的新功能。

Azure 数据工厂是一个完全托管的基于云的数据集成服务。 通过该服务，可使用丰富的本地数据存储和基于云的数据存储中的数据填充数据湖，并更快速地生成分析解决方案。 若要查看受支持的连接器的详细列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

Azure 数据工厂提供可横向扩展的托管数据移动解决方案。 得益于 ADF 的横向扩展体系结构，它能以较高的吞吐量引入数据。 有关详细信息，请参阅[复制活动性能](copy-activity-performance.md)。

本文介绍如何使用数据工厂复制数据工具将数据从 _Azure Data Lake Storage Gen1_ 复制到 _Azure Data Lake Storage Gen2_。 可以遵循类似步骤，从其他类型的数据存储中复制数据。

## <a name="prerequisites"></a>先决条件

* Azure 订阅：如果没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/)。
* 其中有数据的 Azure Data Lake Storage Gen1 帐户。
* 已启用 Data Lake Storage Gen2 的 Azure 存储帐户：如果没有存储帐户，请单击[此处](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)进行创建。

## <a name="create-a-data-factory"></a>创建数据工厂

1. 在左侧菜单中，选择“新建” > “数据 + 分析” > “数据工厂”：
   
   ![新建数据工厂](./media/load-azure-data-lake-storage-gen2-from-gen1/new-azure-data-factory-menu.png)
2. 在“新建数据工厂”页中，为下图中所示的字段提供值： 
      
   ![“新建数据工厂”页](./media/load-azure-data-lake-storage-gen2-from-gen1/new-azure-data-factory.png)
 
    * **名称**：输入 Azure 数据工厂的全局唯一名称。 如果收到错误“数据工厂名称 \"LoadADLSDemo\" 不可用”，请输入不同的数据工厂名称。 例如，可以使用名称 _**yourname**_**ADFTutorialDataFactory**。 请重试创建数据工厂。 有关数据工厂项目的命名规则，请参阅[数据工厂 - 命名规则](naming-rules.md)。
    * **订阅**：选择要在其中创建数据工厂的 Azure 订阅。 
    * **资源组**：从下拉列表中选择现有资源组，或选择“新建”选项并输入资源组的名称。 若要了解有关资源组的详细信息，请参阅 [使用资源组管理 Azure 资源](../azure-resource-manager/resource-group-overview.md)。  
    * **版本**：选择“V2”.
    * **位置**：选择数据工厂所在的位置。 下拉列表中仅显示支持的位置。 数据工厂使用的数据存储可以在其他位置和区域中。 

3. 选择**创建**。
4. 创建操作完成后，请转到数据工厂。 此时会看到“数据工厂”主页，如下图所示： 
   
   ![数据工厂主页](./media/load-azure-data-lake-storage-gen2-from-gen1/data-factory-home-page.png)

   选择“创作和监视”磁贴，在单独的选项卡中启动数据集成应用程序。

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>将数据加载到 Azure Data Lake Storage Gen2 中

1. 在“入门”页中，单击“复制数据”磁贴启动“复制数据”工具： 

   ![“复制数据”工具磁贴](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-tile.png)
2. 在“属性”页中，为“任务名称”字段指定“CopyFromADLSGen1ToGen2”，然后选择“下一步”：

    ![“属性”页](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-properties-page.png)
3. 在“源数据存储”页面，单击“+ 创建新连接”：

    ![“源数据存储”页](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page.png)
    
    从连接器库中选择“Azure Data Lake Storage Gen1”，然后选择“继续”
    
    ![源数据存储 Azure Data Lake Storage Gen1 页](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page-adls-gen1.png)
    
4. 在“指定 Azure Data Lake Storage Gen1 连接”页中，执行以下步骤：
   1. 选择你的 Data Lake Storage Gen1 作为帐户名称。
   2. 指定或验证**租户**，然后选择“完成”。
   3. 选择“**下一步**”。
   
   > [!IMPORTANT]
   > 在此演练中，使用_托管服务标识_进行 Data Lake Storage Gen1 身份验证。 请务必按照[这些说明](connector-azure-data-lake-store.md#using-managed-service-identity-authentication)授予 MSI 在 Azure Data Lake Storage Gen1 中的适当权限。
   
   ![指定 Azure Data Lake Storage Gen1 帐户](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen1-account.png)
   
   4. 随即会显示新创建的连接。 选择“**下一步**”。
   
5. 在“选择输入文件或文件夹”页上，浏览到要复制的文件夹和文件。 选择文件夹/文件，选择“选择”：

    ![选择输入文件或文件夹](./media/load-azure-data-lake-storage-gen2-from-gen1/choose-input-folder.png)

6. 选中“以递归方式复制文件”和“以二进制方式复制”选项，指定复制行为。 选择“下一步”：

    ![指定输出文件夹](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-binary-copy.png)
    
7. 在“目标数据存储”页，单击“+ 创建新连接”，然后选择“Azure Data Lake Storage Gen2 (预览版)”，并选择“继续”：

    ![“目标数据存储”页](./media/load-azure-data-lake-storage-gen2-from-gen1/destination-data-storage-page.png)

8. 在“指定 Azure Data Lake Storage Gen2 连接”页中，执行以下步骤：

   1. 从“存储帐户名称”下拉列表中选择能使用 Data Lake Storage Gen2 的帐户。
   2. 选择“**下一步**”。
   
   ![指定 Azure Data Lake Storage Gen2 帐户](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-account.png)

9. 在“选择输出文件或文件夹”页上，输入 **copyfromadlsgen1** 作为输出文件夹名称，然后选择“下一步”： 

    ![指定输出文件夹](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-path.png)

10. 在“设置”页中选择“下一步”，以便使用默认设置。

11. 在“摘要”页中检查设置，然后选择“下一步”：

    ![“摘要”页](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-summary.png)
12. 在“部署”页中选择“监视”，来监视管道：

    ![“部署”页](./media/load-azure-data-lake-storage-gen2-from-gen1/deployment-page.png)
13. 请注意，界面中已自动选择左侧的“监视”选项卡。 “操作”列中包含用于查看活动运行详细信息以及用于重新运行管道的链接：

    ![监视管道运行](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-pipeline-runs.png)

14. 若要查看与管道运行关联的活动运行，请选择“操作”列中的“查看活动运行”链接。 该管道只包含一个活动（复制活动），因此只显示了一个条目。 若要切换回到管道运行视图，请选择顶部的“管道”链接。 选择“刷新”可刷新列表。 

    ![监视活动运行](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-runs.png)

15. 若要监视每个复制活动的执行详细信息，请在活动监视视图中选择“操作”下的“详细信息”链接（眼镜图标）。 可以监视详细信息，如从源复制到接收器的数据量、吞吐量、执行步骤以及相应的持续时间和使用的配置：

    ![监视活动运行详细信息](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-run-details.png)

16. 验证数据是否已复制到 Data Lake Storage Gen2 帐户。

## <a name="best-practices"></a>最佳实践

如果要从基于文件的数据存储复制大量数据，建议：

- 将文件分成每个 10TB 到 30TB 的文件集。
- 请勿触发过多并发复制，以便避免受到来自源或接收器数据存储的限制。 可先进行一个复制操作并监视吞吐量，然后按需逐渐增加。

## <a name="next-steps"></a>后续步骤

* [复制活动概述](copy-activity-overview.md)
* [Azure Data Lake Storage Gen2 连接器](connector-azure-data-lake-storage.md)