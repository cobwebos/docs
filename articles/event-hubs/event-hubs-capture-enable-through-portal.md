---
title: "通过门户启用 Azure 事件中心捕获 | Microsoft Docs"
description: "通过 Azure 门户启用事件中心捕获功能。"
services: event-hubs
documentationcenter: 
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/28/2017
ms.author: sethm
ms.translationtype: HT
ms.sourcegitcommit: 8351217a29af20a10c64feba8ccd015702ff1b4e
ms.openlocfilehash: 5420c1cdefa99ff30320dd757e04aa0cafc792bc
ms.contentlocale: zh-cn
ms.lasthandoff: 08/29/2017

---

# <a name="enable-event-hubs-capture-using-the-azure-portal"></a>通过 Azure 门户启用事件中心捕获

Azure [事件中心捕获][capture-overview]用于自动将事件中心的流式处理数据传递到所选的 [Azure Blob 存储](https://azure.microsoft.com/services/storage/blobs/)或 [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/) 帐户。

可以使用 [Azure 门户](https://portal.azure.com)在创建事件中心时配置捕获。 可以将数据捕获到 Azure [Blob 存储](https://azure.microsoft.com/services/storage/blobs/)容器或 [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/) 帐户。

有关详细信息，请参阅[事件中心捕获概述][capture-overview]。

## <a name="capture-data-to-an-azure-storage-account"></a>将数据捕获到 Azure 存储帐户  

创建事件中心以后，即可在“创建事件中心”门户屏幕中单击“启用”按钮，以便启用捕获。 然后在“捕获提供程序”框中单击“Azure 存储”，指定存储帐户和容器。 由于事件中心捕获对存储使用服务到服务身份验证，因此无需指定存储连接字符串。 资源选取器自动为存储帐户选择资源 URI。 如果使用 Azure Resource Manager，必须以字符串形式显式提供此 URI。

默认时间段为 5 分钟。 最小值为 1，最大值为 15。 **大小**窗口的范围为 10-500 MB。

![][1]

## <a name="capture-data-to-an-azure-data-lake-store-account"></a>将数据捕获到 Azure Data Lake Store 帐户

若要将数据捕获到 Azure Data Lake Store，请创建一个 Data Lake Store 帐户和一个事件中心：

### <a name="create-an-azure-data-lake-store-account-and-folders"></a>创建 Azure Data Lake Store 帐户和文件夹

1. 创建 Data Lake Store 帐户，遵循 [Azure Data Lake Store 入门（使用 Azure 门户）](../data-lake-store/data-lake-store-get-started-portal.md)中的说明。 
2. 在此帐户下创建一个文件夹，遵循[在 Azure Data Lake Store 帐户中创建文件夹](../data-lake-store/data-lake-store-get-started-portal.md#createfolder)部分的说明。
3. 在 Data Lake Store 帐户页中，单击“数据资源管理器”。
4. 单击“访问权限”。
5. 单击“添加”。
6. 在“按名称或电子邮件地址搜素”框中键入“Microsoft.EventHubs”，然后选择此选项。 
7. 此时会显示“权限”选项卡。 设置权限，如下图所示：

    ![][6]

8. 单击 **“确定”**。
9. 现在，请浏览到目标文件夹并单击文件夹名称，以便在根文件夹中创建一个文件夹。
10. 单击“访问权限”。
11. 单击“添加”。
12. 在“按名称或电子邮件地址搜素”框中键入“Microsoft.EventHubs”，然后选择此选项。
13. 此时会再次显示“权限”选项卡。 设置权限，如下图所示：

    ![][5]

### <a name="create-an-event-hub"></a>创建事件中心

1. 注意，事件中心与刚创建的Azure Data Lake Store 必须属于同一 Azure 订阅。 创建事件中心，在“创建事件中心”门户页中单击“捕获”下的“启用”按钮。 
2. 在“创建事件中心”门户页的“捕获提供程序”框中选择“Azure Data Lake Store”。
3. 在“选择 Data Lake Store”中指定此前创建的 Data Lake Store 帐户，并在“Data Lake 路径”字段中输入已创建的数据文件夹的路径。

    ![][3]

## <a name="add-or-configure-capture-on-an-existing-event-hub"></a>在现有的事件中心添加或配置捕获

可以在事件中心命名空间中的现有事件中心配置捕获。 若要对现有的事件中心启用“捕获”功能，或者要更改“捕获”设置，请单击命名空间以加载“概要”屏幕，然后单击要启用或更改“捕获”设置的事件中心。 最后，单击已打开页面的“属性”部分，然后编辑“捕获”设置，如下图所示：

### <a name="azure-blob-storage"></a>Azure Blob 存储

![][2]

### <a name="azure-data-lake-store"></a>Azure Data Lake Store

![][4]

[1]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture1.png
[2]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture2.png
[3]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture3.png
[4]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture4.png
[5]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture5.png
[6]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture6.png

## <a name="next-steps"></a>后续步骤

- 阅读[事件中心捕获概述][capture-overview]，详细了解事件中心捕获。
- 还可以通过 Azure Resource Manager 模板配置事件中心捕获。 有关详细信息，请参阅[通过 Azure Resource Manager 模板启用捕获](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)。

[capture-overview]: event-hubs-capture-overview.md
