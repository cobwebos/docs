---
title: 将数据从 Azure 数据工厂复制到 Azure 数据资源管理器
description: 本文介绍如何使用 Azure 数据工厂复制工具将数据引入（加载）到 Azure 数据资源管理器中。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 8e17a004ff866f3915000fb72b6770757062cf83
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422916"
---
# <a name="copy-data-to-azure-data-explorer-by-using-azure-data-factory"></a>使用 Azure 数据工厂将数据复制到 Azure 数据资源管理器 

Azure 数据资源管理器是一个快速、完全托管的数据分析服务。 它可以实时分析从应用程序、网站和 IoT 设备等许多源流式传输的大量数据。 使用 Azure 数据资源管理器，可以迭代方式浏览数据，识别模式和异常以改进产品、增强客户体验、监视设备，以及提升操作性能。 它可以帮助你探讨新的问题，并在短时间内获得解答。 

Azure 数据工厂是一个完全托管的基于云的数据集成服务。 可以使用它在 Azure 数据资源管理器数据库中填充现有系统中的数据。 它可以帮助你节省生成分析解决方案所花费的时间。

将数据载入 Azure 数据资源管理器时，数据工厂可提供以下优势：

* **简单的设置**：获得一个直观的五步向导，无需编写脚本。
* **丰富的数据存储支持**：获得对一组丰富的本地和基于云的数据存储的内置支持。 有关详细列表，请参阅表[支持的数据存储](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats)。
* **安全和合规**：数据通过 HTTPS 或 Azure 快速路由传输。 存在全局服务可确保数据永远不会离开地理边界。
* **高性能**：数据加载速度高达每秒 1 GB （GBps） 到 Azure 数据资源管理器。 有关详细信息，请参阅[复制活动性能](/azure/data-factory/copy-activity-performance)。

在本文中，你将使用数据工厂复制数据工具，将数据从 Amazon 简单存储服务 (S3) 载入 Azure 数据资源管理器。 可以遵循类似的步骤，从下述其他数据存储复制数据：
* [Azure Blob 存储](/azure/data-factory/connector-azure-blob-storage)
* [Azure SQL Database](/azure/data-factory/connector-azure-sql-database)
* [Azure SQL 数据仓库](/azure/data-factory/connector-azure-sql-data-warehouse)
* [Google BigQuery](/azure/data-factory/connector-google-bigquery)
* [Oracle](/azure/data-factory/connector-oracle)
* [文件系统](/azure/data-factory/connector-file-system)

## <a name="prerequisites"></a>先决条件

