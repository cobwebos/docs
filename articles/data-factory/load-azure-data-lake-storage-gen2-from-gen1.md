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
ms.openlocfilehash: d6e09ec1f070f9ee0f4162524e4bd80d1f81adc3
ms.sourcegitcommit: 179918af242d52664d3274370c6fdaec6c783eb6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/13/2019
ms.locfileid: "65560642"
---
# <a name="copy-data-from-azure-data-lake-storage-gen1-to-gen2-with-azure-data-factory"></a>使用 Azure 数据工厂将数据从 Azure Data Lake Storage Gen1 复制到 Gen2

Azure Data Lake Storage Gen2 是一组专用于大数据分析的功能，内置于 [Azure Blob 存储](../storage/blobs/storage-blobs-introduction.md)中。 它可使用文件系统和对象存储范例与数据进行交互。

如果当前正在使用 Azure Data Lake Storage Gen1，则可以使用 Azure 数据工厂将数据从 Data Lake Storage Gen1 复制到 Gen2，以便评估 Gen2 的新功能。

Azure 数据工厂是一个完全托管的基于云的数据集成服务。 通过该服务，可使用丰富的本地数据存储和基于云的数据存储中的数据填充数据湖，并更快速地生成分析解决方案。 若要查看受支持的连接器的详细列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

Azure 数据工厂提供可横向扩展的托管数据移动解决方案。 得益于 ADF 的横向扩展体系结构，它能以较高的吞吐量引入数据。 有关详细信息，请参阅[复制活动性能](copy-activity-performance.md)。

本文介绍如何使用数据工厂复制数据工具将数据从 _Azure Data Lake Storage Gen1_ 复制到 _Azure Data Lake Storage Gen2_。 可以遵循类似步骤，从其他类型的数据存储中复制数据。

## <a name="prerequisites"></a>必备组件

* Azure 订阅：如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。
* 其中有数据的 Azure Data Lake Storage Gen1 帐户。
* 启用了 Data Lake Storage Gen2 的 Azure 存储帐户：如果没有存储帐户[创建一个帐户](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)。

## <a name="create-a-data-factory"></a>创建数据工厂

