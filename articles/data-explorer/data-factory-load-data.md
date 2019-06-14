---
title: 将数据从 Azure 数据工厂复制到 Azure 数据资源管理器
description: 在本主题中，您学习如何将 （加载） 数据引入到 Azure 数据资源管理器中，通过使用 Azure 数据工厂复制工具
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 64856d53168a7676cf279da2d8675ce81e1985f7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60447724"
---
# <a name="copy-data-to-azure-data-explorer-using-azure-data-factory"></a>将数据复制到 Azure 数据资源管理器使用 Azure 数据工厂 

Azure 数据资源管理器是对很多来源，如应用程序、 网站和 IoT 设备流式处理的数据量很大的实时分析的快速、 完全托管的数据分析服务。 以迭代方式浏览数据并标识模式和异常情况以改进产品，增强客户体验，监视设备，并提升操作。 探索新问题并在几分钟内获得答案。 Azure 数据工厂是一个完全托管的基于云的数据集成服务。 该服务可用于在 Azure 数据资源管理器用数据填充数据库从现有系统和节省时间时生成分析解决方案。

Azure 数据工厂提供了用于将数据加载到 Azure 数据资源管理器的以下好处：

* **轻松设置**：无需脚本直观五步向导。
* **丰富的数据存储支持**:对一组丰富的本地和基于云的数据存储的内置支持。 有关详细列表，请参阅表[支持的数据存储](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats)。
* **安全性和符合性**:通过 HTTPS 或 ExpressRoute 传输数据。 存在全局服务可确保数据永远不会离开地理边界。
* **高性能**:最多 1 GB/s 数据加载到 Azure 数据资源管理器的速度。 有关详细信息，请参阅[复制活动性能](/azure/data-factory/copy-activity-performance)。

本文介绍如何使用数据工厂复制数据工具将数据从 Amazon S3 加载到 Azure 数据资源管理器。 可以遵循类似的步骤来将数据复制从其他数据存储，例如[Azure Blob 存储](/azure/data-factory/connector-azure-blob-storage)， [Azure SQL 数据库](/azure/data-factory/connector-azure-sql-database)， [Azure SQL 数据仓库](/azure/data-factory/connector-azure-sql-data-warehouse)， [GoogleBigQuery](/azure/data-factory/connector-google-bigquery)，[Oracle](/azure/data-factory/connector-oracle)，和[文件系统](/azure/data-factory/connector-file-system)。

## <a name="prerequisites"></a>必备组件