* 如果还没有 Azure 订阅，可以在开始前创建一个[免费 Azure 帐户](https://azure.microsoft.com/free/)。
* [Azure 数据资源管理器群集和数据库](create-cluster-database-portal.md)。
* 数据源。

## <a name="create-a-data-factory"></a>创建数据工厂

1. 登录 [Azure 门户](https://ms.portal.azure.com)。

1. 在左侧窗格中，选择 **"创建资源** > **分析** > **数据工厂**"。

   ![在 Azure 门户中创建数据工厂](media/data-factory-load-data/create-adf.png)

1. 在“新建数据工厂”窗格中，提供下表中的字段值：****

   ![“新建数据工厂”窗格](media/data-factory-load-data/my-new-data-factory.png)  

   | 设置  | 要输入的值  |
   |---|---|
   | **名称** | 在框中输入数据工厂的全局唯一名称。 如果收到错误“数据工厂名称 \"LoadADXDemo\" 不可用”，请输入不同的数据工厂名称。** 有关数据工厂项目的命名规则，请参阅[数据工厂命名规则](/azure/data-factory/naming-rules)。|
   | **订阅** | 在下拉列表中，选择要在其中创建数据工厂的 Azure 订阅。 |
   |  **资源组** | 选择“新建”，然后输入新资源组的名称。**** 如果已有一个资源组，请选择“使用现有项”。**** |
   | **版本** | 在下拉列表中选择“V2”。**** |    
   | **位置** | 在下拉列表中选择数据工厂的位置。 该列表仅显示支持的位置。 数据工厂使用的数据存储可位于其他位置或区域中。 |

1. 选择“创建”  。

1. 若要监视创建过程，请在工具栏上选择“通知”。**** 创建数据工厂后，将其选中。
   
   此时会打开“数据工厂”窗格。****

   ![“数据工厂”窗格](media/data-factory-load-data/data-factory-home-page.png)

1. 若要在单独的窗格中打开应用程序，请选择“创作和监视”磁贴****。

## <a name="load-data-into-azure-data-explorer"></a>将数据载入 Azure 数据资源管理器

可将许多类型的[数据存储](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats)中的数据载入 Azure 数据资源管理器。 本文介绍如何从 Amazon S3 加载数据。

可通过以下任一方式加载数据：

* 在 Azure 数据工厂用户界面中，在左侧窗格中，选择 **"作者"** 图标。 这显示在[使用 Azure 数据工厂 UI 创建数据工厂的](/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory)"创建数据工厂"部分中。
* 使用 Azure 数据工厂复制数据工具，如[使用复制数据工具复制数据](/azure/data-factory/quickstart-create-data-factory-copy-data-tool)中所述。

### <a name="copy-data-from-amazon-s3-source"></a>从 Amazon S3（源）复制数据

1. 在“开始使用”窗格中，选择“复制数据”打开复制数据工具。********

   ![复制数据工具按钮](media/data-factory-load-data/copy-data-tool-tile.png)

1. 在“属性”窗格中的“任务名称”框内输入名称，然后选择“下一步”。************

    ![复制数据工具的“属性”窗格](media/data-factory-load-data/copy-from-source.png)

1. 在“源数据存储”窗格中，选择“创建新连接”。********

    ![复制数据工具的“源数据存储”窗格](media/data-factory-load-data/source-create-connection.png)

1. 依次选择“Amazon S3”、“继续”。********

    ![“新建链接服务”窗格](media/data-factory-load-data/amazons3-select-new-linked-service.png)

1. 在“新建链接服务(Amazon S3)”窗格中执行以下操作：****

    ![指定 Amazon S3 链接服务](media/data-factory-load-data/amazons3-new-linked-service-properties.png)

    a. 在“名称”框中，输入新链接服务的名称。****

    b. 在“通过集成运行时进行连接”下拉列表中选择值。****

    c. 在“访问密钥 ID”框中输入值。****
    
    > [!NOTE]
    > 若要在 Amazon S3 中查找访问密钥，请在导航栏上选择自己的 Amazon 用户名，然后选择“我的安全凭据”。****
    
    d. 在“机密访问密钥”中输入值。****

    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 若要测试创建的链接服务连接，请选择“测试连接”。****

    f. 选择“完成”****。
    
      “源数据存储”窗格中会显示新的 AmazonS31 连接。**** 

1. 选择“下一步”。

   ![源数据存储 - 创建的连接](media/data-factory-load-data/source-data-store-created-connection.png)

1. 在 **"选择输入文件"或"文件夹**"窗格中，执行以下步骤：

    a. 浏览到要复制的文件或文件夹并将其选中。

    b. 选择所需的复制行为。 请确保未选中“二进制副本”复选框。****

    c. 选择“下一步”。

    ![选择输入文件或文件夹](media/data-factory-load-data/source-choose-input-file.png)

1. 在“文件格式设置”窗格中选择文件的相关设置。**** 然后选择“下一步”。****

   ![“文件格式设置”窗格](media/data-factory-load-data/source-file-format-settings.png)

### <a name="copy-data-into-azure-data-explorer-destination"></a>将数据复制到 Azure 数据资源管理器（目标）

现已创建新的 Azure 数据资源管理器链接服务，用于将数据复制到本部分指定的 Azure 数据资源管理器目标表（接收器）。

> [!NOTE]
> 使用[Azure 数据工厂命令活动运行 Azure 数据资源管理器控件命令](data-factory-command-activity.md)，并使用[查询命令（](/azure/kusto/management/data-ingestion/ingest-from-query)如`.set-or-replace`）中的任何引入。

#### <a name="create-the-azure-data-explorer-linked-service"></a>创建 Azure 数据资源管理器链接服务

要创建 Azure 数据资源管理器链接服务，请执行以下步骤：

1. 若要使用现有的数据存储连接或指定新的数据存储，请在“目标数据存储”窗格中选择“创建新连接”。********

    ![“目标数据存储”窗格](media/data-factory-load-data/destination-create-connection.png)

1. 在“新建链接服务”窗格中选择“Azure 数据资源管理器”，然后选择“继续”。************

    ![“新建链接服务”窗格](media/data-factory-load-data/adx-select-new-linked-service.png)

1. 在新**链接服务（Azure 数据资源管理器）** 窗格中，执行以下步骤：

    ![Azure 数据资源管理器的“新建链接服务”窗格](media/data-factory-load-data/adx-new-linked-service.png)

   a. 在“名称”框中，输入 Azure 数据资源管理器链接服务的名称。****

   b. 在 **"科目选择方法**"下，选择以下选项之一： 

    * 选择“从 Azure 订阅”，然后在下拉列表中，选择你的 **Azure 订阅**和**群集**。**** 

        > [!NOTE]
        > “群集”下拉控件只会列出与你的订阅关联的群集。****

    * 选择“手动输入”，然后输入你的**终结点**。****

   c. 在“租户”框中输入租户名称。****

   d. 在“服务主体 ID”框中输入服务主体 ID。****

   e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 选择“服务主体密钥”，然后在“服务主体密钥”框中输入密钥的值。********

   f. 在“数据库”下拉列表中选择数据库名称。**** 或者，选中“编辑”复选框，然后输入数据库名称。****

   g. 若要测试创建的链接服务连接，请选择“测试连接”。**** 如果可以连接到该链接服务，该窗格将显示绿色的勾选标记和“连接成功”消息。****

   h. 选择“完成”以完成链接服务的创建过程。****

    > [!NOTE]
    > Azure 数据工厂使用服务主体来访问 Azure 数据资源管理器服务。 若要创建服务主体，请参阅[创建 Azure Active Directory (Azure AD) 服务主体](/azure-stack/operator/azure-stack-create-service-principals#manage-an-azure-ad-service-principal)。 不要使用 Azure Key Vault 方法。

#### <a name="configure-the-azure-data-explorer-data-connection"></a>配置 Azure 数据资源管理器数据连接

创建链接服务连接后，“目标数据存储”窗格将会打开，创建的连接可供使用。**** 要配置连接，请执行以下步骤：

1. 选择“下一步”。

    ![Azure 数据资源管理器的“目标数据存储”窗格](media/data-factory-load-data/destination-data-store.png)

1. 在“表映射”窗格中设置目标表名称，然后选择“下一步”。********

    ![目标数据集“表映射”窗格](media/data-factory-load-data/destination-dataset-table-mapping.png)

1. 在“列映射”窗格中，将发生以下映射：****

    a. 第一个映射由 Azure 数据工厂根据 [Azure 数据工厂架构映射](/azure/data-factory/copy-activity-schema-and-type-mapping)执行。 请执行以下操作：

    * 设置 Azure 数据工厂目标表的**列映射**。 将显示从源到 Azure 数据工厂目标表的默认映射。

    * 取消选择不需要定义列映射的列。

    b. 将此表格数据引入 Azure 数据资源管理器时，会发生第二个映射。 映射是根据 [CSV 映射规则](/azure/kusto/management/mappings#csv-mapping)执行的。 即使源数据不采用 CSV 格式，Azure 数据工厂也会将数据转换为表格格式。 因此，在此阶段，只有 CSV 映射才是相关的映射。 请执行以下操作：

    * （可选）在“Azure 数据资源管理器(Kusto)接收器属性”下，添加相关的“引入映射名称”，以便可以使用列映射。********

    * 如果未指定“引入映射名称”，将使用“列映射”部分定义的“按名称”映射顺序。********** 如果“按名称”映射失败，Azure 数据资源管理器会尝试“按列位置”顺序（即，默认按位置映射）引入数据。****

    * 选择“下一步”。

    ![目标数据集“列映射”窗格](media/data-factory-load-data/destination-dataset-column-mapping.png)

1. 在 **"设置"** 窗格中，执行以下步骤：

    a. 在“容错设置”下，输入相关设置。****

    b. 在“性能设置”下，“启用暂存”的选项不适用，“高级设置”包括成本考虑因素。************ 如果没有具体的要求，请将这些设置保留原样。

    c. 选择“下一步”。

    ![复制数据工具的“设置”窗格](media/data-factory-load-data/copy-data-settings.png)

1. 在“摘要”窗格中检查设置，然后选择“下一步”。********

    ![复制数据工具的“摘要”窗格](media/data-factory-load-data/copy-data-summary.png)

1. 在“部署已完成”窗格中执行以下操作：****

    a. 若要切换到“监视”选项卡并查看管道的状态（进度、错误和数据流），选择“监视”。********

    b. 若要编辑链接服务、数据集和管道，请选择“编辑管道”。****

    c. 选择“完成”以完成数据复制任务。****

    ![“部署已完成”窗格](media/data-factory-load-data/deployment.png)

## <a name="next-steps"></a>后续步骤

* 了解 Azure 数据工厂中的 [Azure 数据资源管理器连接器](/azure/data-factory/connector-azure-data-explorer)。
* 详细了解如何在[数据工厂 UI](/azure/data-factory/quickstart-create-data-factory-portal) 中编辑链接服务、数据集和管道。
* 了解用于查询数据的 [Azure 数据资源管理器查询](/azure/data-explorer/web-query-data)。
