---
title: 将数据从 Azure 数据工厂复制到 Azure 数据资源管理器
description: 本主题介绍如何使用 Azure 数据工厂复制工具将数据引入（加载）到 Azure 数据资源管理器中
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: b3bd9b800da4f096639d02c78b718216441621a9
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/08/2019
ms.locfileid: "70803983"
---
# <a name="copy-data-to-azure-data-explorer-using-azure-data-factory"></a>使用 Azure 数据工厂将数据复制到 Azure 数据资源管理器 

Azure 数据资源管理器是一个快速、完全托管的数据分析服务，用于实时分析从应用程序、网站和 IoT 设备等许多源流式传输的大量数据。 用户可在其中以迭代方式浏览数据，识别模式和异常以改进产品、增强客户体验、监视设备，以及提升操作性能。 他们可以探讨新的问题，并在短时间内获得解答。 Azure 数据工厂是一个完全托管的基于云的数据集成服务。 可以使用该服务在 Azure 数据资源管理器数据库中填充现有系统中的数据，并节点生成分析解决方案的时间。

在将数据载入 Azure 数据资源管理器方面，Azure 数据工厂的优势如下：

* **轻松设置**：使用直观五步骤向导，无需编写脚本。
* **丰富的数据存储支持**：对一组丰富的本地和基于云的数据存储的内置支持。 有关详细列表，请参阅表[支持的数据存储](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats)。
* **安全且合规**：通过 HTTPS 或 ExpressRoute 传输数据。 存在全局服务可确保数据永远不会离开地理边界。
* **高性能**：向 Azure 数据资源管理器载入数据的速度高达 1-GB/秒。 有关详细信息，请参阅[复制活动性能](/azure/data-factory/copy-activity-performance)。

本文介绍如何使用数据工厂复制数据工具，将数据从 Amazon S3 载入 Azure 数据资源管理器。 可以遵循类似的步骤，从 [Azure Blob 存储](/azure/data-factory/connector-azure-blob-storage)、[Azure SQL 数据库](/azure/data-factory/connector-azure-sql-database)、[Azure SQL 数据仓库](/azure/data-factory/connector-azure-sql-data-warehouse)、[Google BigQuery](/azure/data-factory/connector-google-bigquery)、[Oracle](/azure/data-factory/connector-oracle) 和[文件系统](/azure/data-factory/connector-file-system)等其他数据存储复制数据。

## <a name="prerequisites"></a>先决条件

