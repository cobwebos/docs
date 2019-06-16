---
title: 使用 Azure 门户捕获流式处理事件 - Azure事件中心 | Microsoft Docs
description: 本文介绍如何使用 Azure 门户捕获通过 Azure 事件中心流式处理的事件。
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.custom: seodec18
ms.devlang: na
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: shvija
ms.openlocfilehash: 9108c52529319288fba48dbad3c6f8aa6cb5f725
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60822537"
---
# <a name="enable-capturing-of-events-streaming-through-azure-event-hubs"></a>启用捕获通过 Azure 事件中心流式处理的事件

Azure [事件中心捕获][capture-overview]用于自动将事件中心的流式处理数据传递到所选的 [Azure Blob 存储](https://azure.microsoft.com/services/storage/blobs/)或 [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/) 帐户。

可以使用 [Azure 门户](https://portal.azure.com)在创建事件中心时配置捕获。 可以将数据捕获到 Azure [Blob 存储](https://azure.microsoft.com/services/storage/blobs/)容器或 [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/) 帐户。

有关详细信息，请参阅[事件中心捕获概述][capture-overview]。

## <a name="capture-data-to-an-azure-storage-account"></a>将数据捕获到 Azure 存储帐户  

创建事件中心以后，即可在“创建事件中心”门户屏幕中单击“启用”按钮，以便启用捕获。   然后在“捕获提供程序”框中单击“Azure 存储”，指定存储帐户和容器。   由于事件中心捕获对存储使用服务到服务身份验证，因此无需指定存储连接字符串。 资源选取器自动为存储帐户选择资源 URI。 如果使用 Azure 资源管理器，必须以字符串形式显式提供此 URI。

默认时间段为 5 分钟。 最小值为 1，最大值为 15。 **大小**窗口的范围为 10-500 MB。

![捕获的时间范围][1]

> [!NOTE]
> 可以允许或禁止当捕获时间段内未发生任何事件时发出空文件。 

## <a name="capture-data-to-an-azure-data-lake-store-account"></a>将数据捕获到 Azure Data Lake Store 帐户

若要将数据捕获到 Azure Data Lake Store，请创建一个 Data Lake Store 帐户和一个事件中心：

### <a name="create-an-azure-data-lake-store-account-and-folders"></a>创建 Azure Data Lake Store 帐户和文件夹

> [!NOTE]
> 目前，事件中心捕获功能支持仅 Gen 1 的 Azure Data Lake Store，不第 2 代。 

1. 创建 Data Lake Store 代 1 帐户中的说明[Get started with Azure Data Lake Store 使用 Azure 门户入门](../data-lake-store/data-lake-store-get-started-portal.md)。
2. 按照[为事件中心分配权限](../data-lake-store/data-lake-store-archive-eventhub-capture.md#assign-permissions-to-event-hubs)部分中的说明，在 Data Lake Store 帐户中创建一个文件夹（要在其中捕获事件中心的数据），为事件中心分配权限，以便它可以将数据写入到 Data Lake Store 帐户。  


### <a name="create-an-event-hub"></a>创建事件中心

1. 注意，事件中心与刚创建的Azure Data Lake Store 必须属于同一 Azure 订阅。 创建事件中心，在“创建事件中心”门户页中单击“捕获”下的“启用”按钮。    
2. 在“创建事件中心”  门户页的“捕获提供程序”框中选择“Azure Data Lake Store”。  
3. 在“选择 Data Lake Store”中指定此前创建的 Data Lake Store 帐户，并在“Data Lake 路径”字段中输入已创建的数据文件夹的路径。  

    ![选择 Data Lake Storage 帐户][3]

## <a name="add-or-configure-capture-on-an-existing-event-hub"></a>在现有的事件中心添加或配置捕获

可以在事件中心命名空间中的现有事件中心配置捕获。 若要对现有的事件中心启用“捕获”功能，或者要更改“捕获”设置，请单击命名空间以加载概览屏幕，然后单击要启用或更改“捕获”设置的事件中心。 最后，单击已打开页面左侧的“捕获”  选项，然后编辑设置，如以下各图所示：

### <a name="azure-blob-storage"></a>Azure Blob 存储

![配置 Azure Blob 存储][2]

### <a name="azure-data-lake-store"></a>Azure Data Lake Store

![配置 Azure Data Lake Storage][4]

[1]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture1.png
[2]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture2.png
[3]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture3.png
[4]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture4.png

## <a name="next-steps"></a>后续步骤

- 阅读[事件中心捕获概述][capture-overview]，详细了解事件中心捕获。
- 还可以通过 Azure 资源管理器模板配置事件中心捕获。 有关详细信息，请参阅[通过 Azure 资源管理器模板启用捕获](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)。
- [了解如何创建使用事件中心命名空间作为源的 Azure 事件网格订阅](store-captured-data-data-warehouse.md)
- [Azure Data Lake Store 入门（使用 Azure 门户）](../data-lake-store/data-lake-store-get-started-portal.md)

[capture-overview]: event-hubs-capture-overview.md
