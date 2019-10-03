---
title: 使用 Azure 数据工厂将数据从 Azure Data Lake Storage Gen1 复制到 Gen2
description: 使用 Azure 数据工厂将数据从 Azure Data Lake Storage Gen1 复制到 Gen2
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: jingwang
ms.openlocfilehash: d13dad6e87bd6c821b497138ad75d7ae2b9a052d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67068975"
---
# <a name="copy-data-from-azure-data-lake-storage-gen1-to-gen2-with-azure-data-factory"></a>使用 Azure 数据工厂将数据从 Azure Data Lake Storage Gen1 复制到 Gen2

Azure 数据湖存储第 2 代是一套功能专用于内置的大数据分析[Azure Blob 存储](../storage/blobs/storage-blobs-introduction.md)。 可用于通过使用这两种文件系统和对象存储范例接口与你的数据。

如果当前使用 Azure 数据湖存储 Gen1，您可以通过将数据从数据湖存储 Gen1 复制到第 2 代，通过使用 Azure 数据工厂来评估 Azure 数据湖存储第 2 代。

Azure 数据工厂是一个完全托管的基于云的数据集成服务。 可以使用该服务来填充 lake 使用一套丰富的内部部署中的数据和基于云的数据将存储并保存生成分析解决方案时的时间。 有关支持的连接器的列表，请参阅的表[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。

Azure 数据工厂提供可横向扩展的托管数据移动解决方案。 由于数据工厂的向外扩展体系结构，它可以引入数据进行高吞吐量。 有关详细信息，请参阅[将复制活动性能](copy-activity-performance.md)。

本文介绍如何使用数据工厂复制数据工具将数据从 Azure 数据湖存储 Gen1 复制到 Azure 数据湖存储第 2 代。 可以遵循类似步骤，从其他类型的数据存储中复制数据。

## <a name="prerequisites"></a>必备组件

* Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。
* 其中有数据的 Azure Data Lake Storage Gen1 帐户。
* 数据湖存储第 2 代启用了 azure 存储帐户。 如果没有存储帐户[创建一个帐户](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)。

## <a name="create-a-data-factory"></a>创建数据工厂

1. 在左侧菜单中，选择**创建资源** > **数据 + 分析** > **数据工厂**。
   
   ![在新窗格中选择数据工厂](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. 上**新建数据工厂**页上，在下图中所示的字段提供值： 
      
   ![新建数据工厂页](./media/load-azure-data-lake-storage-gen2-from-gen1/new-azure-data-factory.png)
 
    * **名称**：输入 Azure 数据工厂的全局唯一名称。 如果收到错误“数据工厂名称 \"LoadADLSDemo\" 不可用”，请输入不同的数据工厂名称。 例如，使用名称 _**yourname**_ **ADFTutorialDataFactory**。 重新创建数据工厂。 有关数据工厂项目的命名规则，请参阅[数据工厂命名规则](naming-rules.md)。
    * **订阅**：选择要在其中创建数据工厂的 Azure 订阅。 
    * **资源组**：从下拉列表中选择现有资源组。 您还可以选择**新建**选项并输入资源组的名称。 若要了解资源组，请参阅[使用资源组管理 Azure 资源](../azure-resource-manager/resource-group-overview.md)。 
    * **版本**：选择“V2”  。
    * **位置**：选择数据工厂的位置。 下拉列表中仅显示支持的位置。 数据工厂使用的数据存储可以在其他位置和区域中。 

3. 选择“创建”  。
4. 创建完以后，请转到数据工厂。 此时会看到“数据工厂”  主页，如下图所示： 
   
   ![数据工厂主页](./media/load-azure-data-lake-storage-gen2-from-gen1/data-factory-home-page.png)

5. 选择**创作和监视**磁贴，启动单独的选项卡中的数据集成应用程序。

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>将数据加载到 Azure Data Lake Storage Gen2 中

1. 上**开始**页上，选择**复制数据**磁贴以启动复制数据工具。 

   ![复制数据工具磁贴](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-tile.png)
2. 上**属性**页上，指定**CopyFromADLSGen1ToGen2**有关**任务名称**字段。 选择“**下一步**”。

    ![“属性”页](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-properties-page.png)
3. 上**源数据存储区**页上，选择 **+ 创建新的连接**。

    ![“源数据存储”页](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page.png)
    
4. 选择**Azure 数据湖存储 Gen1**从连接器库，然后选择**继续**。
    
    ![源数据存储 Azure Data Lake Storage Gen1 页](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page-adls-gen1.png)
    
5. 上**指定 Azure 数据湖存储 Gen1 连接**页上，执行以下步骤：

   a. 选择你的 Data Lake Storage Gen1 作为帐户名称，并指定或验证**租户**。
  
   b. 选择**测试连接**对设置进行验证。 然后选择“完成”。 
  
   c. 可以看到已创建新的连接。 选择“**下一步**”。
   
   > [!IMPORTANT]
   > 在本演练中，使用 Azure 资源的托管的标识进行身份验证在 Azure 数据湖存储 Gen1。 若要在 Azure 数据湖存储 Gen1 适当的权限授予托管的标识，请按照[这些说明](connector-azure-data-lake-store.md#managed-identity)。
   
   ![指定 Azure Data Lake Storage Gen1 帐户](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen1-account.png)
      
6. 上**选择输入的文件或文件夹**页上，浏览到的文件夹和你想要复制的文件。 选择文件夹或文件，然后选择**选择**。

    ![选择输入文件或文件夹](./media/load-azure-data-lake-storage-gen2-from-gen1/choose-input-folder.png)

7. 通过选择来指定复制行为**以递归方式复制文件**并**二进制副本**选项。 选择“**下一步**”。

    ![指定输出文件夹](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-binary-copy.png)
    
8. 上**目标数据存储区**页上，选择 **+ 创建新的连接** > **Azure 数据湖存储第 2 代** >  **继续**。

    ![“目标数据存储”页](./media/load-azure-data-lake-storage-gen2-from-gen1/destination-data-storage-page.png)

9. 上**指定 Azure 数据湖存储第 2 代连接**页上，执行以下步骤：

   a. 选择从支持帐户的数据湖存储第 2 代**存储帐户名称**下拉列表。
   
   b. 选择“完成”  以创建连接。 然后，选择“下一步”  。
   
   ![指定 Azure Data Lake Storage Gen2 帐户](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-account.png)

10. 上**选择输出文件或文件夹**页上，输入**copyfromadlsgen1**作为输出文件夹的名称，然后选择**下一步**。 数据工厂创建的相应 Azure 数据湖存储第 2 代文件系统和子文件夹在复制期间如果它们不存在。

    ![指定输出文件夹](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-path.png)

11. 上**设置**页上，选择**下一步**以使用默认设置。

12. 上**摘要**页上，查看设置，然后选择**下一步**。

    ![“摘要”页](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-summary.png)
13. 上**部署页**，选择**监视器**来监视管道。

    ![“部署”页](./media/load-azure-data-lake-storage-gen2-from-gen1/deployment-page.png)
14. 请注意，界面中已自动选择左侧的“监视”选项卡。  “操作”列中包含用于查看活动运行详细信息以及用于重新运行管道的链接  。

    ![监视管道运行](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-pipeline-runs.png)

15. 若要查看与管道运行关联的活动运行，请选择“操作”列中的“查看活动运行”链接。   该管道只包含一个活动（复制活动），因此只显示了一个条目。 若要切换回到管道运行视图，请选择顶部的“管道”链接  。 选择“刷新”可刷新列表。  

    ![监视活动运行](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-runs.png)

16. 若要监视每个复制活动的执行详细信息，请在活动监视视图中选择“操作”下的“详细信息”链接（眼镜图标）   。 等量的数据从源复制到接收器、 吞吐量、 执行步骤以及相应的持续时间和使用的配置，可以监视详细信息。

    ![监视活动运行详细信息](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-run-details.png)

17. 验证将数据复制到 Azure 数据湖存储第 2 代帐户。

## <a name="best-practices"></a>最佳做法

若要评估在一般情况下升级从 Azure 数据湖存储 Gen1 到 Azure 数据湖存储第 2 代，请参阅[将大数据分析解决方案从 Azure 数据湖存储 Gen1 升级到 Azure 数据湖存储第 2 代](../storage/blobs/data-lake-storage-upgrade.md)。 以下部分介绍使用数据工厂从数据湖存储 Gen1 数据升级到数据湖存储第 2 代的最佳实践。

### <a name="data-partition-for-historical-data-copy"></a>对于历史数据副本的数据分区

- 如果在数据湖存储 Gen1 总数据大小为小于 30 TB 和文件数少于 1 百万，可以在单次复制活动运行中复制所有数据。
- 如果您有更多的数据要复制，或希望灵活地管理批处理中的数据迁移并使每个特定的时间段内完成，对数据进行分区。 分区还减少了意外的任何问题的风险。

使用概念证明来验证端到端解决方案和测试环境中的复制吞吐量。 主要概念验证步骤： 

1. 创建一个数据工厂管道包含单个复制活动将数 Tb 的数据从数据湖存储 Gen1 复制到数据湖存储第 2 代若要获取复制性能基线。 开头[数据集成单位 (DIUs)](copy-activity-performance.md#data-integration-units)为 128。 
2. 根据在步骤 1 中获取的复制吞吐量，计算对整个数据迁移所需的估计的时间。 
3. （可选）创建控制表并定义要分区的文件要迁移的文件筛选器。 分区文件的方法是： 

    - 按文件夹名称或使用通配符筛选器的文件夹名称的分区。 我们建议使用此方法。
    - 分区文件的上次修改时间。

### <a name="network-bandwidth-and-storage-io"></a>网络带宽和存储 I/O 

您可以控制数据工厂的复制作业，从数据湖存储 Gen1 读取数据并将数据写入到数据湖存储第 2 代的并发性。 在这种方式，可以管理该存储 I/O，以避免在迁移过程中影响数据湖存储 Gen1 上的正常业务工作上使用。

### <a name="permissions"></a>权限 

在数据工厂[数据湖存储 Gen1 连接器](connector-azure-data-lake-store.md)支持服务的 Azure 资源的身份验证的主体和托管标识。 [数据湖存储第 2 代连接器](connector-azure-data-lake-storage.md)支持帐户密钥、 服务主体和 Azure 资源的身份验证的托管的标识。 若要使数据工厂能够导航并复制所有文件或访问控制列表 (Acl)，您需要高授予足够的权限为您提供用来访问、 读取或写入的所有文件和设置 Acl，你可以选择的帐户。 授予它的超级用户或所有者角色在迁移期间。 

### <a name="preserve-acls-from-data-lake-storage-gen1"></a>保留从数据湖存储 Gen1 Acl

如果你想要从数据湖存储 Gen1 升级到数据湖存储第 2 代时，将复制文件以及数据文件的 Acl，请参阅[从数据湖存储 Gen1 保留 Acl](connector-azure-data-lake-storage.md#preserve-acls-from-data-lake-storage-gen1)。 

### <a name="incremental-copy"></a>增量复制 

您可以使用几种方法从数据湖存储 Gen1 加载仅将新的或更新文件：

- 按时间分区文件夹或文件名称加载新的或更新文件。 例如，/2019年/05/13 / *。
- 加载 LastModifiedDate 通过新的或更新文件。
- 确定任何第三方工具或解决方案的新的或更新文件。 然后文件或文件夹名称传递给数据工厂管道通过参数、 表或文件。 

若要执行增量加载的正确频率取决于 Azure 数据湖存储 Gen1 中的文件总数以及要加载每次新的或更新文件的卷。 

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [复制活动概述](copy-activity-overview.md)
> [Azure 数据湖存储 Gen1 连接器](connector-azure-data-lake-store.md)
> [Azure 数据湖存储第 2 代连接器](connector-azure-data-lake-storage.md)