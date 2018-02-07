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
ms.openlocfilehash: 3f73cd65b0ceb3148ce8ceb83d7b4e1be1280077
ms.sourcegitcommit: 828cd4b47fbd7d7d620fbb93a592559256f9d234
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/18/2018
---
# <a name="load-data-into-azure-data-lake-store-using-azure-data-factory"></a>使用 Azure 数据工厂向 Azure Data Lake Store 加载数据

[Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md) 是一个企业范围的超大规模存储库，适用于大数据分析工作负荷。 使用 Azure Data Lake 可以在单个位置捕获任何大小、类型和引入速度的数据进行操作和探索分析。

Azure 数据工厂是一个完全托管的基于云的数据集成服务，它可用于使用现有系统中的数据填充湖，在节省宝贵时间的同时生成分析解决方案。 以下是使用 Azure 数据工厂将数据加载到 Azure Data Lake Store 的主要优点：

* **轻松设置**：无需脚本的 5 步直观的向导。
* **丰富的数据存储支持**：对一组丰富的本地和基于云的数据存储的内置支持，有关详细列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。
* **安全且合规**：通过 HTTPS 或 ExpressRoute 传输数据，并且全局服务可确保数据不会离开地理边界
* **高性能**：向 Azure Data Lake Store 加载数据的速度高达 1GBps。 从[复制活动性能](copy-activity-performance.md)中了解详细信息。

本文介绍如何使用数据工厂复制数据工具，将数据从 Amazon S3 加载至 Azure Data Lake Store。 可以遵循类似步骤，从其他类型的数据存储中复制数据。

> [!NOTE]
>  有关数据工厂将数据复制到 Azure Data Lake Store，或从 Azure Data Lake Store 复制数据的功能的一般信息，请参阅[使用 Azure 数据工厂将数据复制到 Azure Data Lake Store 或从 Azure Data Lake Store 复制数据](connector-azure-data-lake-store.md)一文。
>
> 本文适用于目前处于预览版的数据工厂版本 2。 如果使用正式版 (GA) 1 版本的数据工厂服务，请参阅 [V1 中的复制活动](v1/data-factory-data-movement-activities.md)。

## <a name="prerequisites"></a>先决条件

