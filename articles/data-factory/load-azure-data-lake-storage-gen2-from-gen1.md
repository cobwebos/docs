---
title: 将数据从 Azure Data Lake Storage Gen1 复制到 Gen2
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
ms.openlocfilehash: 5809307ff8e047ebc6120cb5ebf36590f2a2a51a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75444010"
---
# <a name="copy-data-from-azure-data-lake-storage-gen1-to-gen2-with-azure-data-factory"></a>使用 Azure 数据工厂将数据从 Azure Data Lake Storage Gen1 复制到 Gen2

Azure Data Lake Storage Gen2 是一组专用于[Azure Blob 存储](../storage/blobs/storage-blobs-introduction.md)中的大数据分析的功能。 可以通过使用文件系统和对象存储模式，将其用于与数据进行交互。

如果你当前使用 Azure Data Lake Storage Gen1，则可以通过使用 Azure 数据工厂将数据从 Data Lake Storage Gen1 复制到 Gen2 来评估 Azure Data Lake Storage Gen2。

Azure 数据工厂是一个完全托管的基于云的数据集成服务。 您可以使用该服务，通过一组丰富的本地和基于云的数据存储区中的数据填充该 lake，并在生成分析解决方案时节省时间。 有关支持的连接器的列表，请参阅支持的[数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

Azure 数据工厂提供可横向扩展的托管数据移动解决方案。 由于数据工厂的扩展体系结构，它可以实现高吞吐量的数据。 有关详细信息，请参阅[复制活动性能](copy-activity-performance.md)。

本文介绍如何使用数据工厂复制数据工具将数据从 Azure Data Lake Storage Gen1 复制到 Azure Data Lake Storage Gen2 中。 可以遵循类似步骤，从其他类型的数据存储中复制数据。

## <a name="prerequisites"></a>必备组件

* Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。
* 其中有数据的 Azure Data Lake Storage Gen1 帐户。
* 启用 Data Lake Storage Gen2 的 Azure 存储帐户。 如果没有存储帐户，请[创建一个帐户](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)。

## <a name="create-a-data-factory"></a>创建数据工厂

1. 在左侧菜单中，选择 "**创建资源**" > **数据 + 分析** > "**数据工厂**"。
   
   ![新窗格中的数据工厂选择](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. 在 "**新建数据工厂**" 页上，为下图中显示的字段提供值： 
      
   !["新建数据工厂" 页](./media/load-azure-data-lake-storage-gen2-from-gen1/new-azure-data-factory.png)
 
    * **名称**：输入 Azure 数据工厂的全局唯一名称。 如果收到错误“数据工厂名称 \"LoadADLSDemo\" 不可用”，请输入不同的数据工厂名称。 例如，使用名称 _**yourname**_ **ADFTutorialDataFactory**。 再次创建数据工厂。 有关数据工厂项目的命名规则，请参阅[数据工厂命名规则](naming-rules.md)。
    * **订阅**：选择要在其中创建数据工厂的 Azure 订阅。 
    * **资源组**：从下拉列表中选择现有的资源组。 还可以选择 "新建 **" 选项**并输入资源组的名称。 若要了解资源组，请参阅[使用资源组管理 Azure 资源](../azure-resource-manager/management/overview.md)。 
    * **版本**：选择“V2”.
    * **位置**：选择数据工厂所在的位置。 下拉列表中仅显示支持的位置。 数据工厂使用的数据存储可以在其他位置和区域中。 

3. 选择“创建”。
4. 创建完成后，请切换到数据工厂。 此时会看到“数据工厂”主页，如下图所示： 
   
   ![数据工厂主页](./media/load-azure-data-lake-storage-gen2-from-gen1/data-factory-home-page.png)

5. 选择 "**创作 & 监视器**" 磁贴，在单独的选项卡中启动数据集成应用程序。

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>将数据加载到 Azure Data Lake Storage Gen2 中

1. 在 "**开始**" 页上，选择 "**复制数据**" 磁贴以启动 "复制数据" 工具。 

   ![复制数据工具磁贴](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-tile.png)
2. 在 "**属性**" 页上，为 "**任务名称**" 字段指定**CopyFromADLSGen1ToGen2** 。 选择“**下一页**”。

    ![“属性”页](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-properties-page.png)
3. 在 "**源数据存储**" 页上，选择 " **+ 新建连接**"。

    ![“源数据存储”页](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page.png)
    
4. 从连接器库中选择“Azure Data Lake Storage Gen1”，然后选择“继续”。
    
    ![源数据存储 Azure Data Lake Storage Gen1 页](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page-adls-gen1.png)
    
5. 在 "**指定 Azure Data Lake Storage Gen1 连接**" 页上，执行以下步骤：

   a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 选择你的 Data Lake Storage Gen1 作为帐户名称，并指定或验证**租户**。
  
   b.保留“数据库类型”设置，即设置为“共享”。 选择 "**测试连接**" 以验证设置。 然后选择“完成”。
  
   c. 你会看到已创建新连接。 选择“**下一页**”。
   
   > [!IMPORTANT]
   > 在本演练中，将使用 Azure 资源的托管标识对 Azure Data Lake Storage Gen1 进行身份验证。 若要在 Azure Data Lake Storage Gen1 中向托管标识授予适当的权限，请遵循[这些说明](connector-azure-data-lake-store.md#managed-identity)。
   
   ![指定 Azure Data Lake Storage Gen1 帐户](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen1-account.png)
      
6. 在 "**选择输入文件或文件夹**" 页上，浏览到要复制的文件夹和文件。 选择文件夹或文件，然后选择 "**选择**"。

    ![选择输入文件或文件夹](./media/load-azure-data-lake-storage-gen2-from-gen1/choose-input-folder.png)

7. 通过选择 "**以递归方式复制文件**" 和 "**二进制复制**" 选项来指定复制行为。 选择“**下一页**”。

    ![指定输出文件夹](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-binary-copy.png)
    
8. 在 "**目标数据存储**" 页上，选择 " **+ 创建新连接**" > **Azure Data Lake Storage Gen2** > **继续**。

    ![“目标数据存储”页](./media/load-azure-data-lake-storage-gen2-from-gen1/destination-data-storage-page.png)

9. 在 "**指定 Azure Data Lake Storage Gen2 连接**" 页上，执行以下步骤：

   a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 从 "**存储帐户名称**" 下拉列表中选择 Data Lake Storage Gen2 支持的帐户。
   
   b.保留“数据库类型”设置，即设置为“共享”。 选择“完成”以创建连接。 然后，选择“下一步”。
   
   ![指定 Azure Data Lake Storage Gen2 帐户](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-account.png)

10. 在 "**选择输出文件或文件夹**" 页上，输入**copyfromadlsgen1**作为输出文件夹名称，然后选择 "**下一步**"。 如果文件系统和子文件夹不存在，数据工厂会在复制过程中创建相应的 Azure Data Lake Storage Gen2 文件系统和子文件夹。

    ![指定输出文件夹](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-path.png)

11. 在“设置”页上，选择“下一步”以使用默认设置。

12. 在 "**摘要**" 页上，查看设置，然后选择 "**下一步**"。

    ![“摘要”页](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-summary.png)
13. 在 "**部署" 页**上，选择 "**监视器**" 以监视管道。

    ![“部署”页](./media/load-azure-data-lake-storage-gen2-from-gen1/deployment-page.png)
14. 请注意，界面中已自动选择左侧的“监视”选项卡。 “操作”列中包含用于查看活动运行详细信息以及用于重新运行管道的链接。

    ![监视管道运行](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-pipeline-runs.png)

15. 若要查看与管道运行关联的活动运行，请选择“操作”列中的“查看活动运行”链接。 该管道只包含一个活动（复制活动），因此只显示了一个条目。 若要切换回到管道运行视图，请选择顶部的“管道”链接。 选择“刷新”可刷新列表。 

    ![监视活动运行](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-runs.png)

16. 若要监视每个复制活动的执行详细信息，请在活动监视视图中选择“操作”下的“详细信息”链接（眼镜图标）。 可以监视详细信息，如从源复制到接收器的数据量、数据吞吐量、具有相应持续时间的执行步骤和使用的配置。

    ![监视活动运行详细信息](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-run-details.png)

17. 验证数据是否已复制到 Azure Data Lake Storage Gen2 帐户中。

## <a name="best-practices"></a>最佳实践

若要评估从 Azure Data Lake Storage Gen1 到一般 Azure Data Lake Storage Gen2 的升级，请参阅将[大数据分析解决方案从 Azure Data Lake Storage Gen1 升级到 Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-upgrade.md)。 以下部分介绍了如何使用数据工厂将数据从 Data Lake Storage Gen1 升级到 Data Lake Storage Gen2 的最佳实践。

### <a name="data-partition-for-historical-data-copy"></a>用于历史数据复制的数据分区

- 如果 Data Lake Storage Gen1 中的总数据大小小于 30 TB，并且文件数小于1000000，则可以在单个复制活动运行中复制所有数据。
- 如果要复制的数据量较大，或者您希望灵活地按批管理数据迁移，并使每个数据迁移在特定时间范围内完成，请对数据进行分区。 分区还降低了任何意外问题的风险。

使用概念证明来验证端到端解决方案并在你的环境中测试复制吞吐量。 主要概念证明步骤： 

1. 使用单个复制活动创建一个数据工厂管道，以便将多 Tb 数据从 Data Lake Storage Gen1 复制到 Data Lake Storage Gen2 以获取复制性能基线。 从[数据集成单元（DIUs）](copy-activity-performance.md#data-integration-units)开始，128。 
2. 根据您在步骤1中获取的复制吞吐量，计算整个数据迁移所需的估计时间。 
3. 可有可无创建一个控制表，并定义用于对要迁移的文件进行分区的文件筛选器。 对文件进行分区的方式如下： 

    - 使用通配符筛选器按文件夹名称或文件夹名称进行分区。 我们建议使用此方法。
    - 按文件的上次修改时间进行分区。

### <a name="network-bandwidth-and-storage-io"></a>网络带宽和存储 i/o 

可以控制从 Data Lake Storage Gen1 读取数据并将数据写入 Data Lake Storage Gen2 的数据工厂复制作业的并发性。 通过这种方式，你可以管理该存储 i/o 上的使用情况，以避免在迁移期间影响 Data Lake Storage Gen1 的正常业务工作。

### <a name="permissions"></a>权限 

在数据工厂中， [Data Lake Storage Gen1 连接器](connector-azure-data-lake-store.md)支持 Azure 资源身份验证的服务主体和托管标识。 [Data Lake Storage Gen2 连接器](connector-azure-data-lake-storage.md)支持 Azure 资源身份验证的帐户密钥、服务主体和托管标识。 若要使数据工厂能够导航并复制所有需要的文件或访问控制列表（Acl），请为提供的用于访问、读取或写入所有文件的帐户授予足够高的权限，并在选择的情况下设置 Acl。 在迁移期间向其授予超级用户或所有者角色。 

### <a name="preserve-acls-from-data-lake-storage-gen1"></a>保留 Data Lake Storage Gen1 中的 Acl

如果要在从 Data Lake Storage Gen1 升级到 Data Lake Storage Gen2 时，将 Acl 与数据文件一起复制，请参阅[保留 Data Lake Storage Gen1 中的 acl](connector-azure-data-lake-storage.md#preserve-acls-from-data-lake-storage-gen1)。 

### <a name="incremental-copy"></a>增量复制 

您可以使用多种方法仅加载 Data Lake Storage Gen1 的新文件或已更新的文件：

- 按时间分区的文件夹或文件名加载新的或更新的文件。 例如/2019/05/13/*。
- 通过 LastModifiedDate 加载新的或更新的文件。
- 使用任何第三方工具或解决方案标识新文件或更新的文件。 然后通过参数或表或文件将文件或文件夹名称传递到数据工厂管道。 

执行增量加载的正确频率取决于 Azure Data Lake Storage Gen1 中的文件总数以及每次加载的新文件或已更新文件的数量。 

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [复制活动概述](copy-activity-overview.md)
> [Azure Data Lake Storage Gen1 连接器](connector-azure-data-lake-store.md)
> [Azure Data Lake Storage Gen2 连接器](connector-azure-data-lake-storage.md)