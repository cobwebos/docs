---
title: 将 Azure Data Lake Storage Gen2 和其他 Azure 服务集成 | Microsoft Docs
description: 了解 Azure Data Lake Storage Gen2 如何与其他 Azure 服务集成
documentationcenter: ''
services: storage
author: normesta
ms.component: data-lake-storage-gen2
ms.service: storage
ms.devlang: na
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: nitinme
ms.openlocfilehash: 7bc4889403e1d52cfa3b18792a554f0faf605132
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2019
ms.locfileid: "55885504"
---
# <a name="integrate-azure-data-lake-storage-gen2-with-other-azure-services"></a>将 Azure Data Lake Storage Gen2 和其他 Azure 服务集成

可以使用 Azure 服务将数据引入 Azure Data Lake Storage Gen2 存储帐户中，并对其进行分析和可视化操作。 选择最适合你正在尝试完成的任务的服务。

## <a name="ingest-data-into-your-data-lake"></a>将数据引入 Data Lake

可以通过这些服务为 Data Lake 填充数据。

### <a name="azure-data-factory"></a>Azure 数据工厂

可以使用 [Azure 数据工厂](https://azure.microsoft.com/services/data-factory/)从以下源引入数据：

* Azure 表
* Azure SQL 数据库
* Azure SQL 数据仓库
* Azure 存储 Blob
* 本地数据库

请参阅[使用数据工厂将数据移入和移出 Data Lake Storage Gen2](../../data-factory/connector-azure-data-lake-store.md)。

## <a name="analyze-and-visualize-data-in-your-data-lake"></a>分析和可视化 Data Lake 中的数据

这些服务可将 Data Lake Storage Gen2 用作存储终结点。

### <a name="azure-hdinsight"></a>Azure HDInsight

可将 [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/) 群集预配为使用 Data Lake Storage Gen2 作为兼容 HDFS 的存储。 此版本中，对于 Windows 和 Linux 上的 Hadoop 和 Storm 群集，仅可将 Data Lake Storage Gen2 用作额外存储。 这类群集仍使用 Azure 存储 (WASB) 作为默认存储。 但是，对于 Windows 和 Linux 上的 HBase 群集，可使用 Data Lake Storage Gen2 作为默认存储和额外存储。

请参阅[配合使用 Azure Data Lake Storage Gen2 和 Azure HDInsight 群集](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2)

### <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics

可以使用 [Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-overview.md) 处理云规模的大数据。 它能动态地预配资源，并可以百亿亿字节为单位进行数据分析。 Data Lake Analytics 已优化，可以使用 Data Lake Storage Gen2 作为数据源。 

请参阅 [Data Lake Analytics 入门（使用 Data Lake Storage Gen2）](../../data-lake-analytics/data-lake-analytics-get-started-portal.md)。

## <a name="copy-data-to-other-repositories"></a>将数据复制到其他存储库

使用这些服务从 Data Lake 复制数据，然后将其置于其他存储库中。

### <a name="sql-data-warehouse"></a>SQL 数据仓库

可使用 PolyBase 将数据从 Data Lake Storage Gen2 加载到 SQL 数据仓库中。 有关详细信息，请参阅[配合使用 Data Lake Storage Gen2 和 SQL 数据仓库](../../sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store.md)。

## <a name="see-also"></a>另请参阅

* [Azure Data Lake Storage Gen2 概述](data-lake-storage-introduction.md)
* [使用 Azure Data Lake Storage Gen2 满足大数据需求](data-lake-storage-data-scenarios.md)
