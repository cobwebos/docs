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
ms.openlocfilehash: 860b1a579d9c8cee6c6e80ae4c4e7fdd7949d5c7
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300597"
---
# <a name="copy-data-to-azure-data-explorer-by-using-azure-data-factory"></a>使用 Azure 数据工厂将数据复制到 Azure 数据资源管理器 

Azure 数据资源管理器是一种快速、完全托管的数据分析服务。 它提供对大量数据进行实时分析，这些数据从多个源（例如应用程序、网站和 IoT 设备）流式传输。 借助 Azure 数据资源管理器，你可以以迭代方式浏览数据，确定模式和异常以改进产品、增强客户体验、监视设备和提高运营。 它可帮助你探索新问题并在几分钟内获取答案。 

Azure 数据工厂是一项完全托管的基于云的数据集成服务。 可以使用它在 Azure 数据资源管理器数据库中填充现有系统中的数据。 它可以帮助您在生成分析解决方案时节省时间。

将数据载入 Azure 数据资源管理器时，数据工厂具有以下优势：

* **轻松安装**：获取一个直观的五步骤向导，无需编写脚本。
* **丰富的数据存储支持**：获取对一组丰富的本地和基于云的数据存储的内置支持。 有关详细列表，请参阅表[支持的数据存储](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats)。
* **安全且合规**：数据通过 HTTPS 或 Azure ExpressRoute 传输。 存在全局服务可确保数据永远不会离开地理边界。
* **高性能**：数据加载速度为 Azure 数据资源管理器的最大每秒 1 gb。 有关详细信息，请参阅[复制活动性能](/azure/data-factory/copy-activity-performance)。

本文介绍如何使用数据工厂复制数据工具将数据从 Amazon 简单存储服务（S3）加载到 Azure 数据资源管理器。 您可以遵循类似的过程从其他数据存储中复制数据，例如：
* [Azure Blob 存储](/azure/data-factory/connector-azure-blob-storage)
* [Azure SQL 数据库](/azure/data-factory/connector-azure-sql-database)
* [Azure SQL 数据仓库](/azure/data-factory/connector-azure-sql-data-warehouse)
* [Google BigQuery](/azure/data-factory/connector-google-bigquery)
* [Oracle](/azure/data-factory/connector-oracle)
* [文件系统](/azure/data-factory/connector-file-system)

## <a name="prerequisites"></a>先决条件

