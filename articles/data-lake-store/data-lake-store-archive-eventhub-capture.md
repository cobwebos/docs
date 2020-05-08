---
title: 将事件中心的数据捕获到 Azure Data Lake Storage Gen1
description: 使用 Azure Data Lake Storage Gen1 来捕获 Azure 事件中心收到的数据。
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 5cbcdc5cb9713432be5b52898d956506d80d6ea0
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82690668"
---
# <a name="use-azure-data-lake-storage-gen1-to-capture-data-from-event-hubs"></a>使用 Azure Data Lake Storage Gen1 捕获事件中心的数据

了解如何使用 Azure Data Lake Storage Gen1 捕获 Azure 事件中心收到的数据。

## <a name="prerequisites"></a>先决条件

* **一个 Azure 订阅**。 请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。

* **Azure Data Lake Storage Gen1 帐户**。 有关如何创建帐户的说明，请参阅 [Azure Data Lake Storage Gen1 入门](data-lake-store-get-started-portal.md)。

*  **一个事件中心命名空间**。 有关说明，请参阅[创建事件中心命名空间](../event-hubs/event-hubs-create.md#create-an-event-hubs-namespace)。 确保 Data Lake Storage Gen1 帐户和事件中心命名空间位于同一 Azure 订阅中。


## <a name="assign-permissions-to-event-hubs"></a>向事件中心分配权限

本部分在要在其中捕获事件中心的数据的帐户内创建文件夹。 还将向事件中心分配权限，以便其可将数据写入 Data Lake Storage Gen1 帐户。 

1. 打开要在其中捕获事件中心的数据的 Data Lake Storage Gen1 帐户，然后单击“数据资源管理器”****。

    ![Data Lake Storage Gen1 数据资源管理器](./media/data-lake-store-archive-eventhub-capture/data-lake-store-open-data-explorer.png "Data Lake Storage Gen1 数据资源管理器")

1.  单击“新建文件夹”，然后为要在其中捕获数据的文件夹输入名称****。

    ![在 Data Lake Storage Gen1 中创建新文件夹](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-new-folder.png "在 Data Lake Storage Gen1 中创建新文件夹")

1. 分配 Data Lake Storage Gen1 根目录的权限。 

    a. 单击“数据资源管理器”，选择 Data Lake Storage Gen1 帐户的根目录，然后单击“访问”********。

    ![为 Data Lake Storage Gen1 根分配权限](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-root.png "为 Data Lake Storage Gen1 根分配权限")

    b. 在“访问”下，单击“添加”，单击“选择用户或组”，然后搜索 `Microsoft.EventHubs`************。 

    ![为 Data Lake Storage Gen1 根分配权限](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "为 Data Lake Storage Gen1 根分配权限")
    
    单击“选择”  。

    c. 在“分配权限”下，单击“选择权限”********。 将“权限”设为“执行”********。 将“添加到”设为“此文件夹和所有子文件夹”********。 将“添加为”设为“一个访问权限入口和一个默认权限入口”********。

    > [!IMPORTANT]
    > 创建用于捕获 Azure 事件中心接收的数据的新文件夹层次结构时，这是确保可以访问目标文件夹的简便方法。  但是，如果顶层文件夹具有许多子文件和文件夹，则添加对顶层文件夹的所有子代的权限时，可能需要花费很长时间。  如果根文件夹包含大量文件和文件夹，则分别对最终目标文件夹的路径中的每个文件夹添加对 `Microsoft.EventHubs` 的**执行**权限可能更快。 

    ![为 Data Lake Storage Gen1 根分配权限](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp1.png "为 Data Lake Storage Gen1 根分配权限")

    单击 **“确定”** 。

1. 为 Data Lake Storage Gen1 帐户下要在其中捕获数据的文件夹分配权限。

    a. 单击“数据资源管理器”，选择 Data Lake Storage Gen1 帐户中的文件夹，然后单击“访问”********。

    ![分配 Data Lake Storage Gen1 文件夹的权限](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-folder.png "分配 Data Lake Storage Gen1 文件夹的权限")

    b. 在“访问”下，单击“添加”，单击“选择用户或组”，然后搜索 `Microsoft.EventHubs`************。 

    ![分配 Data Lake Storage Gen1 文件夹的权限](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "分配 Data Lake Storage Gen1 文件夹的权限")
    
    单击“选择”  。

    c. 在“分配权限”下，单击“选择权限”********。 将“权限”设为“读取”、“写入”和“执行”************。 将“添加到”设为“此文件夹和所有子文件夹”********。 最后，将“添加为”设为“一个访问权限入口和一个默认权限入口”********。

    ![分配 Data Lake Storage Gen1 文件夹的权限](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp-folder.png "分配 Data Lake Storage Gen1 文件夹的权限")
    
    单击 **“确定”** 。 

## <a name="configure-event-hubs-to-capture-data-to-data-lake-storage-gen1"></a>配置事件中心，将数据捕获到 Data Lake Storage Gen1

本部分在事件中心命名空间内创建事件中心。 还可配置事件中心，将数据捕获到 Azure Data Lake Storage Gen1 帐户。 本部分假设已创建事件中心命名空间。

1. 在事件中心命名空间的“概述”窗格中，单击“+ 事件中心”********。

    ![创建事件中心](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-event-hub.png "创建事件中心")

1. 提供以下值以配置事件中心，将数据捕获到 Data Lake Storage Gen1。

    ![创建事件中心](./media/data-lake-store-archive-eventhub-capture/data-lake-store-configure-eventhub.png "创建事件中心")

    a. 为事件中心提供一个名称。
    
    b. 本教程将“分区计数”和“消息保留期”均设为默认值********。
    
    c. 将“捕获”设为“开”********。 设置“时间窗口”（捕获频率）和“大小窗口”（捕获的数据大小）********。 
    
    d. 对于“捕获提供程序”，选择“Azure Data Lake Store”，然后选择之前创建的 Data Lake Storage Gen1 帐户********。 对于“Data Lake 路径”，输入之前在 Data Lake Storage Gen1 帐户中创建的文件夹的名称****。 只需提供文件夹的相对路径。

    e. 将“示例捕获文件名格式”保留为默认值****。 此选项用于管理在捕获文件夹下创建的文件夹结构。

    f. 单击 **“创建”** 。

## <a name="test-the-setup"></a>测试设置

现可通过向 Azure 事件中心发送数据来测试解决方案。 按照[将事件发送到 Azure 事件中心](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md)中的说明进行操作。 开始发送数据后，可看到数据使用指定的文件夹结构反映在 Data Lake Storage Gen1 中。 例如，在 Data Lake Storage Gen1 帐户中，文件夹结构如以下屏幕截图所示。

![Data Lake Storage Gen1 中的 EventHub 数据示例](./media/data-lake-store-archive-eventhub-capture/data-lake-store-eventhub-data-sample.png "Data Lake Storage Gen1 中的 EventHub 数据示例")

> [!NOTE]
> 即使没有消息进入事件中心，事件中心也会将仅含标题的空文件写入 Data Lake Storage Gen1 帐户。 写入文件的时间间隔与创建事件中心时提供的时间间隔相同。
> 
>

## <a name="analyze-data-in-data-lake-storage-gen1"></a>分析 Data Lake Storage Gen1 中的数据

数据位于 Data Lake Storage Gen1 中后，可运行分析作业来处理数据。 请参阅 [USQL Avro Example](https://github.com/Azure/usql/tree/master/Examples/AvroExamples)（USQL Avro 示例）了解如何使用 Azure Data Lake Analytics 执行此操作。
  

## <a name="see-also"></a>另请参阅
* [保护 Data Lake Storage Gen1 中的数据](data-lake-store-secure-data.md)
* [将数据从 Azure 存储 Blob 复制到 Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
