---
title: "使用 Azure 数据工厂向 Azure Data Lake Store 加载数据 | Microsoft Docs"
description: "使用 Azure 数据工厂将数据复制到 Azure Data Lake Store"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.topic: article
ms.date: 01/17/2018
ms.author: jingwang
ms.openlocfilehash: 4446f83563293d0834f241dcca382ccf6ea99403
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2018
---
# <a name="load-data-into-azure-data-lake-store-by-using-azure-data-factory"></a>使用 Azure 数据工厂向 Azure Data Lake Store 加载数据

[Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md) 是一个企业范围的超大规模存储库，适用于大数据分析工作负荷。 使用 Azure Data Lake 可以捕获任何大小、类型和引入速度的数据。 可在单个位置捕获数据以用于操作和探索性分析。

Azure 数据工厂是一个完全托管的基于云的数据集成服务。 该服务可用于使用现有系统中的数据填充 Lake，在生成分析解决方案时节省时间。

Azure 数据工厂在将数据加载到 Azure Data Lake Store 方面提供以下优点：

* **轻松设置**：无需脚本的 5 步直观的向导。
* **丰富的数据存储支持**：对一组丰富的本地和基于云的数据存储的内置支持。 有关详细列表，请参阅表[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。
* **安全且合规**：通过 HTTPS 或 ExpressRoute 传输数据。 存在全局服务可确保数据永远不会离开地理边界。
* **高性能**：向 Azure Data Lake Store 加载数据的速度高达 1-GB/秒。 有关详细信息，请参阅[复制活动性能](copy-activity-performance.md)。

本文介绍如何使用数据工厂复制数据工具，_将数据从 Amazon S3 加载至 Azure Data Lake Store_。 可以遵循类似步骤，从其他类型的数据存储中复制数据。

> [!NOTE]
> 有关详细信息，请参阅[使用 Azure 数据工厂向/从 Azure Data Lake Store 复制数据](connector-azure-data-lake-store.md)。
>
> 本文适用于目前处于预览状态的 Azure 数据工厂第 2 版。 如果使用正式版 (GA) 版本 1 的数据工厂服务，请参阅 [Azure 数据工厂版本 1 中的复制活动](v1/data-factory-data-movement-activities.md)。

## <a name="prerequisites"></a>先决条件

* Azure 订阅：如果没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/)。
* Azure Data Lake Store：如果没有 Data Lake Store 帐户，请参阅[创建 Data Lake Store 帐户](../data-lake-store/data-lake-store-get-started-portal.md#create-an-azure-data-lake-store-account)中的说明。
* Amazon S3：本文介绍如何从 Amazon S3 复制数据。 可以按类似步骤使用其他数据存储。

## <a name="create-a-data-factory"></a>创建数据工厂

1. 在左侧菜单中，选择“新建” > “数据 + 分析” > “数据工厂”：
   
   ![新建数据工厂](./media/load-data-into-azure-data-lake-store/new-azure-data-factory-menu.png)
2. 在“新建数据工厂”页中，为下图中所示的字段提供值： 
      
   ![“新建数据工厂”页](./media/load-data-into-azure-data-lake-store//new-azure-data-factory.png)
 
    * **名称**：输入 Azure 数据工厂的全局唯一名称。 如果收到错误“数据工厂名称 \"LoadADLSDemo\" 不可用”，请输入不同的数据工厂名称。 例如，可以使用名称 _**yourname**_**ADFTutorialDataFactory**。 请重试创建数据工厂。 有关数据工厂项目的命名规则，请参阅[数据工厂 - 命名规则](naming-rules.md)。
    * **订阅**：选择要在其中创建数据工厂的 Azure 订阅。 
    * **资源组**：从下拉列表中选择现有资源组，或选择“新建”选项并输入资源组的名称。 若要了解有关资源组的详细信息，请参阅 [使用资源组管理 Azure 资源](../azure-resource-manager/resource-group-overview.md)。  
    * **版本**：选择“V2 (预览版)”。
    * **位置**：选择数据工厂所在的位置。 下拉列表中仅显示支持的位置。 数据工厂使用的数据存储可以在其他位置和区域中。 这些数据存储包括 Azure Data Lake Store、Azure 存储、Azure SQL 数据库，等等。

3. 选择“创建”。
4. 创建操作完成后，请转到数据工厂。 此时会看到“数据工厂”主页，如下图所示： 
   
   ![数据工厂主页](./media/load-data-into-azure-data-lake-store/data-factory-home-page.png)

   选择“创作和监视”磁贴，在单独的选项卡中启动数据集成应用程序。

## <a name="load-data-into-azure-data-lake-store"></a>向 Azure Data Lake Store 加载数据

1. 在“入门”页中，单击“复制数据”磁贴启动“复制数据”工具： 

   ![“复制数据”工具磁贴](./media/load-data-into-azure-data-lake-store/copy-data-tool-tile.png)
2. 在“属性”页中，为“任务名称”字段指定“CopyFromAmazonS3ToADLS”，然后选择“下一步”：

    ![“属性”页](./media/load-data-into-azure-data-lake-store/copy-data-tool-properties-page.png)
3. 在“源数据存储”页中选择“Amazon S3”，然后选择“下一步”：

    ![“源数据存储”页](./media/load-data-into-azure-data-lake-store/source-data-store-page.png)
4. 在“指定 Amazon S3 连接”页中，执行以下步骤： 
   1. 指定“访问密钥 ID”值。
   2. 指定“机密访问密钥”值。
   3. 选择“**下一步**”。
   
   ![指定 Amazon S3 帐户](./media/load-data-into-azure-data-lake-store/specify-amazon-s3-account.png)
5. 在“选择输入文件或文件夹”页上，浏览到要复制的文件夹和文件。 选中文件夹/文件，选择“选择”，然后选择“下一步”：

    ![选择输入文件或文件夹](./media/load-data-into-azure-data-lake-store/choose-input-folder.png)

6. 在“目标数据存储”页中，选择“Azure Data Lake Store”，然后选择“下一步”：

    ![“目标数据存储”页](./media/load-data-into-azure-data-lake-store/destination-data-storage-page.png)

7. 通过选中“以递归方式复制文件”和“以二进制方式复制”（按原样复制文件）选项，选择复制行为。 选择“下一步”：

    ![指定输出文件夹](./media/load-data-into-azure-data-lake-store/specify-binary-copy.png)

8. 在“指定 Data Lake Store 连接”页中，执行以下步骤： 

   1. 为“Data Lake Store 帐户名”选择你的 Data Lake Store。
   2. 指定服务主体信息：“租户”、“服务主体 ID”和“服务主体键”。
   3. 选择“**下一步**”。
   
   > [!IMPORTANT]
   > 在此演练中，使用_服务主体_进行 Data Lake Store 身份验证。 请务必按照[这些说明](connector-azure-data-lake-store.md#using-service-principal-authentication)授予服务主体在 Azure Data Lake Store 中的适当权限。
   
   ![指定 Azure Data Lake Store 帐户](./media/load-data-into-azure-data-lake-store/specify-adls.png)
9. 在“选择输出文件或文件夹”页上，输入 **copyfroms3** 作为输出文件夹名称，然后选择“下一步”： 

    ![指定输出文件夹](./media/load-data-into-azure-data-lake-store/specify-adls-path.png)

10. 在“设置”页中，选择“下一步”：

    ![“设置”页](./media/load-data-into-azure-data-lake-store/copy-settings.png)
11. 在“摘要”页中检查设置，然后选择“下一步”：

    ![“摘要”页](./media/load-data-into-azure-data-lake-store/copy-summary.png)
12. 在“部署”页中，选择“监视”可以监视管道（任务）：

    ![“部署”页](./media/load-data-into-azure-data-lake-store/deployment-page.png)
13. 请注意，界面中已自动选择左侧的“监视”选项卡。 “操作”列中包含用于查看活动运行详细信息以及用于重新运行管道的链接：

    ![监视管道运行](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)
14. 若要查看与管道运行关联的活动运行，请选择“操作”列中的“查看活动运行”链接。 该管道只包含一个活动（复制活动），因此只显示了一个条目。 若要切换回到管道运行视图，请选择顶部的“管道”链接。 选择“刷新”可刷新列表。 

    ![监视活动运行](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

15. 若要监视每个复制活动的执行详细信息，请在活动监视视图中选择“操作”下的“详细信息”链接。 可以监视详细信息，如从源复制到接收器的数据量、吞吐量、执行步骤以及相应的持续时间和使用的配置：

    ![监视活动运行详细信息](./media/load-data-into-azure-data-lake-store/monitor-activity-run-details.png)

16. 验证数据是否已复制到 Azure Data Lake Store： 

    ![验证 Data Lake Store 输出](./media/load-data-into-azure-data-lake-store/adls-copy-result.png)

## <a name="next-steps"></a>后续步骤

请转至下列文章，了解有关 Azure Data Lake Store 支持的信息： 

> [!div class="nextstepaction"]
>[Azure Data Lake Store 连接器](connector-azure-data-lake-store.md)