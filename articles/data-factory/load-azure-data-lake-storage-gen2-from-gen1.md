---
title: 将数据从 Azure 数据存储第 1 代复制到第 2 代
description: 使用 Azure 数据工厂将数据从 Azure Data Lake Storage Gen1 复制到 Gen2
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/13/2019
ms.openlocfilehash: f782985d975dd137aa2da48d12757685b47e62b1
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421266"
---
# <a name="copy-data-from-azure-data-lake-storage-gen1-to-gen2-with-azure-data-factory"></a>使用 Azure 数据工厂将数据从 Azure Data Lake Storage Gen1 复制到 Gen2

Azure 数据湖存储 Gen2 是一组专用于大数据分析的功能，内置于[Azure Blob 存储](../storage/blobs/storage-blobs-introduction.md)中。 它可使用文件系统和对象存储范例与数据进行交互。

如果当前使用 Azure 数据存储第 1 代，则可以使用 Azure 数据工厂将数据从数据存储第 1 代复制到第 2 代，从而评估 Azure 数据存储湖存储 Gen2。

Azure 数据工厂是一个完全托管的基于云的数据集成服务。 您可以使用该服务使用来自大量本地和基于云的数据存储的数据填充湖中，并在构建分析解决方案时节省时间。 有关支持的连接器列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

Azure 数据工厂提供可横向扩展的托管数据移动解决方案。 由于数据工厂的横向扩展体系结构，它可以以高吞吐量引入数据。 有关详细信息，请参阅[复制活动性能](copy-activity-performance.md)。

