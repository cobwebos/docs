---
title: "Azure Data Lake Store 跨区域迁移 | Microsoft 文档"
description: "了解 Azure Data Lake Store 的跨区域迁移。"
services: data-lake-store
documentationcenter: 
author: swums
manager: amitkul
editor: swums
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/27/2017
ms.author: stewu
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: a0eecbb50b001325c91d1d721bf8de7244f04674
ms.contentlocale: zh-cn
ms.lasthandoff: 04/27/2017

---
# <a name="migrate-data-lake-store-across-regions"></a>跨区域迁移 Data Lake Store

Azure Data Lake Store 在新区域中推出后，用户可以选择执行一次性的迁移，然后便可利用新区域。 了解规划和完成迁移时要注意的事项。

## <a name="prerequisites"></a>先决条件

* **一个 Azure 订阅**。 有关详细信息，请参阅[立即创建免费 Azure 帐户](https://azure.microsoft.com/pricing/free-trial/)。
* **在两个不同的区域中提供 Data Lake Store 帐户**。 有关详细信息，请参阅 [Azure Data Lake Store 入门](data-lake-store-get-started-portal.md)。
* **Azure 数据工厂** 有关详细信息，请参阅 [Azure 数据工厂简介](../data-factory/data-factory-introduction.md)。


## <a name="migration-considerations"></a>迁移注意事项

首先，确定最适合在 Data Lake Store 中写入、读取或处理数据的应用程序的迁移策略。 选择策略时，应考虑应用程序的可用性要求，以及迁移期间的停机时间。 例如，最简单的方法可能是使用“即时转移”云迁移模型。 使用此方法时，可以暂停现有区域中的应用程序，同时将所有数据复制到新区域。 复制过程完成后，可以恢复新区域中的应用程序，然后删除旧的 Data Lake Store 帐户。 迁移过程中需要停机。

为了减少停机时间，可以立即开始在新区域中引入新数据。 获得所需的少量数据后，在新区域中运行应用程序。 在后台可以继续将现有 Data Lake Store 帐户中的旧数据复制到新区域中的新 Data Lake Store 帐户。 使用这种方法可以在造成极短停机时间的情况下切换到新区域。 复制所有旧数据后，可以删除旧的 Data Lake Store 帐户。

规划迁移时要考虑的其他重要详细信息包括：

* **数据量**。 数据量（GB 大小、文件和文件夹数目等等）会影响迁移所需的时间和资源。

* **Data Lake Store 帐户名**。 新区域中的新帐户名必须全局唯一。 例如，如果美国东部 2 区中旧 Data Lake Store 帐户的名称为 contosoeastus2.azuredatalakestore.net， 可将欧盟北部中的新 Data Lake Store 帐户命名为 contosonortheu.azuredatalakestore.net。

* **工具**。 建议使用 [Azure 数据工厂复制活动](../data-factory/data-factory-azure-datalake-connector.md)来复制 Data Lake Store 文件。 数据工厂支持高性能、高可靠性的数据移动。 请记住，数据工厂只会复制文件夹层次结构和文件内容。 需要手动将旧帐户中使用的任何访问控制列表 (ACL) 应用到新帐户。 有关详细信息，包括最佳方案的性能目标，请参阅[复制活动性能和优化指南](../data-factory/data-factory-copy-activity-performance.md)。 如果想要更快地复制数据，可能需要使用其他云数据移动单元。 其他某些工具（例如 AdlCopy）不支持在区域之间复制数据。  

* **带宽费用**。 由于要将数据传出 Azure 区域，因此会产生[带宽费用](https://azure.microsoft.com/en-us/pricing/details/bandwidth/)。

* **数据 ACL**。 可以通过向文件和文件夹应用 ACL 来保护新区域中的数据。 有关详细信息，请参阅 [Securing data stored in Azure Data Lake Store](data-lake-store-secure-data.md)（保护 Azure Data Lake 中存储的数据）。 我们建议通过迁移来更新和调整 ACL。 可以使用类似于当前设置的设置。 可以使用 Azure 门户、[PowerShell cmdlet](/powershell/module/azurerm.datalakestore/get-azurermdatalakestoreitempermission) 或 SDK 查看已应用到任何文件的 ACL。  

* **分析服务的位置** 为了获得最佳性能，应该将 Azure Data Lake Analytics 或 Azure HDInsight 等分析服务放置在数据所在的同一区域。  

## <a name="next-steps"></a>后续步骤
* [Overview of Azure Data Lake Store](data-lake-store-overview.md)