* **Azure 订阅**。 如果你还没有 Azure 订阅，可以在开始前创建一个[免费](https://azure.microsoft.com/free/)帐户。
* **Azure Data Lake Store**。 如果没有 Azure Data Lake Store 帐户，请参阅[创建 Azure Data Lake Store 帐户](../data-lake-store/data-lake-store-get-started-portal.md#create-an-azure-data-lake-store-account)一文，按其中所述步骤创建帐户。
* **Amazon S3**。 本文介绍如何从 Amazon S3 复制数据。 可以按类似步骤使用其他数据存储。

## <a name="create-a-data-factory"></a>创建数据工厂

1. 在左侧菜单中单击“新建”，并依次单击“数据 + 分析”、“数据工厂”。 
   
   ![新建 -> DataFactory](./media/load-data-into-azure-data-lake-store/new-azure-data-factory-menu.png)
2. 在“新建数据工厂”页中，提供以下屏幕截图中所示的值： 
      
     ![“新建数据工厂”页](./media/load-data-into-azure-data-lake-store//new-azure-data-factory.png)
 
   * **名称。** 输入数据工厂的全局唯一名称。 如果收到错误，请更改数据工厂的名称（例如改为 yournameADFTutorialDataFactory），并重新尝试创建。 有关数据工厂项目命名规则，请参阅[数据工厂 - 命名规则](naming-rules.md)一文。
  
            `Data factory name "LoadADLSDemo" is not available`

    * **订阅。** 选择要在其中创建数据工厂的 Azure **订阅**。 
    * **资源组。** 从下拉列表中选择现有资源组，或选择“新建”选项并输入资源组的名称。 若要了解有关资源组的详细信息，请参阅 [使用资源组管理 Azure 资源](../azure-resource-manager/resource-group-overview.md)。  
    * **版本。** 选择“V2 (预览版)”。
    * **位置。** 选择数据工厂的位置。 下拉列表中仅显示支持的位置。 数据工厂使用的数据存储（Azure Data Lake Store、Azure 存储、Azure SQL 数据库等）可以位于其他位置/区域。

3. 单击“创建”。
4. 创建完成后，请转至数据工厂，那里将会显示图中所示的“数据工厂”页。 单击“创作和监视”磁贴，在单独的选项卡中启动数据集成应用程序。 
   
   ![数据工厂主页](./media/load-data-into-azure-data-lake-store/data-factory-home-page.png)

## <a name="load-data-into-azure-data-lake-store"></a>向 Azure Data Lake Store 加载数据

1. 在入门页中，单击“复制数据”磁贴启动“复制数据”工具。 

   ![“复制数据”工具磁贴](./media/load-data-into-azure-data-lake-store/copy-data-tool-tile.png)
2. 在“复制数据”工具的“属性”页中，为“任务名称”指定“CopyFromAmazonS3ToADLS”，然后单击“下一步”。 

    ![“复制数据”工具 -“属性”页](./media/load-data-into-azure-data-lake-store/copy-data-tool-properties-page.png)
3. 在“源数据存储”页中选择“Amazon S3”，然后单击“下一步”。

    ![“源数据存储”页](./media/load-data-into-azure-data-lake-store/source-data-store-page.png)
4. 在“指定 Amazon S3 连接”页中，执行以下步骤： 
    1. 指定访问密钥 ID。
    2. 指定机密访问密钥。
    3. 单击“资源组名称” 的 Azure 数据工厂。 

        ![指定 Amazon S3 帐户](./media/load-data-into-azure-data-lake-store/specify-amazon-s3-account.png)
5. 在“选择输入文件或文件夹”页中，导航至要复制的文件夹/文件，选择并单击“选择”，然后单击“下一步”。 

    ![选择输入文件或文件夹](./media/load-data-into-azure-data-lake-store/choose-input-folder.png)

6. 在“目标数据存储”页中，选择“Azure Data Lake Store”，然后单击“下一步”。 

    ![“目标数据存储”页](./media/load-data-into-azure-data-lake-store/destination-data-storage-page.png)

7. 在此页中，通过选中“以递归方式复制文件”和“以二进制方式复制”（按原样复制文件）选项，选择复制行为。 单击“资源组名称” 的 Azure 数据工厂。

    ![指定输出文件夹](./media/load-data-into-azure-data-lake-store/specify-binary-copy.png)

8. 在“指定 Data Lake Store 连接”页中，执行以下步骤： 

    1. 为“Data Lake Store 帐户名”选择你的 Data Lake Store。
    2. 指定服务主体信息，包括“租户”、“服务主体 ID”和“服务主体键”。
    3. 单击“资源组名称” 的 Azure 数据工厂。 

    > [!IMPORTANT]
    > 在此演练中，使用“服务主体”对 Data Lake Store 进行身份验证。 请遵循[此处](connector-azure-data-lake-store.md#using-service-principal-authentication)的说明，确保在 Azure Data Lake Store 中授予服务主体适当的权限。

    ![指定 Azure Data Lake Store 帐户](./media/load-data-into-azure-data-lake-store/specify-adls.png)

9. 在“选择输出文件或文件夹”页中，指定“copyfroms3”，然后单击“下一步”。 

    ![指定输出文件夹](./media/load-data-into-azure-data-lake-store/specify-adls-path.png)


10. 在“设置”页中，单击“下一步”。 

    ![“设置”页](./media/load-data-into-azure-data-lake-store/copy-settings.png)
11. 在“摘要”页中检查设置，然后单击“下一步”。

    ![“摘要”页](./media/load-data-into-azure-data-lake-store/copy-summary.png)
12. 在“部署”页中，单击“监视”可以监视管道（任务）。

    ![“部署”页](./media/load-data-into-azure-data-lake-store/deployment-page.png)
13. 请注意，界面中已自动选择左侧的“监视”选项卡。 在“操作”列中，可以看到用于查看活动运行详细信息以及重新运行管道的链接。 

    ![监视管道运行](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)
14. 若要查看与管道运行相关联的活动运行，请单击“操作”列中的“查看活动运行”链接。 该管道只包含一个活动（复制活动），因此只显示了一个条目。 若要切换回到管道运行视图，请单击顶部的“管道”链接。 单击“刷新”可刷新列表。 

    ![监视活动运行](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

15. 可以进一步监视每个复制活动的执行详细信息，方法是在活动监视视图中单击“操作”下的“详细信息”链接。 它显示以下信息：从源复制到接收器的数据量、吞吐量、所执行的步骤以及相应的持续时间和使用的配置。

    ![监视活动运行详细信息](./media/load-data-into-azure-data-lake-store/monitor-activity-run-details.png)

16. 验证数据是否已复制到 Azure Data Lake Store。 

    ![验证 Data Lake Store 输出](./media/load-data-into-azure-data-lake-store/adls-copy-result.png)

## <a name="next-steps"></a>后续步骤

请转至下列文章，了解有关 Azure Data Lake Store 支持的信息： 

> [!div class="nextstepaction"]
>[Azure Data Lake Store 连接器](connector-azure-data-lake-store.md)