本文介绍如何使用数据工厂复制数据工具将数据从 Azure 数据湖存储 Gen1 复制到 Azure 数据湖存储 Gen2 中。 可以遵循类似步骤，从其他类型的数据存储中复制数据。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请先创建[一个免费帐户](https://azure.microsoft.com/free/)。"
* 其中有数据的 Azure Data Lake Storage Gen1 帐户。
* 启用了数据存储第 2 代的 Azure 存储帐户。 如果没有存储帐户，请[创建一个帐户](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)。

## <a name="create-a-data-factory"></a>创建数据工厂

1. 在左侧菜单中，选择 **"创建资源** > **数据 + 分析** > **数据工厂**"。
   
   !["新建"窗格中的数据工厂选择](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. 在 **"新建数据工厂"** 页上，为下图中显示的字段提供值： 
      
   ![新数据工厂页面](./media/load-azure-data-lake-storage-gen2-from-gen1/new-azure-data-factory.png)
 
    * **名称**：输入 Azure 数据工厂的全局唯一名称。 如果收到错误“数据工厂名称 \"LoadADLSDemo\" 不可用”，请输入不同的数据工厂名称。 例如，使用名称 _**yourname**_**ADFTutorialDataFactory**。 再次创建数据工厂。 有关数据工厂项目的命名规则，请参阅[数据工厂命名规则](naming-rules.md)。
    * **订阅**：选择要在其中创建数据工厂的 Azure 订阅。 
    * **资源组**：从下拉列表中选择现有资源组。 您还可以选择"**创建新**选项"并输入资源组的名称。 若要了解资源组，请参阅[使用资源组管理 Azure 资源](../azure-resource-manager/management/overview.md)。 
    * **版本**：选择“V2”****.
    * **位置**：选择数据工厂所在的位置。 下拉列表中仅显示支持的位置。 数据工厂使用的数据存储可以在其他位置和区域中。 

3. 选择“创建”  。
4. 创建完成后，转到数据工厂。 此时会看到“数据工厂”**** 主页，如下图所示： 
   
   ![数据工厂主页](./media/load-azure-data-lake-storage-gen2-from-gen1/data-factory-home-page.png)

5. 选择 **"作者&监视器"** 磁贴以在单独的选项卡中启动数据集成应用程序。

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>将数据加载到 Azure Data Lake Storage Gen2 中

1. 在"**开始"** 页上，选择 **"复制数据"** 磁贴以启动复制数据工具。 

   ![“复制数据”工具磁贴](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-tile.png)
2. 在 **"属性"** 页上，为**任务名称**字段指定**CopyFromADLSGen1ToGen2。** 选择“下一步”。

    ![“属性”页](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-properties-page.png)
3. 在 **"源数据存储"** 页上，选择 **"创建新连接**"。

    ![“源数据存储”页](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page.png)
    
4. 从连接器库中选择**Azure 数据湖存储第 1 代**，然后选择"**继续**"。
    
    ![源数据存储 Azure Data Lake Storage Gen1 页](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page-adls-gen1.png)
    
5. 在 **"指定 Azure 数据存储湖存储第 1 代连接"页上**，按照以下步骤操作：

   a. 选择你的 Data Lake Storage Gen1 作为帐户名称，并指定或验证**租户**。
  
   b. 选择 **"测试连接**"以验证设置。 然后选择“完成”****。
  
   c. 您将看到已创建新连接。 选择“下一步”。
   
   > [!IMPORTANT]
   > 在本演练中，使用 Azure 资源的托管标识对 Azure 数据湖存储 Gen1 进行身份验证。 要在 Azure 数据湖存储 Gen1 中授予托管标识适当的权限，请按照[这些说明操作](connector-azure-data-lake-store.md#managed-identity)。
   
   ![指定 Azure Data Lake Storage Gen1 帐户](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen1-account.png)
      
6. 在 **"选择输入文件或文件夹**"页上，浏览到要复制的文件夹和文件。 选择文件夹或文件，然后**选择**。

    ![选择输入文件或文件夹](./media/load-azure-data-lake-storage-gen2-from-gen1/choose-input-folder.png)

7. 通过选择递**归文件和****二进制复制**选项来指定复制行为。 选择“下一步”。

    ![指定输出文件夹](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-binary-copy.png)
    
8. 在 **"目标数据存储"** 页上，选择 **"创建新连接** > **Azure 数据湖存储第 2** > 代**继续**"。

    ![“目标数据存储”页](./media/load-azure-data-lake-storage-gen2-from-gen1/destination-data-storage-page.png)

9. 在 **"指定 Azure 数据存储湖存储 Gen2"连接**页上，按照以下步骤操作：

   a. 从 **"存储帐户名称**"下拉列表中选择支持数据存储湖存储 Gen2 的帐户。
   
   b. 选择“完成”**** 以创建连接。 然后选择 **"下一步**"。
   
   ![指定 Azure Data Lake Storage Gen2 帐户](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-account.png)

10. 在 **"选择输出文件或文件夹**"页上，将**从adlsgen1的副本**输入为输出文件夹名称，然后选择 **"下一步**"。 数据工厂在复制期间创建相应的 Azure 数据存储湖存储 Gen2 文件系统和子文件夹（如果它们不存在）。

    ![指定输出文件夹](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-path.png)

11. 在“设置”页上，选择“下一步”以使用默认设置********。

12. 在 **"摘要"** 页上，查看设置，然后选择 **"下一步**"。

    ![“摘要”页](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-summary.png)
13. 在 **"部署"页上**，选择 **"监视器"** 以监视管道。

    ![“部署”页](./media/load-azure-data-lake-storage-gen2-from-gen1/deployment-page.png)
14. 请注意，界面中已自动选择左侧的“监视”选项卡。**** “操作”列中包含用于查看活动运行详细信息以及用于重新运行管道的链接****。

    ![监视管道运行](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-pipeline-runs.png)

15. 若要查看与管道运行关联的活动运行，请选择“操作”列中的“查看活动运行”链接。******** 该管道只包含一个活动（复制活动），因此只显示了一个条目。 若要切换回到管道运行视图，请选择顶部的“管道”链接****。 选择“刷新”可刷新列表。**** 

    ![监视活动运行](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-runs.png)

16. 若要监视每个复制活动的执行详细信息，请在活动监视视图中选择“操作”下的“详细信息”链接（眼镜图标）********。 您可以监视详细信息，例如从源复制到接收器的数据量、数据吞吐量、具有相应持续时间的执行步骤以及使用的配置。

    ![监视活动运行详细信息](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-run-details.png)

17. 验证数据是否复制到 Azure 数据湖存储 Gen2 帐户中。

## <a name="best-practices"></a>最佳做法

要评估从 Azure 数据存储第 1 代升级到 Azure 数据存储湖存储第 2 代，请参阅[将大数据分析解决方案从 Azure 数据存储库第 1 代升级到 Azure 数据存储湖存储第 2 代](../storage/blobs/data-lake-storage-upgrade.md)。 以下各节介绍使用数据工厂将数据升级到数据湖存储第 1 代的最佳做法。

### <a name="data-partition-for-historical-data-copy"></a>历史数据副本的数据分区

- 如果数据存储 Gen1 中的总数据大小小于 30 TB 且文件数小于 100 万，则可以在单个复制活动中复制所有数据。
- 如果要复制的数据量较大，或者希望灵活地分批管理数据迁移并使每个迁移在特定时间范围内完成，请对数据进行分区。 分区还降低了任何意外问题的风险。

使用概念验证来验证端到端解决方案并测试环境中的复制吞吐量。 主要的概念验证步骤： 

1. 创建一个数据工厂管道，具有单个复制活动，以将数据湖存储 Gen1 复制到数据湖存储 Gen2 中的多个 TB，以获得复制性能基准。 从[数据集成单元 （DIUs）](copy-activity-performance-features.md#data-integration-units)开始，为 128。 
2. 根据步骤 1 中的副本吞吐量，计算整个数据迁移所需的估计时间。 
3. （可选）创建控制表并定义文件筛选器以对要迁移的文件进行分区。 对文件进行分区的方法是： 

    - 使用通配符筛选器按文件夹名称或文件夹名称进行分区。 我们建议使用此方法。
    - 按文件上次修改时间进行分区。

### <a name="network-bandwidth-and-storage-io"></a>网络带宽和存储 I/O 

您可以控制数据工厂复制作业的并发性，这些作业从数据存储第 1 代读取数据并将数据写入数据存储 Gen2。 通过这种方式，您可以管理该存储 I/O 上的使用情况，以避免在迁移期间影响数据湖存储 Gen1 上的正常业务工作。

### <a name="permissions"></a>权限 

在数据工厂中，[数据湖存储 Gen1 连接器](connector-azure-data-lake-store.md)支持 Azure 资源身份验证的服务主体和托管标识。 [Data Lake 存储 Gen2 连接器](connector-azure-data-lake-storage.md)支持 Azure 资源身份验证的帐户密钥、服务主体和托管标识。 要使 Data 工厂能够导航和复制所需的所有文件或访问控制列表 （ACL），请为您提供的帐户授予足够高的权限，以便访问、读取或写入所有文件，并在选择时设置 ACL。 在迁移期间授予其超级用户或所有者角色。 

### <a name="preserve-acls-from-data-lake-storage-gen1"></a>保留数据湖存储第 1 代中的 ACL

如果要在从数据存储库第 1 代升级到数据存储库 Gen2 时复制 ACL 和数据文件，请参阅[保留来自数据存储湖存储 Gen1 的 ACL。](connector-azure-data-lake-storage.md#preserve-acls) 

### <a name="incremental-copy"></a>增量复制 

您可以使用多种方法仅加载 Data Lake 存储 Gen1 中的新文件或更新的文件：

- 按分区文件夹或文件名加载新文件或更新文件。 例如 /2019/05/13/*。
- 按上次修改日期加载新文件或更新文件。
- 通过任何第三方工具或解决方案识别新的或更新的文件。 然后通过参数或表或文件将文件或文件夹名称传递到数据工厂管道。 

执行增量加载的正确频率取决于 Azure 数据湖存储 Gen1 中的文件总数以及每次要加载的新文件或更新文件的数量。 

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [复制活动概述](copy-activity-overview.md)
> [Azure 数据存储第 1 代连接器](connector-azure-data-lake-store.md)
> [Azure 数据湖存储 Gen2 连接器](connector-azure-data-lake-storage.md)