* 如果还没有 Azure 订阅，可以在开始前创建一个[免费 Azure 帐户](https://azure.microsoft.com/free/)。
* [Azure 数据资源管理器群集和数据库](create-cluster-database-portal.md)。
* 数据源。

## <a name="create-a-data-factory"></a>创建数据工厂

1. 登录到 [Azure 门户](https://ms.portal.azure.com)。

1. 在左窗格中，选择 "**创建资源** > **分析** > **数据工厂**"。

   ![在 Azure 门户中创建数据工厂](media/data-factory-load-data/create-adf.png)

1. 在 "**新建数据工厂**" 窗格中，提供下表中的字段值：

   !["新建数据工厂" 窗格](media/data-factory-load-data/my-new-data-factory.png)  

   | 设置  | 要输入的值  |
   |---|---|
   | **名称** | 在 "名称" 框中，输入数据工厂的全局唯一名称。 如果收到错误 "*数据工厂名称\"\" LoadADXDemo 不可用*"，请为数据工厂输入其他名称。 有关数据工厂项目的命名规则，请参阅[数据工厂命名规则](/azure/data-factory/naming-rules)。|
   | **订阅** | 在下拉列表中，选择要在其中创建数据工厂的 Azure 订阅。 |
   | **资源组** | 选择 "**新建**"，然后输入新资源组的名称。 如果已有资源组，请选择 "**使用现有**"。 |
   | **版本** | 在下拉列表中，选择 " **V2**"。 |  
   | **Location** | 在下拉列表中，选择数据工厂的位置。 列表中仅显示支持的位置。 数据工厂使用的数据存储可以位于其他位置或区域。 |

1. 选择“创建”。

1. 若要监视创建过程，请在工具栏上选择 "**通知**"。 创建数据工厂后，将其选中。
   
   此时将打开 "**数据工厂**" 窗格。

   !["数据工厂" 窗格](media/data-factory-load-data/data-factory-home-page.png)

1. 若要在单独的窗格中打开应用程序，请选择 "**创作 & 监视器**" 磁贴。

## <a name="load-data-into-azure-data-explorer"></a>将数据载入 Azure 数据资源管理器

可以从多种类型的[数据存储](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats)将数据加载到 Azure 数据资源管理器中。 本文介绍如何从 Amazon S3 加载数据。

可以通过以下任一方式加载数据：

* 在 Azure 数据工厂用户界面的左窗格中，选择 "**创作**" 图标，如[使用 AZURE 数据工厂 UI 创建数据工厂](/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory)中的 "创建数据工厂" 一节中所示。
* 在 Azure 数据工厂复制数据工具中，如[使用复制数据工具复制数据](/azure/data-factory/quickstart-create-data-factory-copy-data-tool)中所示。

### <a name="copy-data-from-amazon-s3-source"></a>从 Amazon S3 复制数据（源）

1. 在 "**开始**" 窗格中，通过选择 "**复制数据**" 打开复制数据工具。

   !["复制数据工具" 按钮](media/data-factory-load-data/copy-data-tool-tile.png)

1. 在 "**属性**" 窗格中，在 "**任务名称**" 框中输入名称，然后选择 "**下一步**"。

    !["复制数据属性" 窗格](media/data-factory-load-data/copy-from-source.png)

1. 在 "**源数据存储**" 窗格中，选择 "**新建连接**"。

    ![复制数据 "源数据存储" 窗格](media/data-factory-load-data/source-create-connection.png)

1. 选择 " **Amazon S3**"，然后选择 "**继续**"。

    !["新建链接服务" 窗格](media/data-factory-load-data/amazons3-select-new-linked-service.png)

1. 在 "**新建链接服务（Amazon S3）** " 窗格中，执行以下操作：

    ![指定 Amazon S3 链接服务](media/data-factory-load-data/amazons3-new-linked-service-properties.png)

    a. 在 "**名称**" 框中，输入新的链接服务的名称。

    b. 在 "**通过集成运行时连接**" 下拉列表中，选择值。

    c. 在 "**访问密钥 ID** " 框中，输入值。
    
    > [!NOTE]
    > 在 Amazon S3 中，若要查找访问密钥，请在导航栏上选择 Amazon 用户名，然后选择 **"我的安全凭据**"。
    
    d. 在 "**密钥访问密钥**" 框中，输入一个值。

    e. 若要测试创建的链接服务连接，请选择 "**测试连接**"。

    f. 选择“完成”。
    
      "**源数据存储**" 窗格将显示新的 AmazonS31 连接。 

1. 选择“**下一步**”。

   ![源数据存储 - 创建的连接](media/data-factory-load-data/source-data-store-created-connection.png)

1. 在 "**选择输入文件或文件夹**" 窗格中，执行以下操作：

    a. 浏览到要复制的文件或文件夹，然后选择该文件或文件夹。

    b. 选择所需的复制行为。 请确保清除 "**二进制副本**" 复选框。

    c. 选择“**下一步**”。

    ![选择输入文件或文件夹](media/data-factory-load-data/source-choose-input-file.png)

1. 在 "**文件格式设置**" 窗格中，选择文件的相关设置。 然后选择 "**下一步**"。

   !["文件格式设置" 窗格](media/data-factory-load-data/source-file-format-settings.png)

### <a name="copy-data-into-azure-data-explorer-destination"></a>将数据复制到 Azure 数据资源管理器（目标）

创建新的 Azure 数据资源管理器链接服务，以便将数据复制到本部分中指定的 Azure 数据资源管理器目标表（接收器）。

#### <a name="create-the-azure-data-explorer-linked-service"></a>创建 Azure 数据资源管理器链接服务

若要创建 Azure 数据资源管理器链接服务，请执行以下操作：

1. 若要使用现有的数据存储连接或指定新的数据存储，请在 "**目标数据存储**" 窗格中选择 "**创建新连接**"。

    !["目标数据存储" 窗格](media/data-factory-load-data/destination-create-connection.png)

1. 在 "**新建链接服务**" 窗格中，选择 " **Azure 数据资源管理器**"，然后选择 "**继续**"。

    !["新建链接服务" 窗格](media/data-factory-load-data/adx-select-new-linked-service.png)

1. 在 "**新建链接服务（Azure 数据资源管理器）** " 窗格中，执行以下操作：

    !["Azure 数据资源管理器新建链接服务" 窗格](media/data-factory-load-data/adx-new-linked-service.png)

   a. 在 "**名称**" 框中，输入 Azure 数据资源管理器链接服务的名称。

   b. 在 "**帐户选择方法**" 下，执行以下操作之一： 

    * 选择 "**从 Azure 订阅**"，然后在下拉列表中选择**Azure 订阅**和**群集**。 

        > [!NOTE]
        > **群集**下拉控件仅列出与订阅关联的群集。

    * 选择 "**手动输入**"，然后输入**终结点**。

   c. 在 "**租户**" 框中，输入租户名称。

   d. 在 "**服务主体 id** " 框中，输入服务主体 id。

   e. 选择 "**服务主体密钥**"，然后在 "**服务主体密钥**" 框中输入密钥的值。

   f. 在 "**数据库**" 下拉列表中，选择数据库名称。 或者，选中 "**编辑**" 复选框，然后输入数据库名称。

   g. 若要测试创建的链接服务连接，请选择 "**测试连接**"。 如果可以连接到链接服务，则该窗格将显示绿色的复选标记和**连接成功**消息。

   h. 选择 "**完成**" 以完成链接的服务创建。

    > [!NOTE]
    > Azure 数据工厂使用服务主体来访问 Azure 数据资源管理器服务。 若要创建服务主体，请参阅[创建 Azure Active Directory （Azure AD）服务主体](/azure-stack/operator/azure-stack-create-service-principals#manage-an-azure-ad-service-principal)。 不要使用 Azure Key Vault 方法。

#### <a name="configure-the-azure-data-explorer-data-connection"></a>配置 Azure 数据资源管理器数据连接

创建链接服务连接后，将打开 "**目标数据存储**" 窗格，并可使用创建的连接。 若要配置连接，请执行以下操作：

1. 选择“**下一步**”。

    ![Azure 数据资源管理器 "目标数据存储" 窗格](media/data-factory-load-data/destination-data-store.png)

1. 在 "**表映射**" 窗格中，设置目标表名称，然后选择 "**下一步**"。

    ![目标数据集 "表映射" 窗格](media/data-factory-load-data/destination-dataset-table-mapping.png)

1. 在 "**列映射**" 窗格中，进行以下映射：

    a. 根据[Azure 数据工厂架构映射](/azure/data-factory/copy-activity-schema-and-type-mapping)，Azure 数据工厂会执行第一个映射。 请执行以下操作：

    * 设置 Azure 数据工厂目标表的**列映射**。 默认映射显示从源到 Azure 数据工厂目标表。

    * 取消选择不需要定义列映射的列。

    b. 将此表格数据引入 Azure 数据资源管理器时，会发生第二个映射。 映射是根据 [CSV 映射规则](/azure/kusto/management/mappings#csv-mapping)执行的。 即使源数据不是 CSV 格式，Azure 数据工厂也会将数据转换为表格格式。 因此，CSV 映射是在此阶段唯一相关的映射。 请执行以下操作：

    * 可有可无在 " **Azure 数据资源管理器（Kusto）接收器属性**" 下，添加相关的**引入映射名称**，以便可以使用列映射。

    * 如果未指定**引入映射名称**，将使用 "**列映射**" 部分中定义的*按名称*映射顺序。 如果*按名称*映射失败，Azure 数据资源管理器会尝试*按列位置*顺序引入数据（也就是说，它按位置作为默认值进行映射）。

    * 选择“**下一步**”。

    ![目标数据集 "列映射" 窗格](media/data-factory-load-data/destination-dataset-column-mapping.png)

1. 在 "**设置**" 窗格中，执行以下操作：

    a. 在 "**容错设置**" 下，输入相关设置。

    b. 在 "**性能设置**" 下，"**启用过渡**" 不适用，**高级设置**包括成本注意事项。 如果没有特定要求，请将这些设置保留原样。

    c. 选择“**下一步**”。

    !["复制数据" 的 "设置" 窗格](media/data-factory-load-data/copy-data-settings.png)

1. 在 "**摘要**" 窗格中查看设置，然后选择 "**下一步**"。

    ![复制数据 "摘要" 窗格](media/data-factory-load-data/copy-data-summary.png)

1. 在 "**部署完成**" 窗格中，执行以下操作：

    a. 若要切换到 "**监视**" 选项卡并查看管道的状态（即，"进度"、"错误" 和 "数据流"），请选择 "**监视**"。

    b. 若要编辑链接服务、数据集和管道，请选择 "**编辑管道**"。

    c. 选择 "**完成**" 以完成 "复制数据" 任务。

    !["部署完成" 窗格](media/data-factory-load-data/deployment.png)

## <a name="next-steps"></a>后续步骤

* 了解 Azure 数据工厂中的 [Azure 数据资源管理器连接器](/azure/data-factory/connector-azure-data-explorer)。
* 详细了解如何在[数据工厂 UI](/azure/data-factory/quickstart-create-data-factory-portal) 中编辑链接服务、数据集和管道。
* 了解用于查询数据的 [Azure 数据资源管理器查询](/azure/data-explorer/web-query-data)。