* 如果还没有 Azure 订阅，可以在开始前创建一个[免费 Azure 帐户](https://azure.microsoft.com/free/)。
* [Azure 数据资源管理器群集和数据库](create-cluster-database-portal.md)
* 数据源。

## <a name="create-a-data-factory"></a>创建数据工厂

1. 选择门户左上角的“创建资源”按钮 (+)，然后选择“分析” > “数据工厂”。

   ![新建数据工厂](media/data-factory-load-data/create-adf.png)

1. 在“新建数据工厂”页中，提供以下字段的值并选择“创建”。

    ![“新建数据工厂”页](media/data-factory-load-data/my-new-data-factory.png)

    **设置**  | **字段说明**
    |---|---|
    | **名称** | 输入数据工厂的全局唯一名称。 如果收到错误“数据工厂名称 \"LoadADXDemo\" 不可用”，请输入不同的数据工厂名称。 有关数据工厂项目的命名规则，请参阅[数据工厂命名规则](/azure/data-factory/naming-rules)。|
    | **订阅** | 选择要在其中创建数据工厂的 Azure 订阅。 |
    | **资源组** | 选择“新建”，并输入新资源组的名称。 如果你有现有的资源组，请选择“使用现有项”。 |
    | **版本** | 选择“V2” |
    | **Location** | 选择数据工厂的位置。 下拉列表中仅显示支持的位置。 数据工厂使用的数据存储可位于其他位置或区域中。 |
    | | |

1. 在工具栏上选择“通知”以监视创建过程。 创建操作完成后，转到创建的数据工厂。 此时会打开“数据工厂”主页。

   ![数据工厂主页](media/data-factory-load-data/data-factory-home-page.png)

1. 选择“创作和监视”磁贴，在单独的选项卡中启动应用程序。

## <a name="load-data-into-azure-data-explorer"></a>将数据载入 Azure 数据资源管理器

可将许多类型的[数据存储](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats)中的数据载入 Azure 数据资源管理器。 本主题详细介绍如何从 Amazon S3 加载数据。

可以使用 Azure 数据工厂以两种方式将数据载入 Azure 数据资源管理器：

* Azure 数据工厂用户界面 -[“创作”选项卡](/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory)。
* 本文中使用的 [Azure 数据工厂“复制数据”工具](/azure/data-factory/quickstart-create-data-factory-copy-data-tool)。

### <a name="copy-data-from-amazon-s3-source"></a>从 Amazon S3（源）复制数据

1. 在“开始使用”页中选择“复制数据”磁贴，启动“复制数据”工具。

   ![“复制数据”工具磁贴](media/data-factory-load-data/copy-data-tool-tile.png)

1. 在“属性”页中指定**任务名称**，然后选择“下一步”。

    ![从源属性复制](media/data-factory-load-data/copy-from-source.png)

1. 在“源数据存储”页中，单击“+ 创建新连接”。

    ![源数据 - 创建连接](media/data-factory-load-data/source-create-connection.png)

1. 依次选择“Amazon S3”、“继续”

    ![新建链接服务](media/data-factory-load-data/amazons3-select-new-linked-service.png)

1. 在“新建链接服务(Amazon S3)”页中执行以下步骤：

    ![指定 Amazon S3 链接服务](media/data-factory-load-data/amazons3-new-linked-service-properties.png)

    * 指定新链接服务的**名称**。
    * 从下拉列表中选择“通过集成运行时进行连接”值。
    * 指定“访问密钥 ID”值。
    * 指定“机密访问密钥”值。
    * 选择“测试连接”以测试创建的链接服务连接。
    * 选择“完成”。

1. 在“源数据存储”页中，可以看到新的 AmazonS31 连接。 选择“**下一步**”。

   ![源数据存储 - 创建的连接](media/data-factory-load-data/source-data-store-created-connection.png)

1. 在“选择输入文件或文件夹”页中：

    1. 浏览到要复制的文件夹/文件。 选择文件夹/文件。
    1. 根据需要选择复制行为。 将“二进制副本”保持未选中状态。
    1. 选择“**下一步**”。

    ![选择输入文件或文件夹](media/data-factory-load-data/source-choose-input-file.png)

1. 在“文件格式设置”页中选择文件的相关设置，然后单击“下一步”。

   ![选择输入文件或文件夹](media/data-factory-load-data/source-file-format-settings.png)

### <a name="copy-data-into-azure-data-explorer-destination"></a>将数据复制到 Azure 数据资源管理器（目标）

现已创建新的 Azure 数据资源管理器链接服务，用于将数据复制到下面指定的 Azure 数据资源管理器目标表（接收器）。

#### <a name="create-the-azure-data-explorer-linked-service"></a>创建 Azure 数据资源管理器链接服务

1. 在“目标数据存储”页中，可以使用现有的数据存储连接，或者单击“+ 创建新连接”指定新的数据存储。

    ![“目标数据存储”页](media/data-factory-load-data/destination-create-connection.png)

1. 在“新建链接服务”页中选择“Azure 数据资源管理器”，然后选择“继续”。

    ![选择 Azure 数据资源管理器 - 新建链接服务](media/data-factory-load-data/adx-select-new-linked-service.png)

1. 在“新建链接服务(Azure 数据资源管理器)”页中执行以下步骤：

    ![ADX - 新建链接服务](media/data-factory-load-data/adx-new-linked-service.png)

   * 选择 Azure 数据资源管理器链接服务的**名称**。
   * 在“帐户选择方法”中： 
        * 选中“从 Azure 订阅”单选按钮，并选择你的 **Azure 订阅**帐户。 然后选择你的**群集**。 请注意，下拉列表中只会列出属于该用户的群集。
        * 或者，选中“手动输入”单选按钮并输入你的**终结点**。
    * 指定**租户**。
    * 输入**服务主体 ID**。
    * 选择“服务主体密钥”按钮并输入**服务主体密钥**。
    * 从下拉菜单中选择你的**数据库**。 或者，选中“编辑”复选框并输入你的数据库名称。
    * 选择“测试连接”以测试创建的链接服务连接。 如果可以连接到设置，会出现绿色的勾选标记“连接成功”。
    * 选择“完成”以完成链接服务的创建过程。

    > [!NOTE]
    > Azure 数据工厂使用服务主体来访问 Azure 数据资源管理器服务。 对于服务主体，请[创建 Azure Active Directory (Azure AD) 服务主体](/azure-stack/operator/azure-stack-create-service-principals#manage-an-azure-ad-service-principal)。 不要使用“Azure Key Vault”方法。

#### <a name="configure-the-azure-data-explorer-data-connection"></a>配置 Azure 数据资源管理器数据连接

1. 此时会打开“目标数据存储”。 创建的 Azure 数据资源管理器数据连接现在可供使用。 选择“下一步”以配置连接。

    ![ADX - 目标数据存储](media/data-factory-load-data/destination-data-store.png)

1. 在“表映射”中，设置目标表名称并选择“下一步”。

    ![目标数据集表映射](media/data-factory-load-data/destination-dataset-table-mapping.png)

1. 在“列映射”页中：
    * 第一个映射由 ADF 根据 [ADF 架构映射](/azure/data-factory/copy-activity-schema-and-type-mapping)执行
        * 设置 Azure 数据工厂目标表的**列映射**。 将显示从源到 ADF 目标表的默认映射。
        * 取消选择不需要定义列映射的列。
    * 将此表格数据引入 Azure 数据资源管理器时，会发生第二个映射。 映射是根据 [CSV 映射规则](/azure/kusto/management/mappings#csv-mapping)执行的。 请注意，即使源数据不采用 CSV 格式，ADF 也会将数据转换为表格格式，因此，在此阶段，只有 CSV 映射才是相关的映射。
        * 在“Azure 数据资源管理器(Kusto)接收器属性”下，添加相关的“引入映射名称”（可选），以便可以使用列映射。
        * 如果未指定“引入映射名称”，将采用“列映射”部分定义的“按名称”映射顺序。 如果“按名称”映射失败，Azure 数据资源管理器会尝试“按列位置”顺序（默认按位置映射）引入数据。
    * 选择“下一步”

    ![目标数据集列映射](media/data-factory-load-data/destination-dataset-column-mapping.png)

1. 在“设置”页中：
    * 设置相关的**容错设置**。
    * **性能设置**：启用暂存的选项不适用。 “高级设置”包括成本考虑因素。 如果没有具体的需求，请将它保留原样。
    * 选择“**下一步**”。

    ![复制数据 - 设置](media/data-factory-load-data/copy-data-settings.png)

1. 在“摘要”中检查设置，然后选择“下一步”。

    ![复制数据 - 摘要](media/data-factory-load-data/copy-data-summary.png)

1. 在“部署”页中：
    * 选择“监视”切换到“监视”选项卡，并查看管道的状态（进度、错误和数据流）。
    * 选择“编辑管道”以编辑链接服务、数据集和管道。
    * 选择“完成”以完成数据复制任务。

    ![“部署”页](media/data-factory-load-data/deployment.png)

## <a name="next-steps"></a>后续步骤

* 了解 Azure 数据工厂中的 [Azure 数据资源管理器连接器](/azure/data-factory/connector-azure-data-explorer)。

* 详细了解如何在[数据工厂 UI](/azure/data-factory/quickstart-create-data-factory-portal) 中编辑链接服务、数据集和管道。

* 了解用于查询数据的 [Azure 数据资源管理器查询](/azure/data-explorer/web-query-data)。
