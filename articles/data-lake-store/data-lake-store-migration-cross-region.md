---
title: "跨区域迁移 Azure Data Lake Store 指南 | Microsoft 文档"
description: "跨区域迁移 Azure Data Lake Store 指南"
services: data-lake-store
documentationcenter: 
author: stewu
manager: amitkul
editor: stewu
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/27/2017
ms.author: stewu
translationtype: Human Translation
ms.sourcegitcommit: 3c83a76c589178d5a2a01ed4edd208e42fb2e83e
ms.openlocfilehash: 785f97a28c060f88a0e0f3a5aa9cf6555ed2b5a4
ms.lasthandoff: 02/22/2017


---
# <a name="guidance-to-migrate-azure-data-lake-store-across-regions"></a>跨区域迁移 Azure Data Lake Store 指南

Azure Data Lake Store 在新区域中推出后，用户可以执行一次性的迁移，然后便可利用新的区域。  下面提供了有关在规划和执行迁移时要考虑的因素的一些指导。

## <a name="prerequisites"></a>先决条件

* **一个 Azure 订阅**。 请参阅 [获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
* **在两个不同的区域中提供 Azure Data Lake Store 帐户**。 有关如何创建帐户的说明，请参阅 [Azure Data Lake Store 入门](data-lake-store-get-started-portal.md)。
* **Azure 数据工厂**  有关详细信息，请参阅 [Azure 数据工厂简介](../data-factory/data-factory-introduction.md)。


## <a name="guidance-for-migration"></a>迁移指南

建议首先针对需要在 Azure Data Lake Store 中写入、读取或处理数据的应用程序制定迁移策略。 选择策略时，应考虑应用程序的可用性要求（即停机时间），例如，最简单的方法是“即时转移”数据。  这样就可以暂停旧区域中的应用程序，同时将所有数据复制到新区域。  复制过程完成后，可以恢复新区域中的应用程序，并删除旧的 Azure Data Lake Store 帐户。  但是，执行这种迁移会造成停机。

或者，为了减少停机时间，可以首先直接在新区域中引入新数据，然后在获得所需的少量数据后，立即在新区域中开始运行应用程序。  可以在后台将旧 Azure Data Lake Store 帐户中的较旧数据复制到新区域中的新 Azure Data Lake Store 帐户。  这样，便可以在造成极短停机时间的情况下切换到新区域。  复制所有较旧的数据后，可以删除旧 ADLS 帐户。

规划迁移时要考虑的其他重要详细信息包括：

* **数据量** - 数据量（GB 大小、文件和文件夹数目，等等）会影响迁移所需的时间和资源。

* **Azure Data Lake Store 帐户名称** - 新区域中的新帐户名称需要全局唯一，例如，如果美国东部 2 区中旧存储帐户的名称为 contosoeastus2.azuredatalakestore.net，则可以将欧盟北部中的存储帐户命名为 contosonortheu.azuredatalakestore.net

* **工具选择** - 若要复制 Azure Data Lake Store 文件，我们建议使用 [Azure 数据工厂复制活动](../data-factory/data-factory-azure-datalake-connector.md)。   Azure 数据工厂支持高性能、高可靠性的数据移动。  请记住，Azure 数据工厂只会复制文件夹层次结构和文件内容。 已应用的任何访问控制列表 (ACL) 需要在新帐户中手动应用。  [Azure 数据工厂性能优化指南](../data-factory/data-factory-copy-activity-performance.md)是一份不错的参考资料，其中列出了最佳方案的性能目标。  如果想要在短时间内复制好数据，可能需要使用其他云数据移动单元。  请注意，其他某些工具（例如 ADLCopy）不支持在区域之间复制数据。  

* **[带宽费用](https://azure.microsoft.com/en-us/pricing/details/bandwidth/)**由于要将数据传出 Azure 区域，因此会产生费用。

* **数据 ACL** - 可以通过向文件和文件夹应用 ACL 来保护新区域中的数据。  [此处](data-lake-store-secure-data.md)提供了相关的指导。  我们建议借此次迁移的机会来更新和调整 ACL。  但是，如果想要使用类似的设置，可以通过门户、[PowerShell cmdlet](https://docs.microsoft.com/en-us/powershell/resourcemanager/azurerm.datalakestore/v3.1.0/get-azurermdatalakestoreitempermission) 或 SDK 查看已应用到任何文件的 ACL。  

* **分析服务的位置** - 为了获得最佳性能，应该将 Data Lake Analytics 或 HDInsight 等分析服务放置在数据所在的同一区域。  

## <a name="see-also"></a>另请参阅
* [Overview of Azure Data Lake Store](data-lake-store-overview.md)