* 如果还没有 Azure 订阅，可以在开始前创建一个[免费 Azure 帐户](https://azure.microsoft.com/free/)。
* [Azure 数据资源管理器群集和数据库](create-cluster-database-portal.md)
* 数据源。

## <a name="create-a-data-factory"></a>创建数据工厂

1. 选择**创建资源**门户的左上角的按钮 （+） > **Analytics** > **数据工厂**。

   ![新建数据工厂](media/data-factory-load-data/create-adf.png)

1. 在中**新建数据工厂**页上，为以下字段提供值，然后选择**创建**。

    ![“新建数据工厂”页](media/data-factory-load-data/my-new-data-factory.png)

    **设置**  | **字段说明**
    |---|---|
    | **名称** | 输入你的数据工厂的全局唯一名称。 如果收到错误 *"数据工厂名称\"LoadADXDemo\"不可用"* ，输入不同的数据工厂的名称。 有关数据工厂项目命名规则，请参阅[数据工厂命名规则](/azure/data-factory/naming-rules)。|
    | **订阅** | 选择要在其中创建数据工厂的 Azure 订阅。 |
    | **资源组** | 选择**新建**并输入新的资源组的名称。 选择**使用现有**，如果有现有的资源组。 |
    | **版本** | 选择“V2”  |
    | **位置** | 选择数据工厂的位置。 下拉列表中仅显示支持的位置。 数据工厂使用的数据存储可以在其他位置或区域中。 |
    | | |

1. 在工具栏上，监视创建过程中选择通知。 创建操作完成后，转到你创建的数据工厂。 **数据工厂**主页打开。

   ![数据工厂主页](media/data-factory-load-data/data-factory-home-page.png)

1. 选择**创作和监视**磁贴，启动单独的选项卡中的应用程序。

## <a name="load-data-into-azure-data-explorer"></a>将数据加载到 Azure 数据资源管理器

可以从许多类型的加载数据[数据存储](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats)到 Azure 数据资源管理器。 本主题详细介绍了从 Amazon S3 加载数据。

有两种方法将数据加载到 Azure 数据资源管理器使用 Azure 数据工厂：

* Azure 数据工厂用户界面- [**作者**选项卡](/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory)
* [Azure 数据工厂**复制数据**工具](/azure/data-factory/quickstart-create-data-factory-copy-data-tool)本文中使用。

### <a name="copy-data-from-amazon-s3-source"></a>将数据从 Amazon S3 复制 （源）

1. 在中**让我们开始吧**页上，选择**复制数据**磁贴以启动复制数据工具。

   ![复制数据工具磁贴](media/data-factory-load-data/copy-data-tool-tile.png)

1. 在中**属性**页上，指定**任务名称**，然后选择**下一步**。

    ![将源属性从复制](media/data-factory-load-data/copy-from-source.png)

1. 在中**源数据存储区**页上，单击 **+ 创建新的连接**。

    ![源数据创建的连接](media/data-factory-load-data/source-create-connection.png)

1. 选择**Amazon S3**，然后选择**继续**

    ![新建链接服务](media/data-factory-load-data/amazons3-select-new-linked-service.png)

1. 在中**新建链接服务 (Amazon S3)** 页上，执行以下步骤：

    ![指定 Amazon S3 链接服务](media/data-factory-load-data/amazons3-new-linked-service-properties.png)

    * 指定**名称**将新的链接服务。
    * 选择**通过集成运行时连接**从下拉列表中的值。
    * 指定“访问密钥 ID”  值。
    * 指定“机密访问密钥”  值。
    * 选择**测试连接**来测试您创建的链接的服务连接。
    * 选择“完成”。 

1. 在中**源数据存储区**页上，你将看到新 AmazonS31 连接。 选择“**下一步**”。

   ![源数据存储创建的连接](media/data-factory-load-data/source-data-store-created-connection.png)

1. 在中**选择输入的文件或文件夹**页：

    1. 浏览到想要复制的文件夹/文件。 选择的文件夹/文件。
    1. 根据需要选择复制行为。 保持**二进制副本**未选中状态。
    1. 选择“**下一步**”。

    ![选择输入文件或文件夹](media/data-factory-load-data/source-choose-input-file.png)

1. 在中**文件格式设置**页上，选择文件并单击相关的设置**下一步**。

   ![选择输入文件或文件夹](media/data-factory-load-data/source-file-format-settings.png)

### <a name="copy-data-into-azure-data-explorer-destination"></a>将数据复制到 Azure 数据资源管理器 （目标）

Azure 数据资源管理器中新建链接的服务创建要复制到 Azure 数据资源管理器目标表 （接收器） 下面指定的数据。

1. 在中**目标数据存储区**页上，可以使用现有数据存储连接或通过单击指定新的数据存储区 **+ 创建新的连接**。

    ![“目标数据存储”页](media/data-factory-load-data/destination-create-connection.png)

1. 在中**新建链接服务**页上，选择**Azure 数据资源管理器**，然后选择**继续**

    ![选择 Azure 数据资源管理器-新建链接服务](media/data-factory-load-data/adx-select-new-linked-service.png)

1. 在中**新建链接服务 （Azure 数据资源管理器）** 页上，执行以下步骤：

    ![ADX 新建链接的服务](media/data-factory-load-data/adx-new-linked-service.png)

   * 选择**名称**链接服务的 Azure 数据资源管理器。
   * 在中**帐户选择方法**: 
        * 选择**从 Azure 订阅**单选按钮，然后选择你**Azure 订阅**帐户。 然后，选择你**群集**。 请注意下拉列表将仅列出属于用户的群集。
        * 或者，选择**手动输入**单选按钮并输入你**终结点**。
    * 指定**租户**。
    * 输入**服务主体 ID**。
    * 选择**服务主体键**按钮，然后输入**服务主体键**。
    * 选择你**数据库**从下拉菜单。 或者，选择**编辑**复选框并输入你的数据库名称。
    * 选择**测试连接**来测试您创建的链接的服务连接。 如果您可以连接到您的设置，绿色复选标记**连接成功**将出现。
    * 选择**完成**以完成创建链接的服务。

    > [!NOTE]
    > Azure 数据工厂使用的服务主体来访问 Azure 数据资源管理器服务。 为服务主体[创建服务主体的 Azure Active Directory (Azure AD)](/azure/azure-stack/azure-stack-create-service-principals#manage-service-principal-for-azure-ad)。 不要使用**Azure 密钥保管库**方法。

1. **目标数据存储**随即打开。 您创建的 Azure 数据资源管理器数据连接是可供使用。 选择**下一步**来配置连接。

    ![ADX 目标数据存储](media/data-factory-load-data/destination-data-store.png)

1. 在中**表映射**，请设置目标表名称，然后选择**下一步**。

    ![目标数据集表映射](media/data-factory-load-data/destination-dataset-table-mapping.png)

1. 在中**列映射**页：
    * 第一个映射执行 adf 根据[ADF 架构映射](/azure/data-factory/copy-activity-schema-and-type-mapping)
        * 设置**列映射**Azure 数据工厂目标表。 默认映射从源显示到 ADF 目标表。
        * 取消选择不需要定义列映射的列。
    * 第二个映射发生在此表格的数据引入到 Azure 数据资源管理器。 根据执行映射[CSV 映射规则](/azure/kusto/management/mappings#csv-mapping)。 请注意，即使源数据不是 CSV 格式，ADF 将数据转换为表格格式，因此，CSV 映射是在此阶段仅相关映射。
        * 下**Azure 数据资源管理器 (Kusto) 接收器属性**添加相关**引入映射名称**（可选） 因此，列中可使用映射。
        * 如果**引入映射名称**未指定，在定义"按名称"映射顺序**列映射**部分将发生。 如果"按名称"映射会失败，Azure 数据资源管理器将尝试引入"的列位置"order （映射的位置为默认值） 中的数据。
    * 选择“下一步” 

    ![目标数据集列映射](media/data-factory-load-data/destination-dataset-column-mapping.png)

1. 在“设置”页中： 
    * 设置的相关**容错容差设置**。
    * **性能设置**:启用暂存并不适用。 **高级设置**包括成本的考虑。 如果没有特定于所需保留为是。
    * 选择“**下一步**”。

    ![将数据设置复制](media/data-factory-load-data/copy-data-settings.png)

1. 在中**摘要**，查看设置，然后选择**下一步**。

    ![复制摘要数据](media/data-factory-load-data/copy-data-summary.png)

1. 在中**部署页**:
    * 选择**监视器**若要切换到**监视器**选项卡，请参阅管道 （进度、 错误和数据的流） 的状态。
    * 选择**编辑管道**编辑链接的服务、 数据集和管道。
    * 选择**完成**到完整的副本数据任务

    ![“部署”页](media/data-factory-load-data/deployment.png)

## <a name="next-steps"></a>后续步骤

* 了解如何[Azure 数据资源管理器连接器](/azure/data-factory/connector-azure-data-explorer)Azure 数据工厂中。

* 详细了解如何编辑链接的服务、 数据集、 和中的管道[数据工厂 UI](/azure/data-factory/quickstart-create-data-factory-portal)。

* 了解如何[Azure 数据资源管理器查询](/azure/data-explorer/web-query-data)用于数据查询。