1. 在左侧菜单中，选择“创建资源” > “数据 + 分析” > “数据工厂”：
   
   ![在“新建”窗格中选择“数据工厂”](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. 在“新建数据工厂”页中，为下图中所示的字段提供值： 
      
   ![“新建数据工厂”页](./media/load-azure-data-lake-storage-gen2-from-gen1/new-azure-data-factory.png)
 
    * **名称**：输入 Azure 数据工厂的全局唯一名称。 如果收到错误“数据工厂名称 \"LoadADLSDemo\" 不可用”，请输入不同的数据工厂名称。 例如，可以使用名称 _**yourname**_**ADFTutorialDataFactory**。 请重试创建数据工厂。 有关数据工厂项目的命名规则，请参阅[数据工厂命名规则](naming-rules.md)。
    * **订阅**：选择要在其中创建数据工厂的 Azure 订阅。 
    * **资源组**：从下拉列表中选择现有资源组，或选择“新建”选项并输入资源组的名称。 若要了解有关资源组的详细信息，请参阅 [使用资源组管理 Azure 资源](../azure-resource-manager/resource-group-overview.md)。  
    * **版本**：选择“V2”。
    * **位置**：选择数据工厂的位置。 下拉列表中仅显示支持的位置。 数据工厂使用的数据存储可以在其他位置和区域中。 

3. 选择“创建”。
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
   1. 选择你的 Data Lake Storage Gen1 作为帐户名称，并指定或验证**租户**。
   2. 单击“测试连接”以验证设置，然后选择“完成”。
   3. 随即会显示新创建的连接。 选择“**下一步**”。
   
   > [!IMPORTANT]
   > 在本演练中，使用 Azure 资源的托管标识来对 Data Lake Storage Gen1 进行身份验证。 请务必按照[这些说明](connector-azure-data-lake-store.md#managed-identity)授予 MSI 在 Azure Data Lake Storage Gen1 中的适当权限。
   
   ![指定 Azure Data Lake Storage Gen1 帐户](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen1-account.png)
      
5. 在“选择输入文件或文件夹”页上，浏览到要复制的文件夹和文件。 选择文件夹/文件，选择“选择”：

    ![选择输入文件或文件夹](./media/load-azure-data-lake-storage-gen2-from-gen1/choose-input-folder.png)

6. 选中“以递归方式复制文件”和“以二进制方式复制”选项，指定复制行为。 选择“下一步”：

    ![指定输出文件夹](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-binary-copy.png)
    
7. 在“目标数据存储”页中，单击“+ 新建连接”，然后选择“Azure Data Lake Storage Gen2”，并选择“继续”：

    ![“目标数据存储”页](./media/load-azure-data-lake-storage-gen2-from-gen1/destination-data-storage-page.png)

8. 在“指定 Azure Data Lake Storage Gen2 连接”页中，执行以下步骤：

   1. 从“存储帐户名称”下拉列表中选择能使用 Data Lake Storage Gen2 的帐户。
   2. 选择“完成”以创建连接。 然后，选择“下一步”。
   
   ![指定 Azure Data Lake Storage Gen2 帐户](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-account.png)

9. 在中**选择输出文件或文件夹**页上，输入**copyfromadlsgen1**作为输出文件夹的名称，然后选择**下一步**。 ADF 将创建相应的 ADLS 第 2 代文件系统和子文件夹在复制期间如果不存在。

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

## <a name="best-practices"></a>最佳做法

若要评估在一般情况下升级从 Azure Data Lake 存储 (ADLS) Gen1 到第 2 代，请参阅[将大数据分析解决方案从 Azure 数据湖存储 Gen1 升级到 Azure 数据湖存储第 2 代](../storage/blobs/data-lake-storage-upgrade.md)。 以下部分介绍从 Gen1 到第 2 代的数据升级为使用 ADF 的最佳做法。

### <a name="data-partition-for-historical-data-copy"></a>对于历史数据副本的数据分区

- 如果你在 ADLS Gen1 的总数据大小小于**30 TB**和文件数是否小于**1 百万个**，可以将所有数据都复制运行的单个都复制活动中。
- 如果具有更大大小的数据要复制，或希望灵活地管理批处理中的数据迁移并使每个特定计时时段内完成，建议以对数据进行分区，这种情况下这还会缩短任何意外的 iss 的风险ue。

若要验证端到端解决方案，然后在环境中测试复制吞吐量强烈建议 PoC （概念）。 执行 PoC 的主要步骤： 

1. 创建包含单个复制活动将数 Tb 的数据从 ADLS Gen1 复制 ADLS 第 2 代，若要获取复制性能基准，从开始到一个 ADF 管道[数据集成单位 (DIUs)](copy-activity-performance.md#data-integration-units)为 128。 
2. 根据在步骤 #1 中获取的复制吞吐量，计算整个数据迁移的所需的估计的时间。 
3. （可选）创建控制表并定义要分区的文件要迁移的文件筛选器。 分区的文件按如下操作方法： 

    - 使用通配符筛选器 （建议） 分区按文件夹名称或文件夹名称 
    - 按文件的上次修改时间分区 

### <a name="network-bandwidth-and-storage-io"></a>网络带宽和存储 I/O 

以便可以为了在迁移期间不会影响在正常业务工作，ADLS Gen1 管理存储 I/O 上的使用情况，您可以控制从 ADLS Gen1 读取数据并将数据写入到 ADLS 第 2 代，ADF 复制作业的并发性。

### <a name="permissions"></a>权限 

在数据工厂[ADLS Gen1 连接器](connector-azure-data-lake-store.md)支持服务主体和托管标识的 Azure 资源的身份验证;[ADLS 第 2 代连接器](connector-azure-data-lake-storage.md)支持的帐户密钥，服务主体和 Azure 资源的身份验证的托管标识。 使数据工厂能够导航并复制所有文件/Acl 根据需要都请确保授予高足够的权限的帐户提供访问/读取/写入到的所有文件，并设置 Acl，你可以选择。 建议在迁移期间授予其作为 super 用户/所有者角色。 

### <a name="preserve-acls-from-data-lake-storage-gen1"></a>保留从数据湖存储 Gen1 Acl

如果你想要从数据湖存储 Gen1 升级到第 2 代时复制文件以及数据文件的 Acl，请参阅[从数据湖存储 Gen1 保留 Acl](connector-azure-data-lake-storage.md#preserve-acls-from-data-lake-storage-gen1)。 

### <a name="incremental-copy"></a>增量复制 

几种方法可用于从 ADLS Gen1 加载仅将新的或更新文件：

- 加载新的或更新文件按时间分区文件夹或文件名称，例如/2019年/05/13 / *;
- 加载 LastModifiedDate; 通过新的或更新文件
- 通过任何第三方工具/解决方案，来识别新的或更新文件，然后将文件或文件夹名称参数或表/文件通过传递给 ADF 管道。  

若要执行增量加载的适当频率取决于 ADLS Gen1 中的文件总数以及要加载每次新的或更新文件的卷。  

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [复制活动概述](copy-activity-overview.md)
> [Azure 数据湖存储 Gen1 连接器](connector-azure-data-lake-store.md)
> [Azure 数据湖存储第 2 代连接器](connector-azure-data-lake-storage.md)